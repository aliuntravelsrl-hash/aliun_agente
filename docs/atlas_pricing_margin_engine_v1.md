# ALIUN TRAVEL SRL
# ATLAS PRICING & MARGIN ENGINE
## v1.0 – Motor Inteligente de Margen e Inversión

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Estratégico Financiero  

---

# 1. PROPÓSITO

Definir el motor de cálculo de:

- Margen bruto
- Margen real
- Margen proyectado
- Score de inversión por hotel
- Recomendación de presupuesto publicitario

---

# 2. INPUTS OBLIGATORIOS

- net_price (proveedor)
- sell_price (cliente)
- payment_method
- payment_fee_rate
- operational_cost_per_booking
- expected_rejection_rate
- expected_dispute_rate
- risk_weight

---

# 3. CÁLCULOS BASE

## 3.1 Margen Bruto

GrossMargin = sell_price - net_price

---

## 3.2 Fee Financiero

IF method absorbed:
    PaymentFee = sell_price × fee_rate
ELSE:
    PaymentFee = 0

---

## 3.3 Costo Operativo

OperationalCost = operational_cost_per_booking

---

## 3.4 Costo de Riesgo

RiskCost = sell_price × (rejection_rate + dispute_rate) × risk_weight

---

## 3.5 Margen Real Estimado

NetMarginReal = GrossMargin - PaymentFee - OperationalCost - RiskCost

---

# 4. HOTEL INVESTMENT SCORE (0–100)

Score =  
0.40 * ProfitScore +  
0.20 * ConversionScore +  
0.20 * ReliabilityScore +  
0.10 * ReputationScore +  
0.10 * OpsScore

---

# 5. CLASIFICACIÓN POR TIER

Tier S (90–100)
→ Promoción agresiva

Tier A (75–89)
→ Promoción activa

Tier B (60–74)
→ Test controlado

Tier C (<60)
→ No escalar

---

# 6. BOTÓN “CREAR PRESUPUESTO DE CAMPAÑA”

Al activarse:

- Genera campaign_budget_request
- Incluye:
  - hotel_slug
  - Tier
  - NetMarginReal
  - Score
  - Recomendación de presupuesto
  - Brief creativo automático

---

# 7. RECOMENDACIÓN DE PRESUPUESTO

Tier S → $20–50/día  
Tier A → $10–25/día  
Tier B → $5–10/día  
Tier C → 0

---

# 8. ALERTAS DE MARGEN

Mostrar advertencia si:

NetMarginReal < margen mínimo definido

---

# 9. INTEGRACIÓN CON MCP

Solo confirmed impacta:

- total_facturado
- cash_available

Projected no altera margen real.

---

# 10. OBJETIVO FINAL

Convertir Atlas en:

- Motor comercial
- Motor de margen
- Motor de inversión inteligente
- Base para expansión global
