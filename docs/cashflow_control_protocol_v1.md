# ALIUN TRAVEL SRL
# CASHFLOW CONTROL PROTOCOL
## v1.0 – Protección de Liquidez y Exposición Financiera

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Crítico Financiero  

---

# 1. PROPÓSITO

Establecer el protocolo oficial para:

- Proteger liquidez
- Evitar descuadres de caja
- Minimizar exposición ante proveedores
- Controlar reservas no liquidadas
- Blindar margen real

Este protocolo aplica a:

- Motor 1 (Core)
- Motor 2 (Atlas – Sales Offers & Bloqueos)

---

# 2. PRINCIPIO FUNDAMENTAL

No se compromete dinero con proveedor
hasta que el ingreso esté confirmado y validado.

Ingreso confirmado = webhook payment status = confirmed.

---

# 3. CLASIFICACIÓN DE LIQUIDEZ

## 3.1 Cash Disponible
Dinero liquidado y asentado bancariamente.

Fuente:
- settlement confirmado
- fees descontados

## 3.2 Cash Comprometido
Reservas confirmed pendientes de pago a proveedor.

## 3.3 Cash Proyectado
Reservas en:
- payment_received
- pending_provider_confirmation

Este dinero NO puede comprometerse.

---

# 4. REGLA DE ORO – COMPROMISO CON PROVEEDOR

Solo se puede:

- Emitir pago proveedor
- Transferir anticipo
- Confirmar plaza

Si:

reserva_status = confirmed
AND ingreso_estado = INGRESO_CONFIRMADO

Nunca antes.

---

# 5. SALES OFFERS – PROTECCIÓN DE LIQUIDEZ

Características:
- No reembolsables
- Pago total obligatorio

Protocolo:

1) Esperar webhook confirmed.
2) Calcular margen real.
3) Reservar costo proveedor.
4) Confirmar plaza con proveedor.
5) Emitir voucher definitivo.

Prohibido:
Confirmar proveedor antes del confirmed financiero.

---

# 6. BLOQUEOS – GESTIÓN DE EXPOSICIÓN

Bloqueos pueden implicar:

- Anticipos
- Penalidades
- Ventanas limitadas

Protocolo:

Si proveedor exige anticipo antes de pago cliente:

Debe registrarse como:

EXPOSICION_TEMPORAL

Y debe aprobarse manualmente.

Nunca asumir pago futuro como garantía.

---

# 7. CONTROL DE DISPUTAS

Ante contracargo:

1) Suspender margen proyectado.
2) Activar expediente forense.
3) Congelar pago proveedor si aún no liquidado.
4) Ajustar dashboard financiero.

---

# 8. RESERVAS RECHAZADAS POR PROVEEDOR

Si proveedor rechaza tras pago confirmado:

Opciones:

- Reembolso total inmediato.
- Ofrecer alternativa equivalente.
- Ajustar diferencia.

Cashflow:

- Registrar salida como reversión.
- Ajustar margen real.

---

# 9. INDICADORES DE ALERTA TEMPRANA

Sistema debe alertar si:

- % ingreso_proyectado > 40% del total_facturado.
- cash_available < costo_proveedor_confirmado.
- alta tasa de rejected.
- settlement promedio > 5 días.

---

# 10. MARGEN REAL vs MARGEN PROYECTADO

Margen Proyectado:
precio_venta - costo_estimado

Margen Real:
precio_venta - costo_confirmado - fees_pago - devoluciones

Decisiones estratégicas solo deben basarse en margen real.

---

# 11. TERMÓMETRO 200K – PROTECCIÓN

El Termómetro solo suma:

INGRESO_CONFIRMADO

Nunca:

- quoted
- projected
- payment_received

Esto evita ilusión financiera.

---

# 12. RESERVA DE CONTINGENCIA

Se recomienda mantener:

10%–15% del cash_available
como fondo de contingencia para:

- Reembolsos
- Penalidades proveedor
- Disputas

---

# 13. FUTURA ESCALABILIDAD (API DIRECTA)

Cuando exista API directa:

- Disminuye riesgo de rechazo.
- Reduce cash en proyección.
- Aumenta velocidad de rotación.

Pero el protocolo financiero no cambia.

---

# 14. OBJETIVO FINAL

Este protocolo garantiza:

- Liquidez estable.
- Crecimiento sostenible.
- Protección contra fraude.
- Control de exposición.
- Escalabilidad global sin colapso financiero.
