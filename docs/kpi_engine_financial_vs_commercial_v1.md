# ALIUN TRAVEL SRL
# KPI ENGINE – FINANCIAL VS COMMERCIAL
## v1.0 – Separación de Métricas y Salud Real del Sistema

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Rector de Métricas  

---

# 1. PROPÓSITO

Definir la separación obligatoria entre:

- Métricas Comerciales (Mission Control)
- Métricas Financieras (MCP v3.6.1)
- Métricas Híbridas (Proyección Inteligente)

Objetivo:

Evitar confusión entre:
- Reservas cerradas
- Pagos recibidos
- Ingresos confirmados
- Dinero realmente disponible

---

# 2. PRINCIPIO FUNDAMENTAL

Ventas ≠ Ingresos  
Ingresos ≠ Cash disponible  

Cada KPI debe pertenecer a una categoría específica.

---

# 3. CAPA 1 – MÉTRICAS COMERCIALES (MISSION CONTROL)

Estas métricas miden desempeño de ventas.

## 3.1 Indicadores

- Total Leads
- Pending Quotes
- Booked
- Conversion Rate
- Live Monitor (New / Quoted / Booked)

## 3.2 Fuente de Datos

- bookings (Core)
- atlas_quotes
- atlas_reservations
- kommo_lead_movements

## 3.3 Definiciones

Pending Quotes:
- quoted
- payment_received
- pending_provider_confirmation

Booked:
- confirmed

Conversion Rate:
Booked / Total Leads

Estas métricas NO representan dinero disponible.

---

# 4. CAPA 2 – MÉTRICAS FINANCIERAS (MCP v3.6.1)

Estas métricas miden salud financiera real.

## 4.1 Indicadores

- total_facturado
- cash_available
- pending_payments
- ingreso_proyectado
- margen_real
- margen_proyectado

## 4.2 Fuente de Datos

- offline_operations
- proforma_invoices
- atlas_payments
- bookings
- settlement_logs

## 4.3 Disparador Oficial

Solo se considera INGRESO_CONFIRMADO cuando:

old_status = 'pending'
new_status = 'confirmed'

Emitido por el sistema de pagos (Azul / Stripe + 3DS).

---

# 5. CAPA 3 – MÉTRICAS HÍBRIDAS (PROYECCIÓN REALISTA)

Estas métricas combinan estado comercial + estado financiero.

## 5.1 Clasificación de Ingresos

1) Ingreso Tentativo
   - RESERVA_TENTATIVA

2) Ingreso Proyectado
   - PAGO_EN_VALIDACION
   - pending_provider_confirmation

3) Ingreso Confirmado
   - confirmed

4) Ingreso Liquidado
   - settlement bancario

---

# 6. TABLA DE CLASIFICACIÓN GLOBAL

| Estado | Comercial | Financiero | Impacta Cash |
|--------|-----------|------------|--------------|
| quoted | Sí | No | No |
| payment_received | Sí | Parcial | No |
| pending_provider_confirmation | Sí | Proyectado | No |
| confirmed | Sí | Confirmado | Sí |
| settlement | No | Liquidado | Sí |

---

# 7. MARGEN Y RENTABILIDAD

El sistema debe calcular:

Margen Proyectado:
precio_venta - costo_proveedor_estimado

Margen Real:
precio_venta - costo_proveedor_confirmado - fees_pago

El margen real solo puede calcularse tras:

- Confirmación proveedor
- Confirmación pago
- Fee bancario registrado

---

# 8. TERMÓMETRO 200K – DEFINICIÓN CORRECTA

El Termómetro 200K debe reflejar:

INGRESO_CONFIRMADO acumulado

No:
- Booked
- Quotes
- Ingreso proyectado

Esto evita ilusión de crecimiento falso.

---

# 9. ALERTAS DE RIESGO

El sistema debe alertar si:

- Conversion Rate alto pero cash_available bajo
- Muchas reservas en pending_provider_confirmation
- Alta tasa de rejected
- Alta concentración de ingresos proyectados vs confirmados

---

# 10. VISUALIZACIÓN RECOMENDADA (DASHBOARD FUTURO)

Separar claramente:

## Sección Comercial
- Leads
- Quotes
- Booked
- Conversion %

## Sección Financiera
- Ingreso Confirmado
- Cash Disponible
- Margen Real
- Settlement Pendiente

## Sección Proyección
- Ingreso Proyectado
- Riesgo Operativo
- % Confirmación Proveedor

---

# 11. OBJETIVO FINAL

Con esta separación:

- Ventas no manipulan percepción financiera.
- Finanzas no frenan crecimiento comercial.
- Director tiene visión clara de:
   - Actividad
   - Ingresos reales
   - Flujo de caja
   - Riesgo

Este documento rige toda lectura de métricas en Aliun Travel.
