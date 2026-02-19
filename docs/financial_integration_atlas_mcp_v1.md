# ALIUN TRAVEL SRL
# FINANCIAL INTEGRATION: ATLAS ↔ MCP v3.6.1
## v1.0 – Blindaje Financiero y Disparadores Contables

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Rector Financiero  

---

# 1. PROPÓSITO

Definir la integración obligatoria entre:

- Motor Comercial (Atlas – Sales Offers & Bloqueos)
- Motor Financiero (MCP v3.6.1)
- Dashboard Financiero (get_accounting_dashboard)

Objetivo:

Garantizar:
- Control total de cashflow
- Proyección realista de ingresos
- Prevención de disputas
- Activación correcta del Termómetro 200K
- Cero reconocimiento contable prematuro

---

# 2. PRINCIPIO FUNDAMENTAL

Una reserva NO es un ingreso.

Un pago registrado NO es un ingreso confirmado.

El único disparador contable válido es:

old_status = 'pending'
new_status = 'confirmed'

Emitido por el sistema híbrido de pagos (Azul / Stripe + 3D Secure).

---

# 3. MAPEO OBLIGATORIO DE ESTADOS (ATLAS → MCP)

## 3.1 Estados Comerciales Atlas

- quoted
- payment_received
- pending_provider_confirmation
- confirmed
- rejected
- expired

## 3.2 Mapeo a offline_operations

| Estado Atlas | Categoría Financiera |
|--------------|---------------------|
| quoted | RESERVA_TENTATIVA |
| payment_received | PAGO_EN_VALIDACION |
| pending_provider_confirmation | INGRESO_PROYECTADO |
| confirmed | INGRESO_CONFIRMADO |
| rejected | CANCELADO |
| expired | CANCELADO |

Este mapeo es obligatorio.

Sin este registro, el Termómetro 200K opera a ciegas.

---

# 4. REGLA ESTRICTA – SALES OFFERS (SIN ABONOS)

Si:

engine = 'atlas'
offer_type = 'sales'

Entonces:

- monto_pagado debe ser igual a monto_total
- monto_abonado parcial está prohibido
- cualquier intento parcial debe rechazarse automáticamente

Validación obligatoria en Data Contract:

IF offer_type='sales' AND monto_pagado < monto_total  
THEN reject_transaction()

Mensaje al usuario:
"Las ofertas especiales requieren pago total para confirmar la solicitud."

Esto previene disputas por pagos incompletos en productos no reembolsables.

---

# 5. DISPARADOR CONTABLE ÚNICO

El único evento que puede:

- Generar Proforma (Skeleton)
- Incrementar total_facturado
- Modificar cash_available
- Actualizar Termómetro 200K

Es:

Webhook de pago:
old_status='pending'
new_status='confirmed'

Hasta ese momento:

- No emitir voucher final
- No liberar servicio
- No registrar ingreso confirmado

Esto garantiza Liability Shift frente a fraude.

---

# 6. EVIDENCIA LEGAL AUTOMATIZADA

Cada confirmación Atlas debe generar automáticamente:

- PDF Skeleton
- Hash SHA256 (integrity_hash)
- IP del cliente
- Resultado 3D Secure
- offer_id
- payment_id
- timestamp

Estos datos se registran en:

automation_logs

Esto crea expediente forense automático ante contracargos (Azul / CardNET).

---

# 7. FLUJO FINANCIERO COMPLETO

1) Cliente crea quote → RESERVA_TENTATIVA
2) Cliente paga → PAGO_EN_VALIDACION
3) 3D Secure valida → confirmed
4) Sistema:
   - Genera Skeleton
   - Marca INGRESO_CONFIRMADO
   - Actualiza Dashboard Financiero
5) Settlement bancario → cash_available real

---

# 8. CLASIFICACIÓN DE INGRESOS

## 8.1 Ingreso Tentativo
Quote generada.

## 8.2 Ingreso Proyectado
Pago recibido, pendiente validación proveedor.

## 8.3 Ingreso Confirmado
Webhook confirmed (3DS exitoso).

## 8.4 Ingreso Liquidado
Settlement bancario.

El dashboard debe distinguir claramente entre estas categorías.

---

# 9. PROTECCIÓN CONTRA ERRORES OPERATIVOS

Está prohibido:

- Marcar reserva como confirmed manualmente.
- Generar factura sin webhook confirmado.
- Omitir registro en offline_operations.
- Permitir pagos parciales en Sales Offers.

---

# 10. FUTURA INTEGRACIÓN API PROVEEDORES

Cuando exista API directa:

- pending_provider_confirmation podrá automatizarse.
- confirmation_mode cambiará a 'api'.
- Disminuirá latencia operativa.
- No cambia el disparador contable.

La regla financiera permanece inmutable.

---

# 11. RESULTADO ESPERADO

Con esta integración:

- Mission Control refleja realidad comercial.
- MCP refleja realidad financiera.
- El Termómetro 200K opera con datos reales.
- Se elimina reconocimiento anticipado de ingresos.
- Se minimizan disputas y contracargos.

Este documento rige toda interacción Atlas ↔ MCP.
Cualquier modificación al flujo de pagos requiere actualización obligatoria.
