# ALIUN TRAVEL SRL
# MOTOR 2 — CAMPAIGN & SALES OFFERS ENGINE
## v1.0 – Arquitectura Comercial Prioritaria

Fecha: 18 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Estratégico-Técnico  

---

# 1. PROPÓSITO

El Motor 2 es el sistema oficial de campañas comerciales de Aliun Travel.

Este motor es responsable de:

- Ofertas efímeras
- Niños gratis
- Descuentos especiales
- Upgrades incluidos
- Brazaletes VIP
- Último minuto
- Cupos limitados
- Ventanas de reserva (Booking Windows)

Motor 2 tiene prioridad sobre Motor 1 cuando existe campaña activa.

---

# 2. PRINCIPIOS ESTRUCTURALES

1. Toda campaña requiere aprobación en Admin.
2. Toda campaña tiene fecha de inicio y expiración obligatoria.
3. Ninguna promoción vive fuera de base de datos.
4. El agente no puede inventar promociones.
5. La escasez solo puede comunicarse si existe cupo real registrado.

---

# 3. TIPOS DE CAMPAÑA SOPORTADOS

## 3.1 Niños Gratis
- max_children_free
- rango de edad
- fechas válidas
- hoteles aplicables

## 3.2 Descuento Porcentaje
- discount_percentage
- margen mínimo requerido

## 3.3 Último Minuto
- booking_window (<= 30 días)
- fechas de viaje dentro del mismo mes

## 3.4 Upgrade Incluido
- room_upgrade_type
- condiciones mínimas de estancia

## 3.5 Brazalete VIP
- beneficio incluido
- valor agregado no monetario

## 3.6 Plazas Limitadas
- cupo_total
- cupo_disponible
- decremento automático al confirmar reserva

---

# 4. ESTRUCTURA BASE DE DATOS PROPUESTA

Tabla: campaign_offers

Campos mínimos:

- campaign_id (PK)
- hotel_slug (FK → hotels_master.slug)
- campaign_type
- total_price_override (nullable)
- discount_percentage (nullable)
- max_children_free (nullable)
- room_upgrade (nullable)
- vip_included (boolean)
- cupo_total
- cupo_disponible
- valid_from
- valid_to
- booking_window_max_days
- status (draft / approved / expired)
- priority_weight
- created_by_admin
- created_at

---

# 5. LÓGICA DE EXPIRACIÓN

Una campaña expira automáticamente si:

- current_date > valid_to
- cupo_disponible = 0
- booking_window superado

Al expirar:
status → expired
Agente deja de ofrecerla inmediatamente.

---

# 6. LÓGICA DE SELECCIÓN DEL AGENTE

Cuando el cliente consulta:

Paso 1:
Consultar campaign_offers
WHERE status = 'approved'
AND hotel_slug coincida
AND fechas dentro de valid_from / valid_to
AND booking_window válida

Paso 2:
Si existen múltiples campañas:
Ordenar por priority_weight DESC

Paso 3:
Si no hay campañas:
Fallback a Motor 1

---

# 7. COHERENCIA CON PUBLICIDAD

Toda campaña activa en Ads debe existir en campaign_offers.

El agente:
- Debe identificar campaign_id desde UTM o contexto.
- Debe validar que la campaña esté activa.
- Debe responder coherentemente con lo publicado.

---

# 8. ESCALABILIDAD FUTURA

En fase Content Engine:

El algoritmo podrá sugerir campañas basadas en:

- Alta demanda
- Baja tasa de rechazo
- Alto margen
- Ocupación crítica

Pero la aprobación final siempre será humana.

---

# 9. MÉTRICAS CLAVE

Por campaña se medirá:

- Reservas generadas
- Revenue total
- Margen real
- Tasa de conversión
- Velocidad de agotamiento de cupo

---

# 10. PRIORIDAD SOBRE MOTOR 1

Regla oficial:

Si existe campaña válida para la intención del usuario,
Motor 2 tiene prioridad sobre Motor 1.

Motor 1 solo actúa como fallback.

---

# 11. OBJETIVO FINAL

Motor 2 es el núcleo de:

- Marketing estratégico
- Escasez controlada
- Venta inteligente
- Ofertas medibles

Este motor debe mantener independencia total de rates y seasons.
