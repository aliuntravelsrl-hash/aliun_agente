# 📝 Guía de Commits y Estructura de Repositorio

## 🎯 Commits Recomendados para Este Update

### Commit Principal (Recomendado)

```bash
git add .
git commit -m "feat: implement complete additional discount system and horizons dashboard

✅ Backend:
- Add 6 columns to atlas_offers for additional discounts
- Implement rpc_apply_additional_discount with enterprise validations
- Implement rpc_get_discount_info for queries
- Create v_discount_audit view for reporting
- Add performance indexes
- Guarantee 15% minimum net margin
- Implement approval thresholds (0-5%, 5-10%, 10%+)

✅ Frontend:
- Create HorizonsLayout.jsx with sidebar navigation
- Create DashboardHome.jsx with KPIs and quick actions
- Create AdditionalDiscountPanel.jsx for discount management
- Fix CreateOfferForm.jsx and MarketingOffersPanel.jsx

✅ Integration:
- Integrate Atlas Admin in ALIUNADMIN sidebar
- Fix 404 errors and 'cn' function issue
- Public landing /destinos/ofertas operational

✅ Tests:
- 11 automated tests for additional discount
- All validation tests passing (5%, 7%, 12%, no reason)

✅ Documentation:
- Complete INVENTARIO_HORIZONS.md
- Complete DESCUENTO_ADICIONAL_CERTIFICACION.md
- Complete README.md roadmap
- Complete CHANGELOG.md

🎯 Status: Backend 60% | Frontend 40% | Production Ready
"
```

---

### Commits Separados por Módulo (Alternativa)

Si prefieres commits más granulares:

#### 1. Backend - Descuento Adicional

```bash
git add database/ backend/
git commit -m "feat(backend): implement additional discount system with enterprise validations

- Add 6 columns to atlas_offers table
- Create rpc_apply_additional_discount function
- Create rpc_get_discount_info function
- Create v_discount_audit view
- Add performance indexes
- Implement 15% minimum margin validation
- Implement approval thresholds (0-5%, 5-10%, 10%+)
- Add comprehensive error handling

Tests: 11/11 passing
Status: Production Ready ✅
"
```

#### 2. Frontend - Horizons Dashboard

```bash
git add components/layouts/ pages/horizons/
git commit -m "feat(frontend): create horizons dashboard base structure

- Create HorizonsLayout.jsx with collapsible sidebar
- Create DashboardHome.jsx with KPIs and quick actions
- Implement modular structure for 5 main modules
- Add navigation between modules
- Integrate with existing Marketing module

Components: 2 new, 12 total ready
Status: Ready for integration
"
```

#### 3. Frontend - Descuento Adicional UI

```bash
git add components/atlas/
git commit -m "feat(frontend): implement additional discount management panel

- Create AdditionalDiscountPanel.jsx
- Add real-time validations
- Add visual status badges
- Implement backend integration
- Add approval workflow UI

Status: Ready to integrate in offer detail page
"
```

#### 4. Fixes y Correcciones

```bash
git add lib/cn.js atlas-admin/
git commit -m "fix: resolve 404 errors and missing cn function

- Create cn function in /lib/cn.js
- Fix Atlas Admin route registration
- Fix v_discount_audit view calculations
- Correct RPC to use actual database columns

Issues resolved: 5
"
```

#### 5. Documentación

```bash
git add docs/ *.md
git commit -m "docs: add comprehensive project documentation

- Add README.md with complete roadmap
- Add CHANGELOG.md with version history
- Add INVENTARIO_HORIZONS.md with component inventory
- Add DESCUENTO_ADICIONAL_CERTIFICACION.md with certification
- Add AJUSTES_ESTRUCTURA_REAL.md with database adjustments

Documentation coverage: 80%
"
```

---

## 📁 Estructura de Repositorio Recomendada

```
proyecto-atlas/
│
├── .github/
│   ├── ISSUE_TEMPLATE/
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── workflows/
│       └── ci.yml
│
├── docs/
│   ├── README.md (este archivo)
│   ├── CHANGELOG.md
│   ├── INVENTARIO_HORIZONS.md
│   ├── DESCUENTO_ADICIONAL_CERTIFICACION.md
│   ├── AJUSTES_ESTRUCTURA_REAL.md
│   └── architecture/
│       ├── backend.md
│       ├── frontend.md
│       └── database-schema.md
│
├── database/
│   ├── migrations/
│   │   ├── 001_initial_schema.sql
│   │   ├── 002_marketing_offers.sql
│   │   └── 003_additional_discount.sql
│   ├── functions/
│   │   ├── rpc_apply_additional_discount.sql
│   │   ├── rpc_get_discount_info.sql
│   │   └── rpc_get_atlas_dashboard.sql
│   ├── views/
│   │   ├── v_discount_audit.sql
│   │   └── v_marketing_active_offers.sql
│   └── triggers/
│       ├── validate_marketing_offer.sql
│       └── offer_confirmed_trigger.sql
│
├── components/
│   ├── layouts/
│   │   └── HorizonsLayout.jsx
│   ├── atlas/
│   │   └── AdditionalDiscountPanel.jsx
│   ├── marketing/
│   │   ├── CreateOfferForm.jsx
│   │   └── MarketingOffersPanel.jsx
│   └── ui/
│       └── (componentes reutilizables)
│
├── pages/ (o app/ si es Next.js 13+)
│   ├── horizons/
│   │   ├── index.js
│   │   └── marketing/
│   │       └── offers/
│   │           ├── index.js
│   │           └── new.js
│   └── destinos/
│       └── ofertas/
│           └── index.js
│
├── lib/
│   ├── supabaseClient.js
│   ├── cn.js
│   └── utils.js
│
├── hooks/
│   ├── usePaymentMethodsConfig.js
│   └── useFinancialValidation.js
│
├── services/
│   └── marketingService.js
│
├── __tests__/
│   ├── marketingValidation.test.js
│   └── descuento_adicional.test.js
│
├── public/
│   └── assets/
│
├── .gitignore
├── package.json
├── README.md
└── CHANGELOG.md
```

---

## 🏷️ Convención de Commits

Seguimos [Conventional Commits](https://www.conventionalcommits.org/):

### Formato

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: Nueva funcionalidad
- `fix`: Corrección de bug
- `docs`: Cambios en documentación
- `style`: Cambios de formato (no afectan el código)
- `refactor`: Refactorización de código
- `perf`: Mejoras de performance
- `test`: Agregar o modificar tests
- `chore`: Cambios en build, CI/CD, etc.

### Scopes

- `backend`: Cambios en base de datos, RPCs, triggers
- `frontend`: Cambios en componentes React
- `ui`: Cambios en componentes de UI
- `api`: Cambios en APIs
- `docs`: Cambios en documentación
- `tests`: Cambios en tests

### Ejemplos

```bash
feat(backend): add rpc_apply_additional_discount function
fix(frontend): resolve cn function not defined error
docs: update README with complete roadmap
test(backend): add 11 tests for discount validation
refactor(ui): improve AdditionalDiscountPanel layout
```

---

## 📦 Estrategia de Versionado

Usamos [Semantic Versioning](https://semver.org/):

```
MAJOR.MINOR.PATCH

1.0.0
│ │ └─ Patch: Bug fixes
│ └─── Minor: New features (backward compatible)
└───── Major: Breaking changes
```

### Versiones Actuales

- **v1.0.0** - Sistema de Descuento Adicional + Horizons Base (21 Feb 2026)
- **v0.9.0** - Marketing Offers + Bloque A Core (20 Feb 2026)

---

## 🔀 Estrategia de Branches

### Main Branches

- `main` - Código en producción
- `develop` - Código en desarrollo (integración)

### Feature Branches

- `feature/additional-discount` - Descuento adicional
- `feature/horizons-dashboard` - Dashboard Horizons
- `feature/sales-module` - Módulo de ventas
- `feature/ai-agent-integration` - Integración agente IA

### Flujo de Trabajo

```bash
# Crear feature branch
git checkout -b feature/additional-discount

# Trabajar en la feature
git add .
git commit -m "feat(backend): add discount validation"

# Actualizar desde develop
git checkout develop
git pull origin develop
git checkout feature/additional-discount
git merge develop

# Push feature
git push origin feature/additional-discount

# Crear Pull Request en GitHub
# Después de review y aprobación, merge a develop
```

---

## 📋 Pull Request Template

```markdown
## Descripción
Breve descripción de los cambios

## Tipo de Cambio
- [ ] Nueva funcionalidad (feat)
- [ ] Corrección de bug (fix)
- [ ] Documentación (docs)
- [ ] Refactorización (refactor)
- [ ] Tests (test)

## Checklist
- [ ] Código testeado
- [ ] Tests pasan
- [ ] Documentación actualizada
- [ ] Sin errores de lint
- [ ] Revisado por al menos 1 persona

## Screenshots (si aplica)
```

---

## 🚀 Comandos Útiles

```bash
# Ver estado
git status

# Ver cambios
git diff

# Agregar todos los cambios
git add .

# Commit con mensaje
git commit -m "feat: add new feature"

# Push a branch
git push origin feature/branch-name

# Ver historial
git log --oneline --graph

# Crear tag de versión
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0

# Revertir último commit (mantener cambios)
git reset --soft HEAD~1

# Revertir último commit (descartar cambios)
git reset --hard HEAD~1
```

---

## ✅ Checklist Pre-Commit

- [ ] Código funciona localmente
- [ ] Tests pasan (`npm test`)
- [ ] No hay console.logs innecesarios
- [ ] Código formateado (`npm run format`)
- [ ] No hay errores de lint (`npm run lint`)
- [ ] Documentación actualizada
- [ ] CHANGELOG.md actualizado
- [ ] Mensaje de commit descriptivo

---

**Última actualización:** 21 de Febrero 2026
