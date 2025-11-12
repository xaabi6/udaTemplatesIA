# Estructura del Proyecto UDA

---

## 📖 Guía de Lectura de este Documento

### 🎯 Elementos de la Estructura

| Elemento | Significado | Ejemplo | Acción |
|----------|-------------|---------|--------|
| 📁 `carpeta/` | Directorio | `src/` | Crear carpeta |
| 📄 `archivo.ext` | Archivo | `pom.xml` | Crear archivo |
| 🔤 `[placeholder]` | Variable | `[proyecto]` | Reemplazar |
| ✅ | Obligatorio | - | Debe existir |
| ⚠️ | Recomendado | - | Debería existir |
| ❌ | Opcional | - | Puede omitirse |

### 💡 Ejemplos Prácticos

#### Caso 1: Proyecto "Sistema de Ventas"

```
❓ Documentación:
   com/uda/[nombre-proyecto]/Application.java

✅ Implementación:
   com/uda/sistema-ventas/Application.java
```

#### Caso 2: Entidad "Cliente"

```
❓ Documentación:
   controller/[Entidad]Controller.java

✅ Implementación:
   controller/ClienteController.java
```

### 📋 Reglas de Nomenclatura

```
Java:     PascalCase.java    → ProductoService.java
React:    PascalCase.jsx     → ProductoList.jsx
Service:  camelCase.js       → productoService.js
SQL:      UPPER_CASE         → PRODUCTOS
```

---

## 📋 Descripción

Este documento define la estructura **exacta** de carpetas y archivos que debe tener una aplicación UDA generada. La IA debe seguir esta estructura al pie de la letra.

---

## 🗂️ Estructura Completa

```
proyecto-uda/
├── README.md
├── .gitignore
├── docker-compose.yml
├── .env.example
│
├── backend/
│   ├── pom.xml
│   └── src/main/
│       ├── java/com/uda/[proyecto]/
│       │   ├── Application.java
│       │   ├── config/          # JacksonConfig, SecurityConfig, etc.
│       │   ├── controller/      # REST Controllers
│       │   ├── service/         # Business logic
│       │   ├── repository/      # Data access
│       │   ├── entity/          # JPA entities
│       │   ├── dto/             # Data transfer objects
│       │   ├── mapper/          # MapStruct mappers
│       │   ├── exception/       # Exception handlers
│       │   └── security/        # JWT, filters
│       └── resources/
│           ├── application.yml
│           ├── application-prod.yml
│           └── db/migration/    # SQL scripts
│
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── index.html
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── theme/              # Material-UI theme
│       ├── pages/              # Page components
│       ├── components/         # Reusable components
│       ├── services/           # API services
│       ├── hooks/              # Custom hooks
│       ├── contexts/           # React contexts
│       └── utils/              # Utilities + validationSchemas.js
│
└── database/
    ├── schema/                 # DDL scripts
    └── data/                   # DML scripts
```

---

## 📁 Descripción de Directorios Principales

### Backend (`backend/`)

| Directorio | Descripción | Obligatorio |
|------------|-------------|-------------|
| `config/` | Configuraciones de Spring (Security, CORS, DB, etc.) | ✅ |
| `controller/` | Controladores REST que exponen endpoints | ✅ |
| `service/` | Lógica de negocio | ✅ |
| `service/impl/` | Implementaciones de servicios | ✅ |
| `repository/` | Repositorios JPA para acceso a datos | ✅ |
| `entity/` | Entidades JPA mapeadas a tablas | ✅ |
| `dto/` | Data Transfer Objects | ✅ |
| `mapper/` | Mappers MapStruct para conversión Entity ↔ DTO | ✅ |
| `exception/` | Excepciones personalizadas y manejadores globales | ✅ |
| `security/` | Componentes de seguridad (JWT, filtros, etc.) | ✅ |
| `specification/` | Specifications para consultas dinámicas | ⚠️ Opcional |
| `aspect/` | Aspectos AOP (logging, auditoría, etc.) | ⚠️ Opcional |
| `util/` | Utilidades y constantes | ⚠️ Opcional |

### Frontend (`frontend/`)

| Directorio | Descripción | Obligatorio |
|------------|-------------|-------------|
| `theme/` | Tema UDA de Material-UI (MUI) | ✅ |
| `pages/` | Componentes de página completa | ✅ |
| `components/` | Componentes reutilizables | ✅ |
| `components/common/` | Componentes comunes a toda la app | ✅ |
| `layouts/` | Layouts de página | ✅ |
| `services/` | Servicios para llamadas API | ✅ |
| `hooks/` | Custom hooks de React | ✅ |
| `contexts/` | Contextos de React para estado global | ✅ |
| `utils/` | Funciones utilitarias | ✅ |
| `styles/` | Configuración de tema y estilos globales | ✅ |
| `__tests__/` | Tests unitarios y de integración | ✅ |

### Database (`database/`)

| Directorio | Descripción | Obligatorio |
|------------|-------------|-------------|
| `schema/` | Scripts DDL para crear estructura | ✅ |
| `data/` | Scripts DML para datos iniciales | ✅ |
| `scripts/` | Scripts de utilidad (backup, restore) | ⚠️ Opcional |

---

## 📄 Archivos Obligatorios

### Raíz del Proyecto

| Archivo | Descripción | Obligatorio |
|---------|-------------|-------------|
| `README.md` | Documentación principal del proyecto | ✅ |
| `.gitignore` | Archivos a ignorar en Git | ✅ |
| `docker-compose.yml` | Configuración de Docker Compose | ✅ |
| `.env.example` | Ejemplo de variables de entorno | ✅ |

### Archivos Críticos

**Backend:**
- `pom.xml` (packaging=war)
- `Application.java` (extends SpringBootServletInitializer)
- `application.yml` (H2)
- `application-prod.yml` (Oracle)
- `JacksonConfig.java`
- `GlobalExceptionHandler.java`

**Frontend:**
- `package.json` (React 18 + MUI 5)
- `main.jsx` (ThemeProvider)
- `theme/` (5 archivos)
- `validationSchemas.js` (Yup)

---

## 🔤 Convenciones de Nomenclatura

### Backend (Java)

```
Clases:           PascalCase          ProductoService, UsuarioController
Interfaces:       PascalCase          ProductoRepository, IProductoService
Métodos:          camelCase           findById(), createProducto()
Variables:        camelCase           productoRepository, nombreUsuario
Constantes:       UPPER_SNAKE_CASE    MAX_RESULTS, DEFAULT_PAGE_SIZE
Paquetes:         lowercase           com.uda.proyecto.service
```

### Frontend (JavaScript/React)

```
Componentes:      PascalCase          ProductoList, UserProfile
Funciones:        camelCase           handleSubmit(), fetchData()
Variables:        camelCase           userData, isLoading
Constantes:       UPPER_SNAKE_CASE    API_BASE_URL, MAX_FILE_SIZE
Archivos JSX:     PascalCase.jsx      ProductoList.jsx, Header.jsx
Archivos JS:      camelCase.js        authService.js, validators.js
```

### Base de Datos (Oracle)

```
Tablas:           UPPER_SNAKE_CASE    PRODUCTOS, USUARIOS
Columnas:         UPPER_SNAKE_CASE    ID, NOMBRE, FECHA_CREACION
Secuencias:       [TABLA]_SEQ         PRODUCTOS_SEQ, USUARIOS_SEQ
Índices:          IDX_[TABLA]_[COL]   IDX_PRODUCTOS_NOMBRE
Primary Key:      PK_[TABLA]          PK_PRODUCTOS
Foreign Key:      FK_[TABLA]_[REF]    FK_PRODUCTOS_CATEGORIA
Unique:           UK_[TABLA]_[COL]    UK_USUARIOS_EMAIL
Check:            CK_[TABLA]_[COL]    CK_PRODUCTOS_PRECIO
```

---

## 📦 Archivos de Configuración Estándar

### `.gitignore` (Raíz)

```gitignore
# IDEs
.idea/
.vscode/
*.iml
*.ipr
*.iws

# OS
.DS_Store
Thumbs.db

# Logs
logs/
*.log

# Environment
.env
.env.local

# Build
target/
dist/
build/
node_modules/

# Temporary
*.tmp
*.temp
```

### `.gitignore` (Backend)

```gitignore
target/
!.mvn/wrapper/maven-wrapper.jar
.mvn/
mvnw
mvnw.cmd

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### Logs ###
logs/
*.log
```

### `.gitignore` (Frontend)

```gitignore
# Dependencies
node_modules/
/.pnp
.pnp.js

# Testing
/coverage

# Production
/dist
/build

# Misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDE
.vscode/
.idea/
```

---

## 🎯 Reglas de Estructura

### 1. Separación de Responsabilidades

- **Backend**: Cada capa (Controller, Service, Repository) en su propio paquete
- **Frontend**: Componentes, páginas, servicios y utilidades separados
- **Database**: Scripts DDL y DML en carpetas diferentes

### 2. Nomenclatura Consistente

- Usar **singular** para entidades: `Producto`, `Usuario`
- Usar **plural** para colecciones: `productos`, `usuarios`
- Prefijos claros: `[Entidad]Controller`, `[Entidad]Service`, `[Entidad]Repository`

### 3. Organización por Funcionalidad

```
✅ CORRECTO:
components/
  ├── producto/
  │   ├── ProductoCard.jsx
  │   ├── ProductoTable.jsx
  │   └── ProductoDialog.jsx
  └── usuario/
      ├── UsuarioCard.jsx
      └── UsuarioTable.jsx

❌ INCORRECTO:
components/
  ├── cards/
  │   ├── ProductoCard.jsx
  │   └── UsuarioCard.jsx
  └── tables/
      ├── ProductoTable.jsx
      └── UsuarioTable.jsx
```

### 4. Tests Junto al Código

```
✅ CORRECTO:
src/
  ├── services/
  │   └── authService.js
  └── __tests__/
      └── services/
          └── authService.test.js

❌ INCORRECTO:
src/
  └── services/
      └── authService.js
tests/
  └── authService.test.js
```

---

## 📋 Checklist de Estructura

Al generar un proyecto, verificar que existen:

### Backend
- [ ] `pom.xml` con todas las dependencias
- [ ] `Application.java` que extiende `SpringBootServletInitializer`
- [ ] Carpetas `config/`, `controller/`, `service/`, `repository/`, `entity/`, `dto/`, `mapper/`, `exception/`, `security/`
- [ ] `application.yml`, `application-prod.yml`
- [ ] Al menos una entidad completa con su Controller, Service, Repository, DTO y Mapper
- [ ] `GlobalExceptionHandler` y excepciones personalizadas
- [ ] Configuración de seguridad con JWT
- [ ] Tests unitarios en `src/test/`

### Frontend
- [ ] `package.json` con todas las dependencias
- [ ] `vite.config.js` y `vitest.config.js`
- [ ] Carpetas `pages/`, `components/`, `services/`, `hooks/`, `contexts/`, `utils/`, `styles/`
- [ ] `main.jsx` y `App.jsx`
- [ ] Configuración de tema en `styles/theme.js`
- [ ] Servicio API configurado en `services/api.js`
- [ ] Contexto de autenticación en `contexts/AuthContext.jsx`
- [ ] Componentes comunes: `Header`, `Footer`, `Loading`, `ErrorBoundary`, `ProtectedRoute`
- [ ] Al menos una página de listado y formulario
- [ ] Tests en `__tests__/`

### Database
- [ ] Scripts de creación de tablas en `schema/`
- [ ] Scripts de datos iniciales en `data/`
- [ ] Secuencias para todas las tablas
- [ ] Índices en columnas frecuentemente consultadas
- [ ] Constraints (PK, FK, UK, CK)

### Raíz
- [ ] `README.md` completo
- [ ] `.gitignore` configurado
- [ ] `docker-compose.yml` funcional
- [ ] `.env.example` documentado
- [ ] Carpeta `docs/` con documentación adicional

---

## 🔗 Referencias

- **ESPECIFICACION_COMPLETA.md**: Detalles técnicos de cada componente
- **REGLAS_GENERACION.md**: Reglas que la IA debe seguir
- **CHECKLIST_CALIDAD.md**: Validación de calidad del código generado
- **docs/**: Documentación detallada por área

---

**Nota**: Esta estructura es **obligatoria** y debe ser seguida exactamente por la IA al generar una aplicación UDA.

---

## ✅ Validación de la Estructura

Para verificar que la estructura generada es correcta, usa [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md).

### Validación Rápida de Estructura

```bash
# Verificar que existen todos los directorios obligatorios
ls -la backend/src/main/java/com/uda/[proyecto]/
# Debe mostrar: config/, controller/, service/, repository/, entity/, dto/, mapper/, exception/

ls -la frontend/src/
# Debe mostrar: pages/, components/, services/, hooks/, contexts/, utils/, styles/

ls -la database/
# Debe mostrar: schema/, data/
```

**Para validación completa:** [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md) - PASO 1

---
```