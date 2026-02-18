# ALIUN TRAVEL SRL
# AI REVENUE ENGINE
## System Architecture v1.0

**Fecha:** 17 Febrero 2026  
**Director:** Aldo Hilario  
**Clasificación:** Documento Rector – Infraestructura Comercial

---

# 1. PROPÓSITO

Este documento define la arquitectura oficial del ecosistema tecnológico de Aliun Travel SRL.

Objetivo:

- Unificar contexto técnico
- Evitar fragmentación de decisiones
- Establecer SSOT (Single Source of Truth)
- Definir capas del sistema
- Servir como referencia para desarrollos futuros

---

# 2. VISIÓN ESTRATÉGICA

Aliun Travel opera como una Agencia de Viajes con infraestructura automatizada basada en IA.

El sistema está diseñado para:

- Convertir inventario hotelero en ingresos medibles
- Automatizar cotización y confirmación
- Mantener control financiero
- Escalar sin aumentar proporcionalmente carga operativa humana

---

# 3. ARQUITECTURA EN CAPAS

## CAPA 1 — INVENTARIO (SSOT)

Tabla central:
`hotels_master`

Relaciones:

hotels_master  
 ├── rooms  
 │    └── rates  
 │         └── seasons  
 ├── rag_context_cache  
 ├── hotel_media  
 └── JSONB enriquecido  

Regla:
Toda consulta de hotel debe partir desde `hotels_master`.

---

## CAPA 2 — MOTORES DE RESERVA

### Motor 1 — Reserva Dinámica

Función:
Cotización y confirmación en tiempo real.

Flujo:

Cliente → IA → RAG → Validación → Cotización → Pago → Flujo F → PDF → Email → Log

Características:

- Validación de ocupación
- Conversión USD/DOP
- Protocolo de Voucher en 3 estados
- Cero Neuromarketing

---

### Motor 2 — Sales Offers (CODEN 2.2.4.5)

Función:
Ofertas con disponibilidad garantizada.

Flujo:

Proveedor → Telegram → external_offers → Aprobación → Publicación → Venta

Características:

- Precio cerrado
- Fechas bloqueadas
- Validación automática
- ID determinista (ALN-EXT-XXXX)

---

## CAPA 3 — ORQUESTACIÓN

Tecnología:
n8n

Workflows críticos:

- Error Handler (obligatorio)
- Atlas Inventory
- Flujo F
- External Offers
- KPI Engine (fase 1)
- Lead Scoring (fase 2)

Regla:
Ningún workflow entra en producción sin Error Handler activo.

---

## CAPA 4 — ADQUISICIÓN

Fuentes:

- Google Ads (intención alta)
- Meta Retargeting
- Base de datos propia

Flujo:

Ads → Landing → Webhook → Supabase → IA

---

## CAPA 5 — INTELIGENCIA Y MÉTRICAS

Componentes:

- hotel_kpi_daily
- vista rolling 30d
- priority_score
- ROAS por hotel
- Conversión Lead → Reserva

Principio:
Optimización basada en margen, no en likes.

---

## CAPA 6 — INFRAESTRUCTURA

- Supabase (PostgreSQL + RLS + Storage)
- n8n
- Google Gemini
- Redis
- MCP Server (PDF)
- Docker + EasyPanel
- SMTP

---

# 4. PRINCIPIOS ARQUITECTÓNICOS

1. SSOT único.
2. No duplicación de datos.
3. Todo ingreso debe registrarse formalmente.
4. Métricas > intuición.
5. Modularidad.
6. Trazabilidad completa.
7. Error Handler siempre activo.

---

# 5. ROADMAP GENERAL

## Fase 1 — Estabilización
- Confirmar tabla reservas
- Activar workflows
- Implementar KPI Engine
- Activar Ads intención básica

## Fase 2 — Optimización
- Lead Scoring
- Follow-up automático
- Priorización dinámica de hoteles

## Fase 3 — Escala
- Optimización automática Ads
- Activación total Motor 2
- Growth Loop cerrado

---

# 6. ESTADO ACTUAL

✔ SSOT confirmado  
✔ 116 hoteles cargados  
✔ Arquitectura dual definida  
⚠ Workflows inactivos  
⚠ KPI Engine pendiente  
⚠ Tabla reservas por confirmar  

---

Documento oficial base.
Cualquier modificación estructural debe actualizar este archivo.
