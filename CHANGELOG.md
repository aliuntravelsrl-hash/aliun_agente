# Changelog

Todos los cambios notables en este proyecto serán documentados en este archivo.

El formato está basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/),
y este proyecto adhiere a [Semantic Versioning](https://semver.org/lang/es/).

## [1.0.0] - 2026-02-21

### ✅ Agregado

#### Backend

- Sistema de Descuento Adicional completo
  - 6 columnas nuevas en `atlas_offers`
  - RPC `rpc_apply_additional_discount` con validaciones enterprise
  - RPC `rpc_get_discount_info` para consultas
  - Vista `v_discount_audit` para reportes
  - Índices de performance `idx_atlas_offers_additional_discount` y `idx_atlas_offers_approved_by`
  
- Validaciones financieras
  - Margen neto mínimo 15% garantizado
  - Umbrales de aprobación: 0-5% (sin aprobación), 5-10% (con aprobación), 10%+ (bloqueado)
  - Cálculos proporcionalmente correctos
  - Protección contra bypass de validaciones

#### Frontend

- **Horizons Dashboard**
  - `HorizonsLayout.jsx` - Layout base con navegación sidebar
  - `DashboardHome.jsx` - Dashboard principal con KPIs y acciones rápidas
  - Estructura modular para 5 módulos principales
  
- **Descuento Adicional**
  - `AdditionalDiscountPanel.jsx` - Panel completo para aplicar descuentos negociados
  - Validaciones en tiempo real
  - Badges de estado visual
  - Integración con backend

- **Marketing Offers**
  - Correcciones en `CreateOfferForm.jsx`
  - Correcciones en `MarketingOffersPanel.jsx`

#### Integración

- Atlas Admin integrado en ALIUNADMIN
- Landing pública `/destinos/ofertas` operativa
- Entrada en sidebar de ALIUNADMIN

#### Documentación

- `INVENTARIO_HORIZONS.md` - Inventario completo de componentes Horizons
- `DESCUENTO_ADICIONAL_CERTIFICACION.md` - Documentación completa del sistema de descuentos
- `AJUSTES_ESTRUCTURA_REAL.md` - Documentación de ajustes a estructura de BD
- `README.md` - Roadmap completo del proyecto
- `CHANGELOG.md` - Historial de cambios

### 🔧 Corregido

- Error 404 en rutas de Atlas Admin
- Error `'cn' is not defined` - Creada función en `/lib/cn.js`
- Vista `v_discount_audit` mostrando valores incorrectos
- RPC usando columnas inexistentes de `payment_methods_config`
- Múltiples errores de sintaxis SQL

### 🧪 Tests

- 11 tests automatizados para descuento adicional
- Tests de validación ejecutados:
  - ✅ Descuento 5% sin aprobación → OK
  - ✅ Descuento 7% sin aprobación → RECHAZADO (correcto)
  - ✅ Descuento 12% → RECHAZADO (correcto)
  - ✅ Sin motivo → RECHAZADO (correcto)
  - ✅ Remover descuento → OK

## [0.9.0] - 2026-02-20

### ✅ Agregado

- Bloque A (Core Financiero) completo
  - 5 tablas principales
  - 6 RPCs operativos
  - Trigger de confirmación validado

- Marketing Backend completo
  - Tabla `marketing_offers`
  - RPCs para gestión de ofertas
  - Triggers de validación

### 🔧 Corregido

- Correcciones de auditoría ChatGPT aplicadas
- Validaciones sobre `net_margin` en lugar de `gross_margin`
- Fees dinámicos desde `payment_methods_config`

---

## Leyenda

- ✅ **Agregado** - Nuevas funcionalidades
- 🔧 **Corregido** - Corrección de bugs
- 🔄 **Cambiado** - Cambios en funcionalidad existente
- ⚠️ **Deprecado** - Funcionalidades que se eliminarán
- 🗑️ **Eliminado** - Funcionalidades eliminadas
- 🛡️ **Seguridad** - Correcciones de seguridad
- 🧪 **Tests** - Agregados o cambios en tests
