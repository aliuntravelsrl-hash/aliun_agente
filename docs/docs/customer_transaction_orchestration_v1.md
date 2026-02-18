# ALIUN TRAVEL SRL
# CUSTOMER TRANSACTION ORCHESTRATION
## v1.0 – Orquestación Cliente → CRM → Bi-Motor → Confirmación

Fecha: 18 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Rector Operacional (Journey + Routing)

---

# 1. PROPÓSITO

Este documento define el flujo completo del cliente dentro de Aliun Travel, integrando:

- Kommo Action Engine (CRM)
- Booking Engine Bi-Motor (Core + Atlas)
- Vendedor IA
- Flujos n8n (incl. Flujo F)
- Políticas de seguridad y trazabilidad

Objetivo:
Garantizar coherencia transaccional, trazabilidad y control de riesgo.

---

# 2. DECISIÓN RECTOR (CONFIRMADA)

**Regla B (Oficial):**
La primera propuesta siempre se procesa por el Motor Core (Motor 1).
Atlas (Motor 2) solo se activa si existe un match explícito o una asignación forzada.

Esto protege:
- coherencia del CRM
- control de campañas/bloqueos
- prevención de costos por terceros
- minimización de falsas promesas

---

# 3. COMPONENTES DEL SISTEMA

## 3.1 Action Engine (Kommo)
Rol:
- Captura del lead
- Normalización
- Movimiento de pipeline
- Auditoría forense

Activos:
- Validación HMAC-SHA256
- OAuth2
- Logs forenses (kommo_webhook_logs, kommo_lead_movements)
- Función SQL crítica: validate_hotel_budget()

## 3.2 Booking Engine (Bi-Motor)
### Motor 1: Core (Reserva Dinámica)
- Cotización estándar basada en inventario general (SSOT)
- No depende de campañas efímeras

### Motor 2: Atlas (Reservas Garantizadas / Bloqueos / Campañas)
- Ofertas efímeras, cupos limitados, bloqueos proveedor
- Booking windows y expiración
- Política de pago y cancelación asociada
- Cupo se descuenta SOLO al pagar

## 3.3 Content Engine (futuro inmediato)
- Publicación coherente con campañas aprobadas
- Alimenta intención, no inventa promociones

---

# 4. CUSTOMER JOURNEY – FLUJO COMPLETO

## Fase 1 — Entrada del Lead
Canales:
- WhatsApp / Web / Ads → Kommo

Evento:
- Webhook Kommo → n8n Action Engine

## Fase 2 — Calificación (Action Engine)
1. Validación HMAC + OAuth2
2. Log forense (kommo_webhook_logs)
3. validate_hotel_budget() → valida presupuesto mínimo
4. Movimiento pipeline según calificación (kommo_lead_movements)
5. Despacho inicial por WhatsApp (Blotato) / canal

**Nota:** aquí NO se elige Atlas. Solo se califica.

## Fase 3 — Propuesta Inicial (Siempre Core)
El Vendedor IA genera propuesta estándar usando Motor 1 (Core).

Resultado:
- Estado Kommo → "Propuesta (Core)"
- Se emite cotización (no consume cupo)

## Fase 4 — Evaluación Atlas (Match explícito)
Se activa SOLO si ocurre uno de estos triggers:

A) El cliente menciona oferta específica (ID, texto, anuncio)
B) El sistema detecta match explícito con:
   - external_offers aprobadas
   - sales_offers activas
   - bloqueos vigentes con cupo > 0
C) El Director/Operación fuerza engine='atlas'

Si se cumple:
- Se consulta Motor 2
- Se presenta alternativa Atlas (opcional comparativa)
- Estado Kommo → "Propuesta (Atlas)"

## Fase 5 — Pago
El cliente paga (depósito o total).

Evento:
- payment_success webhook → Flujo F / Atlas payments

Regla:
- Solo en payment_success se descuenta cupo (Motor 2)
- Cotización nunca descuenta cupo

## Fase 6 — Confirmación
- Emisión de PDF/Voucher
- Registro en DB
- Email / WhatsApp
- Movimiento Kommo:
  - "Confirmado (Core)" o "Confirmado (Atlas)"
- Trazabilidad en logs

## Fase 7 — Soporte / Excepciones
Si el cliente no puede viajar:

- Si dentro de ventana → soporte gestiona con proveedor
- Si fuera de ventana → penalidades aplican
- Excepciones por accidente/enfermedad → gestión humana con proveedor

El sistema:
- registra caso
- notifica soporte
- no promete reembolso automático

---

# 5. MOTOR SELECTION – SUBCOMPONENTE (RESUMEN)

Regla de selección:

1) Propuesta inicial = Core (siempre)
2) Atlas solo con match explícito o asignación forzada
3) Si Atlas aplica:
   - validar status=approved
   - validar fechas vigentes
   - validar cupo_disponible > 0
   - validar booking_window

---

# 6. ESTADOS CRM (PIPELINE) RECOMENDADOS

- Lead Entrante
- Calificado
- Propuesta (Core)
- Propuesta (Atlas)
- Pago Pendiente
- Confirmado (Core)
- Confirmado (Atlas)
- Soporte / Caso Especial
- Cerrado / Expirado

---

# 7. POLÍTICAS DE RIESGO (NO NEGOCIABLES)

1) Nunca prometer confirmación sin pago.
2) Nunca ofrecer Atlas sin cupo disponible.
3) Nunca inventar campañas/promos.
4) Error Handler activo siempre.
5) Logs forenses obligatorios.
6) Rollback < 15 minutos (objetivo operativo).

---

# 8. ENTREGABLES DE IMPLEMENTACIÓN (REFERENCIA)

- Vincular Error Handler a todos los workflows
- Unificar tracking engine='core'|'atlas' en:
  - frontend
  - workflow
  - DB logs
- Formalizar tabla de reservas (si no existe)
- Implementar decremento de cupo en payment_success (Atlas)

---

Documento rector para orquestación cliente y enrutamiento bi-motor.
Cualquier cambio en Journey o selección debe actualizar este archivo.
