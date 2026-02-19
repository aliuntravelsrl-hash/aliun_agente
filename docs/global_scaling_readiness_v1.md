# ALIUN TRAVEL SRL
# GLOBAL SCALING READINESS
## v1.0 – Preparación para Escala API y Marca Blanca

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Estratégico de Expansión  

---

# 1. PROPÓSITO

Evaluar y estructurar la preparación de Aliun Travel para:

- Conexión API directa con proveedores
- Integración con bancos internacionales
- Operación multi-divisa
- Marca blanca (White Label)
- Escalabilidad sin colapso operativo

Este documento define los pilares necesarios antes de escalar.

---

# 2. SITUACIÓN ACTUAL

Estado actual:

- Motor 1 (Core) operativo.
- Motor 2 (Atlas) con confirmación manual.
- MCP v3.6.1 como motor financiero central.
- Control de cashflow documentado.
- KPI comercial y financiero separados.
- Evidencia antifraude automatizada.

Conclusión:
La arquitectura base está preparada para escalar.

---

# 3. REQUISITOS PARA ESCALA API DIRECTA

## 3.1 Inventario en Tiempo Real

Necesario:

- Endpoint de disponibilidad en vivo.
- Sincronización automática de cupo.
- Eliminación de estado pending_provider_confirmation.

Impacto:

- Reducción de fricción.
- Confirmación inmediata.
- Disminución de rechazos.

---

## 3.2 Sincronización de Tarifas

- Eliminación de precio manual.
- Validación dinámica.
- Prevención de underpricing.

---

## 3.3 Gestión de Penalidades Automáticas

- Políticas embebidas por proveedor.
- Cancelaciones calculadas por API.
- Ajuste automático de margen.

---

# 4. MULTI-DIVISA Y EXPANSIÓN GLOBAL

Requisitos:

- exchange_rates sincronizado.
- Pricing en USD / EUR / DOP.
- Control de spread cambiario.
- Separación margen FX vs margen hotel.

---

# 5. MARCA BLANCA (WHITE LABEL)

Para operar como Tech Travel Global:

Debe existir:

- API pública segura.
- Rate limiting.
- Autenticación OAuth2.
- Dashboard separado por partner.
- Commission tracking.

Modelo:

Aliun = Infraestructura  
Partners = Distribuidores  

---

# 6. RIESGOS DE ESCALA

## 6.1 Riesgo Operativo
- Volumen alto sin automatización API.

## 6.2 Riesgo Financiero
- Ingreso proyectado > cash real.

## 6.3 Riesgo Técnico
- Acoplamiento excesivo entre motores.

---

# 7. INDICADORES DE PREPARACIÓN

Aliun está lista para escala global cuando:

- % rechazo proveedor < 5%
- Confirmación promedio < 2 horas
- Contracargos < 1%
- Margen real estable > 12%
- Settlement promedio < 3 días

---

# 8. FASES DE ESCALAMIENTO

## Fase 1 – Estabilización
Optimización de Atlas manual.
Reducción de fricción.

## Fase 2 – Integración API Selectiva
Conectar 1 proveedor estratégico.
Validar sincronización.

## Fase 3 – Multiproveedor
Escalar inventario.
Automatizar penalidades.

## Fase 4 – Marca Blanca
Abrir API propia.
Integrar partners.

---

# 9. CONDICIÓN NO NEGOCIABLE

El crecimiento comercial nunca debe adelantarse a la capacidad financiera.

Escalar solo cuando:

- Cashflow estable.
- Margen real probado.
- Proceso antifraude validado.

---

# 10. OBJETIVO FINAL

Convertir Aliun Travel en:

- Plataforma tecnológica
- Conexión API directa
- Infraestructura de distribución
- Marca blanca internacional
- Empresa financieramente blindada

Este documento rige cualquier decisión de expansión futura.
