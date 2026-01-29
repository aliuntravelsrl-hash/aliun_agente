docs/SOW_MIGRACION_ADMIN_4.1.md
# 📄 STATEMENT OF WORK (SOW)
# MIGRACIÓN Y CONSOLIDACIÓN DE PANELES ADMINISTRATIVOS

**Proyecto**: Migración Panel Maestro v4.0 → Admin 4.1 Supabase First Edit  
**Cliente**: Aliun Travel SRL  
**Proveedor**: Horizons  
**Auditor Forense**: Perplexity AI (Comet)  
**Fecha**: 29 de enero de 2026  
**Versión**: 2.0

---

## 📊 IDENTIFICACIÓN DE LAS PARTES

### CONTRATANTE
**Aliun Travel SRL**  
📧 Email: aliuntravelsrl@gmail.com  
📍 Ubicación: Anamar, El Seibo, República Dominicana  
👤 Representante Legal: Aliun Travel SRL (Firma Responsable)  
📅 Fecha: 29 de enero de 2026

### AUDITOR FORENSE TÉCNICO
**Perplexity AI (Comet)**  
🔍 Rol: Auditor técnico independiente y representante del contratante  
📋 Función: Supervisión técnica, validación de entregables, control de calidad  
📧 Contacto: A través de aliuntravelsrl@gmail.com  
📅 Fecha: 29 de enero de 2026

### PROVEEDOR TÉCNICO
**Horizons**  
👥 Representante: [Pendiente de asignar]  
📧 Email: [Pendiente de confirmar]  
📅 Fecha: [Por confirmar]

---

## 1️⃣ OBJETO DEL CONTRATO

Migración integral de datos del **Panel Maestro v4.0** (sistema interno de Horizons) hacia el **Admin 4.1 Supabase First Edit** (sistema de producción conectado a base de datos Supabase), bajo supervisión del Auditor Forense Técnico.

### Alcance Principal
- ✅ **116 hoteles activos** a migrar
- ✅ **5 fases técnicas** de implementación
- ✅ **5 días laborables** de duración
- ✅ **Hotel piloto**: `bahia-principe-fantasia`
- ✅ **Consolidación final**: Un único panel Admin 4.1

---

## 2️⃣ ANTECEDENTES

### Situación Actual Identificada

Existen **dos paneles administrativos operando en paralelo**:

| Panel | Función | Conexión DB | Status |
|-------|---------|-------------|--------|
| **Panel Maestro v4.0** | Dashboard estadístico | ❌ NO conectado a Supabase | Datos NO se reflejan en web |
| **Admin Industrial** | Editor granular por hotel | ✅ SÍ conectado a Supabase | Sin uso activo |

### Problema Crítico
Todo el trabajo en Panel Maestro v4.0 **NO está sincronizado** con producción, causando:
- Web pública sin datos actualizados
- Duplicación de esfuerzo
- Riesgo de pérdida de información

### Solución Propuesta
Migrar datos del Panel Maestro v4.0 al Admin conectado a Supabase, incorporar mejoras de UI, y consolidar en **Admin 4.1 Supabase First Edit**.

---

## 3️⃣ ALCANCE TÉCNICO - 5 FASES

### FASE 1: Auditoría y Vaciado de Datos
**Duración**: 4 horas  
**Objetivo**: Transferir datos del Panel Maestro v4.0 al Admin Supabase

#### Actividades:
1. **Auditoría de contenido** en Panel Maestro v4.0
   - Documentar hoteles con datos editados
   - Exportar estructura de datos
   - Generar inventario con checklist

2. **Vaciado manual hotel por hotel**
   - Hotel piloto: `bahia-principe-fantasia`
   - Exportar desde Panel Maestro v4.0
   - Importar a `/admin/hotels/bahia-principe-fantasia`
   - Validar en `/hotel/bahia-principe-fantasia`

3. **Datos a migrar por hotel**:
   - ✅ General: nombre, descripción, ubicación, video_url, check-in/out
   - ✅ Multimedia: imágenes, orden, URLs
   - ✅ Habitaciones: tipos, capacidades, amenidades
   - ✅ Temporadas: fechas, multiplicadores
   - ✅ Tarifas: precios por room + season
   - ✅ Políticas: min_stay, cancelación
   - ✅ Servicios: amenidades, categorías
   - ✅ Gastronomía: restaurantes, bares

#### Entregables:
- [ ] `MIGRATION_AUDIT_CHECKLIST.md` (inventario 116 hoteles)
- [ ] `src/utils/migrationValidator.js` (script de validación)
- [ ] Hotel piloto migrado y validado
- [ ] Reporte CSV con status

---

### FASE 2: Script de Validación Masiva
**Duración**: 3 días  
**Objetivo**: Automatizar validación de los 116 hoteles

#### Actividades:
1. Implementar `migrationValidator.js` con funciones:
   - `validateHotelMigration(slug)` - Validar 1 hotel
   - `validateAllHotels(slugs[])` - Validar lista completa
   - `exportValidationCSV(data)` - Generar reporte

2. Validar que cada hotel tenga:
   - ✅ Multimedia > 0
   - ✅ Rooms > 0
   - ✅ Rates > 0
   - ✅ Seasons > 0
   - ✅ Services > 0
   - ✅ Restaurants ≥ 0
   - ✅ Booking rules > 0

#### Entregables:
- [ ] `src/utils/migrationValidator.js`
- [ ] Tests automatizados
- [ ] Reporte CSV de 116 hoteles

---

### FASE 3: Panel de Validación en Admin
**Duración**: 4 horas  
**Objetivo**: Crear UI para validación visual

#### Actividades:
1. Implementar `MigrationValidationPanel.jsx`:
   - Input para lista de slugs
   - Botón "Iniciar Validación"
   - Grid de estadísticas
   - Tabla de resultados
   - Descarga de reporte CSV

2. Métricas en tiempo real:
   - Total validados
   - % completitud
   - Tiempo transcurrido

#### Entregables:
- [ ] `src/components/admin/MigrationValidationPanel.jsx`
- [ ] Panel en `/admin/dashboard?tab=validacion`
- [ ] Screenshot con datos reales

---

### FASE 4: Actualización Admin v4.0 → v4.1
**Duración**: 1 día  
**Objetivo**: Incorporar mejoras de UI del Panel Maestro v4.0

#### Actividades:
1. **Integrar componentes UI mejorados**:
   - Dashboard con KPIs (solo lectura)
   - Realtime actualizado
   - Tabs mejorados
   - Validadores de formulario
   - Notificaciones toast
   - Modales de confirmación

2. **NO integrar**:
   - Lógica de DB alternativa
   - Conexiones a DB ≠ Supabase
admin/dashboard → KPIs y estadísticas
/admin/hotels → Listado 116 hoteles
/admin/hotels/:slug → Editor (6 tabs)

#### Entregables:
- [ ] Código en rama `migration/admin-4.1`
- [ ] Tests E2E pasando
- [ ] Documentación actualizada

---

### FASE 5: Consolidación y Limpieza
**Duración**: 2 horas  
**Objetivo**: Eliminar Panel Maestro v4.0 y consolidar

#### Actividades:
1. **Verificar migración completa**:
```sql
SELECT COUNT(*) FROM hotels WHERE is_active = true;
-- Esperado: 116

3. **Arquitectura final**:
Confirmar integridad:

Cada hotel tiene datos completos

Web pública muestra todo correctamente

Eliminar código obsoleto:

Panel Maestro v4.0

Rutas duplicadas

Componentes legacy

Renombrar y etiquetar:

Commit: Admin 4.1 Supabase First Edit - Panel Único

Tag: v4.1.0

Entregables:
 Panel Maestro v4.0 eliminado

 Backup guardado (ZIP)

 Deploy en producción

 CONSOLIDATION_REPORT.md

4️⃣ CRONOGRAMA DETALLADO
Fase	Duración	Fecha Inicio	Fecha Fin	Responsable	Auditoría
Auditoría	4h	29 ene 2026	29 ene 2026	Horizons	24h
Piloto	4h	30 ene 2026	30 ene 2026	Horizons	24h
Masivo	3d	31 ene 2026	2 feb 2026	Horizons	24h
Update v4.1	1d	3 feb 2026	3 feb 2026	Horizons	48h
Testing E2E	4h	4 feb 2026	4 feb 2026	Horizons + Aliun	-
Consolidación	2h	4 feb 2026	4 feb 2026	Horizons	24h
Deploy	1h	4 feb 2026	4 feb 2026	Horizons	-
⏰ Tiempo total: 5 días laborables
📅 Fecha límite: 4 de febrero de 2026

5️⃣ CRITERIOS DE ACEPTACIÓN
✅ Fase 1: Hotel Piloto
El trabajo se considera ACEPTADO si:

 https://aliuntravelsrl.com/hotel/bahia-principe-fantasia muestra:

✅ Hero con video

✅ Galería con 8+ imágenes

✅ Gastronomía (restaurantes + bares)

✅ Servicios (amenidades)

✅ Habitaciones (tipos + capacidades)

✅ Políticas (booking rules)

✅ Ubicación con mapa

 Ejecutar en F12 Console:

javascript
window.__NETWORK_AUDIT__.export()
// Debe mostrar requests exitosos (200) a:
// /rest/v1/hotels
// /rest/v1/hotel_multimedia
// /rest/v1/rooms
// /rest/v1/rates
// /rest/v1/seasons
✅ Fase 2-3: Migración Masiva
 116 hoteles con status: COMPLETO en CSV

 Muestreo de 10 hoteles aleatorios pasa validación

 Panel muestra:

Completos: 116 (100%)

Incompletos: 0

Errores: 0

✅ Fase 4-5: Consolidación
 Panel Maestro v4.0 eliminado del código

 Solo existe /admin/hotels/:slug

 Cambios se reflejan en web < 5 seg

 Sin errores 403/401 en Network

6️⃣ RESPONSABILIDADES
🏗️ Horizons (Proveedor)
Obligaciones:

Implementar 5 fases según SOW

Código limpio, comentado, testeado

Testing E2E antes de entrega

Documentar en changelog

Soporte técnico durante migración

Resolver bugs de validación

Backup antes de eliminar

Comunicación:

Reporte diario a aliuntravelsrl@gmail.com

Notificar bloqueos < 2h

Reuniones de seguimiento

🏢 Aliun Travel SRL (Contratante)
Obligaciones:

Proveer accesos (Git, Supabase, Admin)

Aprobar entregables post-auditoría

Responder consultas < 24h

Facilitar reuniones

Comunicación:

Canal: aliuntravelsrl@gmail.com

Tiempo respuesta: < 24h

🔍 Perplexity AI (Auditor Forense)
Obligaciones:

Revisar código de cada commit

Validar criterios de aceptación

Reportes de auditoría por fase

Notificar:

✅ Aprobaciones

❌ Rechazos con motivos

⚠️ Alertas de riesgo

Mediar disputas técnicas

Emitir Certificado de Conformidad

Comunicación:

Reportes diarios vía aliuntravelsrl@gmail.com

Alertas críticas: inmediatas

Reporte final: < 24h post-entrega

7️⃣ PROCESO DE VALIDACIÓN
text
HORIZONS entrega Fase X
        ↓
AUDITOR revisa código (24-48h)
        ↓
        ├─ ✅ APROBADO → Notifica Contratante
        │                     ↓
        │               Contratante confirma
        │                     ↓
        │               Pago liberado (si aplica)
        │
        └─ ❌ RECHAZADO → Lista de correcciones
                               ↓
                         HORIZONS corrige
                               ↓
                         Re-envía para auditoría
Criterios del Auditor
Cada fase APROBADA solo si:

 Código cumple especificaciones

 Tests pasan 100%

 Documentación completa

 Sin hardcoding

 Queries eficientes

 Sin regresiones

 Web refleja cambios

Tiempo de Auditoría
Fases 1-3: **24 horas
