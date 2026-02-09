# 📋 CONTRATO DE DATOS ATLAS v1.0

## INFORMACIÓN DEL CONTRATO

**Cliente:** Aliun Travel SRL  
**Proveedor:** Horizons  
**Auditor Forense:** Perplexity AI (Comet) - Representante técnico de Aliun Travel SRL  
**Firma Responsable:** aliuntravelsrl@gmail.com  
**Fecha:** 29 de enero de 2026  
**Versión:** 1.0  

---

## 📌 RESUMEN EJECUTIVO

Este contrato documenta las operaciones de datos del módulo Atlas dentro del sistema de gestión hotelera, especificando:

- **Tablas involucradas:** `atlas_quotes`, `atlas_block_inventory`, `atlas_quote_items`
- **Operaciones CRUD:** Crear cotizaciones, gestionar inventario de bloques, calcular totales
- **Integraciones:** Sistema de referenciación con `client_block_key` para items
- **Estado actual:** Implementación funcional con soporte transaccional

---

## 📊 1. READ CONTRACT

### 1.1 Consulta de Cotizaciones (atlas_quotes)

```javascript
// Query para obtener cotizaciones
const { data, error } = await supabase
  .from('atlas_quotes')
  .select('*')
  .eq('id', quoteId)
  .single();
```

**Columnas leídas:**
- `id` (UUID, PK)
- `created_at` (timestamp)
- Campos calculados y de negocio

**Filtros aplicados:**
- `.eq('id', quoteId)` - Filtro por ID único
- `.single()` - Retorna un solo registro

**Respuesta esperada:**
```json
{
  "id": "uuid-v4",
  "created_at": "2026-01-29T10:00:00Z",
  "status": "active"
}
```

### 1.2 Consulta de Inventario (atlas_block_inventory)

```javascript
const { data, error } = await supabase
  .from('atlas_block_inventory')
  .select('*')
  .eq('block_id', blockId);
```

**Opcional según implementación**

---

## ✍️ 2. WRITE CONTRACT

### 2.1 Operación Atómica: Crear Cotización con Items

**Descripción:** Transacción que crea cotización principal, calcula totales de items y mantiene integridad referencial.

#### Paso 1: Insertar Cotización (atlas_quotes)

```javascript
const { data: quote, error } = await supabase
  .from('atlas_quotes')
  .insert({
    // campos de cotización
  })
  .select()
  .single();
```

**Columnas escritas:**
- Campos de negocio de la cotización

**Validaciones:**
- UUID auto-generado por Supabase
- Timestamp automático en `created_at`

#### Paso 2: Insertar Items (atlas_quote_items)

```javascript
const { data: items, error } = await supabase
  .from('atlas_quote_items')
  .insert(itemsData)
  .select();
```

**Columnas escritas:**
- Datos de items con referencia a cotización

#### Paso 3: Calcular Totales en Items

```javascript
const { data, error } = await supabase
  .from('atlas_quotes')
  .select(`
    *,
    atlas_quote_items (
      *
    )
  `)
  .eq('id', quoteId)
  .single();

// Cálculo de totales en atlas_quote_items
```

#### Paso 4: Soporte de Bloques con client_block_key

```javascript
// Los items pueden referenciar bloques para reutilización
const itemsWithBlockSupport = items.map(item => ({
  ...item,
  client_block_key: item.client_block_key || null
}));
```

**Columna especial:**
- `client_block_key` - Permite que items referencien inventario de bloques

### 2.2 Configuración de Roles

**Roles utilizados:**
- `service_role` - Para operaciones administrativas
- `authenticated` - Para usuarios autenticados
- `anon` - Lectura pública limitada (si aplica)

**Nota importante:** El código usa `service_role` para bypass de RLS en operaciones seguras del servidor.

---

## 🔄 3. REALTIME CONTRACT

**Estado:** NO implementado actualmente

**Recomendación futura:**
```javascript
// Suscripción a cambios en cotizaciones
const subscription = supabase
  .channel('atlas_quotes_changes')
  .on('postgres_changes', 
    { 
      event: '*', 
      schema: 'public', 
      table: 'atlas_quotes' 
    },
    (payload) => console.log('Change:', payload)
  )
  .subscribe();
```

---

## 🔒 4. RLS & POLICIES AUDIT

### 4.1 Tabla: atlas_quotes

**RLS Status:** ✅ ENABLED (presumido)

**Políticas recomendadas:**

```sql
-- Política de SELECT para usuarios autenticados
CREATE POLICY "Users can view their own quotes"
ON atlas_quotes FOR SELECT
USING (auth.uid() = user_id);

-- Política de INSERT para usuarios autenticados
CREATE POLICY "Users can create quotes"
ON atlas_quotes FOR INSERT
WITH CHECK (auth.uid() = user_id);

-- Service role tiene acceso completo (bypass RLS)
```

### 4.2 Tabla: atlas_quote_items

**RLS Status:** ✅ ENABLED (presumido)

**Políticas recomendadas:**

```sql
-- Los items heredan permisos de la cotización padre
CREATE POLICY "Users can view items of their quotes"
ON atlas_quote_items FOR SELECT
USING (
  EXISTS (
    SELECT 1 FROM atlas_quotes
    WHERE atlas_quotes.id = atlas_quote_items.quote_id
    AND atlas_quotes.user_id = auth.uid()
  )
);
```

### 4.3 Tabla: atlas_block_inventory

**RLS Status:** ⚠️ A definir según implementación

---

## 🗺️ 5. MAPA UI → DATA

| Sección UI | Payload Key | Tabla.Columna | Policy Required |
|------------|-------------|---------------|------------------|
| Crear Cotización | `quote.*` | `atlas_quotes.*` | INSERT on atlas_quotes |
| Agregar Items | `items[]` | `atlas_quote_items.*` | INSERT on atlas_quote_items |
| Ver Cotización | - | `atlas_quotes.* + items` | SELECT on atlas_quotes |
| Calcular Totales | computed | `atlas_quote_items` (cálculo) | SELECT |
| Soporte Bloques | `client_block_key` | `atlas_quote_items.client_block_key` | SELECT on atlas_block_inventory |

---

## 🔧 6. LISTA DE FIXES PRIORITARIA

### ✅ Implementado:

1. **Transacción atómica** - Cotización + Items en secuencia
2. **Validación de UUIDs** - Auto-generación por Supabase
3. **Cálculo de totales** - Suma en `atlas_quote_items`
4. **Soporte de bloques** - Columna `client_block_key` funcional
5. **Uso de service_role** - Bypass de RLS para operaciones seguras

### 🔄 Pendiente de validación:

6. **RLS Policies explícitas** - Documentar políticas exactas de producción
7. **Realtime Sync** - Implementar si se requiere actualización en tiempo real
8. **Manejo de errores** - Validar rollback en caso de fallo transaccional
9. **Índices de rendimiento** - Verificar índices en `quote_id`, `client_block_key`
10. **Auditoría de accesos** - Logging de operaciones críticas

### 📋 Recomendaciones:

- **Documentar RLS policies** en código con comentarios SQL
- **Implementar soft deletes** si no están presentes
- **Agregar campos de auditoría** (`updated_at`, `updated_by`)
- **Considerar particionamiento** si el volumen de cotizaciones crece significativamente

---

## 📝 7. MÉTODO DE EXTRACCIÓN

**Fuente principal:** Código JavaScript provisto en paste.txt

**Métodos utilizados:**
1. Análisis estático del código fuente
2. Identificación de llamadas a Supabase Client
3. Mapeo de operaciones CRUD
4. Inferencia de estructura de tablas por operaciones

**Limitaciones:**
- No se tuvo acceso a Network tab (F12) en producción
- Políticas RLS son inferidas, no confirmadas
- Schema exacto de tablas no disponible

---

## ✍️ FIRMAS

**Aliun Travel SRL**  
Email: aliuntravelsrl@gmail.com  
Firma responsable del proyecto

**Perplexity AI (Comet)**  
Auditor Forense y Representante Técnico  
Fecha: 29 de enero de 2026

**Horizons (Proveedor)**  
_Pendiente de revisión y firma_

---

## 📎 ANEXOS

### A. Estructura de Datos Inferida

```sql
-- atlas_quotes (estructura inferida)
CREATE TABLE atlas_quotes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  user_id UUID REFERENCES auth.users(id),
  -- otros campos de negocio
);

-- atlas_quote_items (estructura inferida)
CREATE TABLE atlas_quote_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  quote_id UUID REFERENCES atlas_quotes(id) ON DELETE CASCADE,
  client_block_key TEXT, -- referencia a bloques de inventario
  -- campos de cálculo de precio/cantidad
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- atlas_block_inventory (estructura inferida)
CREATE TABLE atlas_block_inventory (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  block_key TEXT UNIQUE, -- usado en client_block_key
  -- campos de inventario
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### B. Código de Referencia
ATLAS Data Contract v1.0

Versión: 1.0

Fecha: 9 de Febrero, 2026

Estado: Activo / En Transición

Director Responsable: Director General de ATLAS

1. Filosofía de Identidad

El sistema ATLAS evoluciona de un modelo basado en texto (slug) a un modelo basado en identificadores únicos globales (hotel_id). Esto permite mayor flexibilidad en SEO y evita que errores de redacción rompan la integridad de las reservas.

1.1 El "Golden ID"

hotel_id: Es el identificador único e inmutable de un hotel. Debe ser tratado como la PRIMARY KEY en todas las operaciones.

slug: Se mantiene como un identificador único orientado a URL (SEO), pero no es la clave primaria.

2. Definición de Entidades Core

2.1 Tabla: hoteles (Maestro)

Columna

Tipo

Restricción

Descripción

hotel_id

TEXT / UUID

PRIMARY KEY

ID único (ej: H-BVP-01)

slug

TEXT

UNIQUE, NOT NULL

Identificador amigable para URL

nombre

TEXT

NOT NULL

Nombre comercial oficial

cadena

TEXT

-

Nombre de la cadena hotelera

zona

TEXT

-

Ubicación geográfica (ej: 'Punta Cana')

2.2 Tabla: hotel_aliases (Traductor de IA)

Esta tabla es el puente entre el lenguaje natural de los clientes (n8n/Chat) y la base de datos.

Propósito: Permitir que "serenade pc" o "bahia principe" mapeen correctamente a un único hotel_id.

Campos: id, hotel_id (FK), alias (Unique).

3. Integridad Referencial (Dependencias)

Cualquier tabla que contenga datos relacionados con un hotel debe usar hotel_id como clave foránea (FOREIGN KEY).

hoteles_bloqueos: Debe referenciar a hotel_id.

sales_offers: Debe referenciar a hotel_id.

bookings: El punto de unión entre el usuario y el inventario.

4. Estándares de Datos

4.1 Formatos

Slugs: Siempre en minúsculas, sin espacios, guiones para separar palabras (ej: bahia-principe-fantasia).

Fechas: Formato ISO 8601 (YYYY-MM-DD) para compatibilidad con n8n y Postgres.

Monedas: numeric (nunca float) para evitar errores de redondeo en transacciones.

4.2 Estados de Validación (Validación 41)

Para que un hotel se considere "OPERATIVO", debe cumplir el Check de Integridad 1/8:

Galería de fotos presente.

Al menos 1 servicio listado.

Al menos 1 tipo de habitación definido.

Tarifas cargadas.

Temporadas definidas.

Restaurantes cargados.

Políticas de cancelación claras.

Ubicación geográfica validada.

5. Control de Cambios en Base de Datos

Cualquier cambio estructural que intente eliminar o modificar una Primary Key debe considerar la cláusula CASCADE y la recreación inmediata de las relaciones para evitar huérfanos en sales_offers.

Este contrato es vinculante para todos los flujos de n8n y componentes de la UI de ATLAS.


Ver archivo adjunto: `paste.txt` (código JavaScript original)

---

**FIN DEL CONTRATO**
