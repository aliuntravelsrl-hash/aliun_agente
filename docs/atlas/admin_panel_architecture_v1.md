# ALIUN TRAVEL SRL
# ATLAS CONTROL CENTER
## v1.0 – Arquitectura del Panel Administrativo del Motor 2

Fecha: 19 Febrero 2026  
Director: Aldo Hilario  
Clasificación: Documento Técnico – Infraestructura Administrativa

---

# 1. PROPÓSITO

Definir la arquitectura del panel administrativo exclusivo para:

- Sales Offers
- Bloqueos
- Confirmaciones manuales
- Métricas Atlas
- Control financiero específico de Motor 2

Mission Control (Kommo) permanece independiente.

---

# 2. PRINCIPIO DE SEPARACIÓN

Mission Control:
- Leads
- CRM
- Pipeline
- Conversion

Atlas Control Center:
- Inventario táctico
- Ofertas
- Bloqueos
- Confirmaciones proveedor
- Margen Atlas
- Riesgo operativo

---

# 3. STACK TECNOLÓGICO

Frontend:
- React (Horizons)

Backend:
- Supabase (DB + RPC + RLS)
- n8n (Workflows)
- MCP v3.6.1 (Finanzas)
- Kommo (vía n8n, no directo)

---

# 4. ESTRUCTURA DE RUTAS

/admin/atlas
    /dashboard
    /sales-offers
    /bloqueos
    /confirmaciones
    /finanzas
    /metricas

---

# 5. MÓDULOS PRINCIPALES

## 5.1 Atlas Dashboard

Mostrar:

- Ofertas activas
- Bloqueos activos
- Pending provider confirmations
- Ingreso proyectado Atlas
- Ingreso confirmado Atlas
- % Rechazo proveedor
- Tiempo promedio confirmación

Fuente:
- atlas_offers
- atlas_reservations
- offline_operations

---

## 5.2 Gestión de Sales Offers

Funciones:

- Crear oferta
- Definir vigencia (<= 30 días)
- Definir precio total
- Definir cupo
- Política no reembolsable
- Activar / Desactivar

Regla:
No permitir pagos parciales.

---

## 5.3 Gestión de Bloqueos

Funciones:

- Crear bloqueo
- Definir fechas fijas
- Definir cupo_total
- Permitir abono (si aplica)
- Registrar confirmación proveedor
- Marcar rejected

---

## 5.4 Panel de Confirmaciones

Lista de reservas:

status = pending_provider_confirmation

Acciones:

- Confirmar
- Rechazar
- Ofrecer alternativa

Cada acción:

- Dispara webhook n8n
- Actualiza estado
- Mueve lead en Kommo
- Registra automation_logs

---

## 5.5 Panel Financiero Atlas

Mostrar:

- Ingreso proyectado
- Ingreso confirmado
- Margen proyectado
- Margen real
- Fees bancarios
- % Proyectado vs Confirmado

Fuente:
- offline_operations
- atlas_payments
- proforma_invoices

---

# 6. MODELO DE DATOS CENTRAL

Tabla recomendada:

atlas_offers
- offer_id
- offer_type (sales | bloqueo)
- financial_mode (projected | confirmed_only)
- confirmation_mode (manual | api)
- valid_from
- valid_to
- cupo_total
- cupo_disponible
- status

atlas_reservations
- reservation_id
- offer_id
- client_id
- status
- monto_total
- monto_pagado
- provider_status
- created_at

---

# 7. INTEGRACIÓN CON MCP

Atlas Control no reconoce ingresos.

Solo muestra estados.

El reconocimiento contable ocurre exclusivamente en MCP.

Atlas solo consulta:

get_accounting_dashboard()

---

# 8. SEGURIDAD

- RLS activo en Supabase.
- Solo role 'admin_atlas' puede:
  - Confirmar reservas
  - Modificar cupos
  - Ver margen real
- Logs obligatorios en automation_logs.
- Toda acción crítica requiere registro timestamp + user_id.

---

# 9. CONEXIÓN CON n8n

React no ejecuta lógica crítica.

Flujo:

React → Supabase RPC → n8n Webhook → 
- Actualizar estado
- Notificar proveedor
- Actualizar Kommo
- Registrar log forense

---

# 10. PRINCIPIO DE ESCALABILIDAD

Atlas Control debe poder:

- Operar manual (modo actual)
- Migrar a API (modo futuro)
- Soportar multi-proveedor
- Soportar marca blanca

Sin reescribir arquitectura base.

---

# 11. OBJETIVO FINAL

Atlas Control Center es:

- Centro táctico de inventario
- Motor de margen
- Panel de riesgo operativo
- Sistema de control de exposición

Mission Control sigue siendo CRM.

Ambos conviven sin acoplamiento directo.
