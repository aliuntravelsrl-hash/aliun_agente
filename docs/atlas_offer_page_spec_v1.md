# ALIUN TRAVEL SRL
# ATLAS OFFER PAGE (PDP) SPEC
## v1.0 – High-Conversion Offer Page (Nike PDP Pattern)

Fecha: 18 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Técnico–Producto (Frontend + Atlas)

---

# 1. PROPÓSITO

Definir la página de oferta individual (PDP) del Motor Atlas (Motor 2) para maximizar conversión.

Esta página es un **entrypoint explícito** a Atlas:
- Si el usuario está aquí, el motor seleccionado es Atlas por definición.

---

# 2. PRINCIPIOS DE DISEÑO (CONFIRMADOS)

- La oferta es el héroe (visual premium).
- Fricción cero (CTA siempre visible).
- Minimalismo premium (confianza).
- UX tipo e-commerce: claridad + acción inmediata.

Desktop:
- Split screen 60/40
- Sidebar sticky (transaccional)

Mobile:
- Carrusel arriba
- Sticky bottom CTA

---

# 3. CONTRATO FUNCIONAL (LO QUE DEBE HACER)

## 3.1 Fuente de verdad
La PDP se alimenta de Motor 2 (Atlas):
- Oferta aprobada
- Fechas válidas
- Cupo disponible
- Política de pago vigente
- Expiración activa

## 3.2 Reglas críticas de negocio
1) Nunca mostrar CTA habilitado si:
   - status != approved
   - cupo_disponible <= 0
   - now() > valid_to
   - booking_window excedido

2) Cotización NO descuenta cupo.
3) Cupo se descuenta solo en payment_success.
4) Política no reembolsable debe mostrarse antes de pago.

---

# 4. COMPONENTES UI (OBLIGATORIOS)

## 4.1 Columna Izquierda – Inmersiva
- Galería vertical (desktop) / carrusel (mobile)
- Microcopy: “Oferta Atlas” + badge de expiración (ej: “Expira en 3 días”)
- Highlights visuales del paquete

## 4.2 Columna Derecha – Transaccional (Sticky)
Orden exacto:

1) H1 (nombre oferta + hotel)
2) Precio ancla (total / desde / por persona según modelo)
3) “Qué incluye” (bullet list)
4) Selectores del motor:
   - Fechas (datepicker)
   - Pax (adultos/niños)
   - Room type (si aplica)
5) CTA principal:
   - Desktop: botón negro “Ver disponibilidad / Continuar”
   - Mobile: sticky bottom CTA

6) Política y términos (colapsable):
   - plazo de pago
   - no reembolsable
   - cutoff cancelación
   - soporte excepciones

---

# 5. INTEGRACIÓN (MOTOR DE RESERVAS ATLAS)

## 5.1 Entrada (Offer Context)
La PDP debe cargar por:
- offer_id (ruta: /ofertas/:offer_id) o
- slug + campaign_id (si se usa marketing)

## 5.2 Data Binding (mínimo viable)
Campos mínimos requeridos:

- offer_id
- hotel_slug
- title
- subtitle (opcional)
- images[]
- check_in_min / check_out_max (o date window)
- valid_from / valid_to
- booking_window_max_days
- cupo_disponible
- pricing_model:
  - total_price_override (precio cerrado)
  - o price_per_pax (si se aplica)
- included_benefits[] (vip, upgrade, etc.)
- policy:
  - payment_deadline_hours
  - non_refundable_amount
  - cancellation_cutoff_date
  - provider_penalty_terms

## 5.3 Endpoint/Query sugerido (conceptual)
- GET Offer By ID
- GET Offer Availability Window (si aplica)
- POST Atlas Quote (cotización)
- POST Atlas Payment Init (checkout)
- POST payment_success → decrement cupo (backend)

---

# 6. FLUJO DE CONVERSIÓN (ATLAS)

1) Usuario abre PDP (offer_id)
2) Sistema valida oferta:
   - approved + vigente + cupo
3) Usuario selecciona fechas y pax
4) CTA → crea “Atlas Quote” (estado quoted)
5) Checkout → pago
6) payment_success:
   - cupo_disponible -= 1
   - reserva atlas = confirmed
   - voucher PDF + email
7) Kommo: movimiento a “Confirmado (Atlas)”

---

# 7. ESTADOS UI (NO NEGOCIABLE)

- Loading (skeleton)
- Oferta no encontrada
- Oferta expirada (CTA deshabilitado)
- Cupo agotado (CTA deshabilitado)
- Oferta vigente (CTA habilitado)
- Error de cotización (retry + soporte)

---

# 8. TRACKING & ATRIBUCIÓN

La PDP debe registrar:

- offer_view
- quote_created (Atlas Quote)
- checkout_started
- payment_success (conversion)

Con UTM:
- source / medium / campaign / content
Asociado a offer_id.

---

# 9. CHECKLIST PARA PASAR A PRODUCCIÓN

Navbar:
- actualizar enlace “Ofertas” cuando Motor 2 esté certificado

Frontend:
- conectar template a datos reales
- sticky sidebar / sticky bottom CTA
- estados UI completos

Backend:
- endpoints/rpcs para offer y quote
- validación server-side de vigencia y cupo
- payment_success decrementa cupo

Operación:
- campañas solo publicables si approved
- expiración automática (cron)

---

Este documento define la PDP oficial de Atlas.
Toda implementación debe cumplir reglas de vigencia, cupo y políticas antes de habilitar CTA.
