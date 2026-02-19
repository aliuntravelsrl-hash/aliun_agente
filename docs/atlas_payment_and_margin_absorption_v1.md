# ALIUN TRAVEL SRL
# ATLAS PAYMENT & MARGIN ABSORPTION POLICY
## v1.0 – Política de Métodos de Pago y Absorción de Costos

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Financiero Crítico  

---

# 1. PROPÓSITO

Definir la política oficial de:

- Métodos de pago permitidos en Atlas
- Absorción o traslado de comisiones
- Registro contable de costos financieros
- Protección contra disputas y contracargos
- Integración con MCP v3.6.1

---

# 2. MÉTODOS DE PAGO OFICIALES

## 2.1 Tarjetas Visa
- Fee estimado: 2%
- Fee absorbido por Aliun
- Impacta margen real

## 2.2 Tarjetas Mastercard
- Fee estimado: 3%
- Fee absorbido por Aliun
- Impacta margen real

## 2.3 PayPal Business
- Fee variable (~4.4% + fijo)
- Fee trasladado al cliente
- Requiere generación automática de Invoice

## 2.4 Transferencia / Depósito Bancario
- Sin fee financiero
- Requiere validación manual
- Estado intermedio obligatorio

---

# 3. REGLA DE ABSORCIÓN

## 3.1 Visa / Mastercard

La comisión es absorbida por Aliun.

Cálculo:

PaymentFee = sell_price × fee_rate

NetMarginReal = GrossMargin - PaymentFee - OperationalCost - RiskCost

Registrar siempre en:

absorbed_margin_ledger

---

# 4. PAYPAL – POLÍTICA ESPECIAL

PayPal no será absorbido.

El sistema debe:

1. Calcular tarifa PayPal.
2. Mostrar desglose al cliente.
3. Generar automáticamente Invoice oficial.
4. Registrar invoice_id en base de datos.
5. Guardar evidencia forense.

## 4.1 Flujo Obligatorio

Cliente selecciona PayPal
↓
Sistema calcula fee
↓
Sistema genera invoice PayPal
↓
Cliente paga invoice
↓
Webhook PayPal confirma
↓
Estado → confirmed

No permitir pagos sin invoice.

---

# 5. TABLAS REQUERIDAS

## 5.1 absorbed_margin_ledger

- id
- reservation_id
- offer_id
- payment_method
- gross_amount
- fee_rate
- fee_amount
- absorbed_by
- fiscal_period
- created_at

---

## 5.2 paypal_invoice_registry

- id
- reservation_id
- paypal_invoice_id
- invoice_amount
- fee_amount
- status
- payer_email
- created_at
- paid_at

---

## 5.3 payment_methods_config

- method_name
- fee_rate
- fee_fixed
- is_absorbed (boolean)
- requires_manual_validation (boolean)
- active

---

# 6. VALIDACIÓN MANUAL

Para métodos:

- Transferencia
- Depósito

Estado inicial:

pending_manual_payment_validation

No se considera ingreso confirmado
hasta validación manual + webhook financiero.

---

# 7. EVIDENCIA ANTI-CHARGEBACK

Al confirmarse pago:

Guardar en automation_logs:

- invoice_pdf
- IP
- payment_gateway_response
- offer_id
- reservation_id
- integrity_hash (SHA256)
- timestamp

---

# 8. IMPACTO CONTABLE

Visa/Mastercard:
- Fee registrado como gasto financiero.

PayPal:
- Fee no afecta margen interno.
- Cliente absorbe costo.

Transferencias:
- Sin gasto financiero.

---

# 9. ALERTAS AUTOMÁTICAS

Sistema debe alertar si:

- NetMarginReal < threshold mínimo
- Uso PayPal > % permitido
- Alta tasa de pagos manuales
- Disputas > 1%

---

# 10. OBJETIVO FINAL

- Maximizar conversión sin destruir margen.
- Minimizar disputas.
- Garantizar trazabilidad financiera.
- Mantener coherencia con MCP v3.6.1.
