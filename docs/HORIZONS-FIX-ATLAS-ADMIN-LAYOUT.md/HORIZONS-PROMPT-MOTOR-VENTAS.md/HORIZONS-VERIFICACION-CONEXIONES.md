# HORIZONS — VERIFICACIÓN DE CONEXIONES: Pipeline Completo Motor de Ventas

## MAPA DEL FLUJO COMPLETO

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     PIPELINE MOTOR DE VENTAS ALIUN                     │
│                                                                         │
│  ┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────────┐  │
│  │  ADMIN   │────▶│   n8n    │────▶│ SUPABASE │────▶│  WEB PÚBLICA │  │
│  │  Atlas   │     │ Backend  │     │   BD     │     │  /ofertas    │  │
│  └──────────┘     └──────────┘     └──────────┘     └──────────────┘  │
│       │                                  │                   ▲         │
│       │                                  │                   │         │
│       └──────────── CRUD directo ────────┘───── RPC ─────────┘         │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## CONEXIÓN 1: ADMIN ATLAS → SUPABASE (CRUD directo)

### Qué hace:
El admin crea/edita/elimina ofertas directamente en Supabase desde el panel `/atlas-admin`.

### Endpoint:
```
NEXT_PUBLIC_SUPABASE_URL = https://oyihiyivdhfxpyiwnmqk.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im95aWhpeWl2ZGhmeHB5aXdubXFrIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NjI0Mzk5NzUsImV4cCI6MjA3ODAxNTk3NX0.8jbifKF9FCExFN3PF1OeUFDVRoHyf652vMHpIgR1DSE
```

### Operaciones que el Admin ejecuta:

#### A) Crear oferta manual (MarketingOfferForm → marketing_offers + offer_date_ranges)
```javascript
import { createClient } from '@supabase/supabase-js';
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// 1. Insert oferta
const { data: offer, error } = await supabase
  .from('marketing_offers')
  .insert({
    hotel_slug: 'occidental-caribe',      // SELECT de dropdown hotels_master
    title: 'Escapada Last Minute 4★',
    description: 'Todo incluido en Punta Cana...',
    offer_type: 'last_minute',            // 'last_minute' | 'flash_sale' | 'package' | 'early_bird' | 'group'
    original_price: 320,
    discount_percentage: 0,
    // ⚠️ NO insertar final_price — es GENERATED COLUMN
    // ⚠️ NO insertar gross_margin, net_margin, net_margin_percentage — trigger los calcula
    base_cost: 250,                        // Costo proveedor. DEBE ser < original_price (margen >= 15%)
    valid_from: new Date().toISOString(),
    valid_until: new Date(Date.now() + 20*24*60*60*1000).toISOString(),
    stock_total: 12,
    stock_sold: 0,
    is_published: true,
    occupancy_desc: '2 adultos + 2 niños gratis',
    conditions_text: 'Niños gratis hasta 12 años. Todo incluido.',
    stars: 5,
    allows_deposit: true,
    payment_method: 'transfer',
    fee_amount: 0,
    source_channel: 'admin_manual',
    // ai_generated_content se puede llenar manual o dejarlo vacío '{}'
  })
  .select()
  .single();

// 2. Insert rangos de fechas
if (offer) {
  const ranges = [
    { offer_id: offer.id, check_in: '2026-05-10', check_out: '2026-05-13', stock_total: 4, stock_sold: 0, stock_held: 0, is_active: true },
    { offer_id: offer.id, check_in: '2026-05-17', check_out: '2026-05-20', stock_total: 8, stock_sold: 0, stock_held: 0, is_active: true },
  ];
  await supabase.from('offer_date_ranges').insert(ranges);
}
```

**VALIDACIÓN FRONTEND antes del insert:**
```javascript
// El trigger en Postgres bloquea si margen < 15%
// Mejor validar en frontend ANTES de enviar
const margin = ((original_price - base_cost) / original_price) * 100;
if (margin < 15) {
  showError(`Margen ${margin.toFixed(1)}% es menor al 15% mínimo. Ajustar base_cost o original_price.`);
  return;
}
```

#### B) Listar ofertas (MarketingOffersPanel)
```javascript
const { data: offers } = await supabase
  .from('marketing_offers')
  .select('*, offer_date_ranges(*)')
  .order('created_at', { ascending: false });
```

#### C) Editar oferta
```javascript
await supabase
  .from('marketing_offers')
  .update({
    title: nuevoTitulo,
    original_price: nuevoPrecio,
    // ⚠️ NUNCA actualizar final_price
    is_published: true,
    updated_at: new Date().toISOString(),
  })
  .eq('id', offerId);
```

#### D) Toggle publicar/despublicar
```javascript
await supabase
  .from('marketing_offers')
  .update({ is_published: !currentValue, updated_at: new Date().toISOString() })
  .eq('id', offerId);
```

#### E) Eliminar oferta (cascade a date_ranges)
```javascript
// Primero eliminar hijos
await supabase.from('offer_date_ranges').delete().eq('offer_id', offerId);
// Luego padre
await supabase.from('marketing_offers').delete().eq('id', offerId);
```

#### F) Dropdown de hoteles (para formularios)
```javascript
const { data: hotels } = await supabase
  .from('hotels_master')
  .select('slug, name, stars, zone')
  .eq('is_active', true)
  .order('name');
// Devuelve ~110 hoteles activos
```

---

## CONEXIÓN 2: ADMIN ATLAS → SUPABASE (Cola de Aprobaciones)

### Qué hace:
El admin revisa ofertas que llegaron de proveedores externos (via n8n) y las aprueba o rechaza.

#### G) Listar pendientes de aprobación
```javascript
const { data: pending } = await supabase
  .from('external_offers')
  .select('*')
  .eq('status', 'pending_approval')
  .order('created_at', { ascending: false });
// Actualmente: 1 pendiente
```

#### H) Aprobar oferta externa
```javascript
await supabase
  .from('external_offers')
  .update({
    status: 'approved',
    approved_by: 'Aldo Hilario',
    approved_at: new Date().toISOString(),
    approval_notes: 'Aprobado desde Atlas Admin'
  })
  .eq('id', externalOfferId);
// Esto dispara el flujo A del workflow n8n → Telegram notifica → flujo B genera marketing
```

#### I) Rechazar oferta externa
```javascript
await supabase
  .from('external_offers')
  .update({
    status: 'rejected',
    rejected_by: 'Aldo Hilario',
    rejected_at: new Date().toISOString(),
    rejection_reason: 'Precio no competitivo'
  })
  .eq('id', externalOfferId);
```

---

## CONEXIÓN 3: n8n BACKEND → SUPABASE (Workflow automatizado)

### Qué hace:
El workflow WF-GENERAR-OFERTA-DE-MARKETING-v2 recibe ofertas de proveedores via webhook, las procesa con IA (Google Gemini), y las inserta en Supabase.

### Webhook endpoint (n8n):
```
PRODUCCIÓN: https://n8n-n8n.xaruuo.easypanel.host/webhook/generate-marketing-offer
PRUEBA:     https://n8n-n8n.xaruuo.easypanel.host/webhook-test/generate-marketing-offer
```

### Flujo:
```
Webhook B1 (POST) 
  → B2. Prep + Slug (genera hotel_slug)
  → B3. Query hotels_master (Supabase SELECT)
  → B4. Merge datos hotel + oferta
  → B5. Agente IA (Google Gemini genera título, descripción, bullets, CTA)
  → B6. Prep marketing_offers row (calcula base_cost si falta)
  → B7. INSERT marketing_offers (Supabase)
  → B8. Prep date_ranges
  → B9. INSERT offer_date_ranges (Supabase)
  → B10. Notificar Telegram
```

### El Admin NO necesita conectarse a n8n directamente.
n8n es backend transparente. El admin solo ve el resultado en Supabase.

**Pero si quieres mostrar estado de n8n en el admin (opcional):**
```
n8n API: https://n8n-n8n.xaruuo.easypanel.host
Workflow ID: 44dJaMdGyIsiyh3Wt5LCk
// No recomendado exponer en frontend — solo para debug
```

---

## CONEXIÓN 4: SUPABASE → WEB PÚBLICA (RPC endpoint)

### Qué hace:
La landing pública `/destinos/ofertas` consume las ofertas activas via RPC.

### Llamada desde el componente OfertasLanding.jsx:
```javascript
const res = await fetch(
  'https://oyihiyivdhfxpyiwnmqk.supabase.co/rest/v1/rpc/rpc_get_marketing_active_offers',
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'apikey': SUPABASE_ANON_KEY,
      'Authorization': `Bearer ${SUPABASE_ANON_KEY}`,
    },
    body: JSON.stringify({
      p_hotel_slug: null,    // null = todas
      p_offer_type: null,    // null = todas | 'last_minute' | 'flash_sale'
      p_limit: 20,
    }),
  }
);
const offers = await res.json(); // Array de ofertas con date_ranges, AI content, marketing_copy
```

### O via Supabase client:
```javascript
const { data, error } = await supabase.rpc('rpc_get_marketing_active_offers', {
  p_hotel_slug: null,
  p_offer_type: null,
  p_limit: 20,
});
```

### Qué filtra el RPC automáticamente:
- Solo ofertas con `is_published = true`
- Solo dentro de `valid_from` ≤ NOW() ≤ `valid_until`
- Solo con stock disponible (stock_total - stock_sold - stock_held > 0)
- Ordenadas por `net_margin_percentage DESC` (las más rentables primero)
- Incluye `date_ranges` solo activos y con stock
- Calcula `stock_available`, `nights`, `marketing_copy` automáticamente

---

## CONEXIÓN 5: WEB PÚBLICA → ADMIN (evento de selección)

### Qué hace:
Cuando un usuario en la web pública clickea "Reservar", se emite un evento que puede capturarse para flujo de checkout.

```javascript
// El componente OfertasLanding emite:
window.dispatchEvent(new CustomEvent('aliun:offer:select', {
  detail: {
    offer_id: 'uuid',
    hotel_slug: 'occidental-caribe',
    hotel_name: 'Occidental Caribe',
    final_price: 245,
    selected_range: {
      id: 'uuid',
      check_in: '2026-05-08',
      check_out: '2026-05-10',
      nights: 2,
      stock_available: 5,
    }
  }
}));

// Capturar en el parent:
<OfertasLanding
  onOfferSelect={(detail) => {
    router.push(`/checkout?offer=${detail.offer_id}&range=${detail.selected_range?.id}`);
  }}
/>
```

---

## ESTADO ACTUAL DE LA BD (verificado ahora mismo)

| Tabla | Total | Activas/Publicadas |
|-------|-------|--------------------|
| hotels_master | 125 | 110 activos |
| marketing_offers | 3 | 3 publicadas |
| offer_date_ranges | 9 | 9 activos |
| external_offers | 3 | 1 pendiente aprobación |

**RPC funcional:** Devuelve 3 ofertas activas con date_ranges, AI content, marketing_copy.

**Triggers activos en marketing_offers:**
- `validate_marketing_offer_before_insert` — bloquea si margen < 15%
- `validate_marketing_offer_before_update` — idem para updates
- `trigger_marketing_offers_depleted` — auto-despublica si stock = 0

---

## CHECKLIST DE VERIFICACIÓN PARA HORIZONS

Confirmar que cada conexión funciona:

### ✅ Admin → Supabase
- [ ] MarketingOffersPanel carga ofertas de `marketing_offers` con `offer_date_ranges`
- [ ] MarketingOfferForm inserta en `marketing_offers` (sin `final_price`) + `offer_date_ranges`
- [ ] Validación de margen >= 15% en frontend antes del insert
- [ ] Toggle publicar/despublicar funciona
- [ ] Dropdown de hoteles carga 110 hoteles de `hotels_master`
- [ ] ExternalOffersQueue carga pendientes de `external_offers`
- [ ] Aprobar/Rechazar actualiza status en `external_offers`
- [ ] MarketingMetricsBar muestra: ofertas publicadas, pendientes, stock total, margen promedio

### ✅ Supabase → Web Pública
- [ ] OfertasLanding llama a `rpc_get_marketing_active_offers` al montar
- [ ] Las 3 ofertas actuales se renderizan como tarjetas
- [ ] Filtros por tipo funcionan (Todas / Last Minute / Flash Sale)
- [ ] Chips de fechas muestran stock por rango
- [ ] CTA emite evento `aliun:offer:select` con offer_id + selected_range

### ✅ Flujo circular completo
- [ ] Admin crea oferta → aparece en web pública
- [ ] Admin despublica oferta → desaparece de web pública
- [ ] Admin edita precio → web pública refleja cambio
- [ ] Stock se agota → trigger auto-despublica → desaparece de web pública

---

## RESUMEN: QUIÉN HABLA CON QUIÉN

```
ADMIN ATLAS (/atlas-admin)
  │
  ├── INSERT/UPDATE/DELETE ──▶ marketing_offers (Supabase)
  ├── INSERT/DELETE ──────────▶ offer_date_ranges (Supabase)
  ├── UPDATE status ─────────▶ external_offers (Supabase)
  ├── SELECT ────────────────▶ hotels_master (Supabase)
  │
  │
n8n WORKFLOW (backend invisible)
  │
  ├── INSERT ────────────────▶ external_offers → marketing_offers → offer_date_ranges
  ├── SELECT ────────────────▶ hotels_master
  ├── NOTIFY ────────────────▶ Telegram
  │
  │
WEB PÚBLICA (/destinos/ofertas)
  │
  ├── RPC call ──────────────▶ rpc_get_marketing_active_offers (Supabase)
  ├── EVENT emit ────────────▶ aliun:offer:select → checkout (futuro)
```
