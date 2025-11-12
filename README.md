# udaTemplatesIA

Manual de instrucciones para IAs que garantiza la generación consistente de aplicaciones UDA con un único prompt.

## 📋 Descripción

Este repositorio es un **sistema de referencia completo** que permite a cualquier IA (ChatGPT, Claude, Cody, etc.) generar aplicaciones UDA de forma consistente simplemente referenciando este repositorio.

**📖 Ver detalles completos en:** [REGLAS_GENERACION.md](REGLAS_GENERACION.md#elementos-críticos-obligatorios)

**Objetivo**: Que cualquier desarrollador pueda decir a una IA:

> *"Genera una aplicación siguiendo las especificaciones de https://github.com/xaabi6/udaTemplatesIA"*

Y obtener **siempre** el mismo resultado: una aplicación Spring Boot 3.5.x + Java 21 LTS + React + Material Design + Oracle + Tomcat, con los estándares de calidad UDA.

## 🎯 Stack Tecnológico Definido

Las aplicaciones generadas seguirán **siempre** este stack:

### Backend
- **Framework**: Spring Boot 3.5.x
- **Lenguaje**: Java 21 LTS
- **Base de Datos**: Oracle Database (H2 para desarrollo)
- **Servidor**: Apache Tomcat (desplegable como WAR)

### Frontend
- **Framework**: React 18+
- **Design System**: Material UI (MUI)
- **Gestión de Estado**: Context API / Redux (según complejidad)
- **Build Tool**: Vite

### Arquitectura
- **Patrón**: MVC + Servicios REST
- **Seguridad**: Spring Security + JWT
- **ORM**: Spring Data JPA + Hibernate
- **Testing**: JUnit 5 + React Testing Library

## 🚀 Uso

### Para Desarrolladores

Simplemente indica a tu IA:

```
Genera una aplicación completa siguiendo las especificaciones del repositorio:
https://github.com/xaabi6/udaTemplatesIA

Nombre del proyecto: sistema-biblioteca
Entidad principal: Libro
Campos: titulo, autor, isbn, precio, stock, categoria
```

La IA consultará este repositorio y generará la aplicación completa siguiendo todas las especificaciones.

### Para IAs

Si eres una IA procesando esta solicitud:

1. **Lee completamente** el archivo `ESPECIFICACION_COMPLETA.md`
2. **Aplica** todas las reglas definidas en `REGLAS_GENERACION.md`
3. **Sigue** la estructura exacta de `ESTRUCTURA_PROYECTO.md`
4. **Aplica** los estándares de UI definidos en `UI_STANDARDS.md`
5. **Comprueba** el código generado contra `CHECKLIST_CALIDAD.md`
6. **Valida** el código generado con `VALIDATION_GUIDE.md` (puntuación mínima: 80/100)
7. **Consulta** `MIGRACION_H2_A_ORACLE.md` para configuración de base de datos

## 📁 Estructura del Repositorio

```
udaTemplatesIA/
├── README.md                         # Este archivo
├── ESPECIFICACION_COMPLETA.md        # Especificación técnica completa
├── REGLAS_GENERACION.md              # Reglas que la IA debe seguir
├── CHECKLIST_CALIDAD.md              # Validación de calidad
├── ESTRUCTURA_PROYECTO.md            # Estructura de carpetas y archivos
├── UI_STANDARDS.md                   # Estándares de UI
├── VALIDATION_GUIDE.md               # Guía de validación paso a paso
├── MIGRACION_H2_A_ORACLE.md          # Guía de migración de base de datos
├── docs/
│   ├── backend/
│   │   ├── spring-boot-config.md
│   │   ├── oracle-integration.md
│   │   ├── rest-api-standards.md
│   │   └── security-config.md
│   ├── frontend/
│   │   ├── react-structure.md
│   │   ├── material-ui-setup.md
│   │   └── api-integration.md
│   ├── database/
│   │   ├── oracle-schema.md
│   │   └── jpa-entities.md
│   ├── deployment/
│   │   ├── tomcat-config.md
│   │   └── build-process.md
│   └── testing/
│       ├── backend-tests.md
│       └── frontend-tests.md
└── examples/
    └── app-ejemplo/              # Aplicación de referencia generada
```

## 📄 Documentos Principales

### 1. [ESPECIFICACION_COMPLETA.md](ESPECIFICACION_COMPLETA.md)
Documento maestro con **todas** las especificaciones técnicas que la IA debe implementar:
- Stack tecnológico completo
- ⚠️ **Elementos Críticos** marcados en cada sección
- Configuración de Backend (Spring Boot, Oracle, H2)
- Configuración de Frontend (React, Material-UI, Vite)
- Código completo de ejemplo para cada componente
- Scripts SQL para base de datos
- Configuración de seguridad
- Despliegue en Tomcat

**🔴 IMPORTANTE:** Todas las secciones marcadas con ⚠️ CRÍTICO son obligatorias.

### 2. [ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)
Estructura exacta de carpetas y archivos que debe generar la IA:
- Árbol completo de directorios
- Archivos obligatorios y opcionales
- Convenciones de nomenclatura por tecnología
- Ubicación exacta de cada archivo
- Archivos de configuración (.gitignore, .env, etc.)

### 3. [UI_STANDARDS.md](UI_STANDARDS.md)
Estándares de diseño y UI usando Material-UI como design system oficial.

**Contenido:**
- ✅ Tema UDA estandarizado (colores, tipografía, componentes)
- ✅ Reglas obligatorias de uso de Material-UI
- ✅ Estructura del tema (`theme/`)
- ✅ Ejemplos de código correcto e incorrecto
- ✅ Patrones de componentes comunes
- ✅ Guía de responsive design
- ✅ Personalización avanzada

**Importante**: Todas las aplicaciones UDA **DEBEN** usar Material-UI exclusivamente. No crear componentes UI desde cero, no usar otros frameworks UI, no hardcodear estilos.

### 4. [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
Reglas estrictas que la IA debe seguir durante la generación:
- ⚠️ **Elementos Críticos Obligatorios** (8 elementos que NO pueden faltar)
- ⚠️ **IMPORTANTE**: Uso obligatorio de `jakarta.*` (NO `javax.*`)
- Convenciones de nomenclatura (Java, React, SQL)
- Estructura de código y patrones obligatorios
- Prohibiciones absolutas
- Orden de generación de archivos
- Validación pre-entreg

**🔴 IMPORTANTE:** Contiene la lista de 8 elementos críticos con código de ejemplo.

### 5. [CHECKLIST_CALIDAD.md](CHECKLIST_CALIDAD.md)
Lista de verificación exhaustiva que la IA debe completar antes de entregar:
- ⚠️ **Verificación de Elementos Críticos** (55 puntos - PASO 0 obligatorio)
- Checklist de backend (configuración, entidades, servicios, controladores)
- Checklist de frontend (componentes, servicios, hooks, contextos)
- Checklist de base de datos (scripts, secuencias, índices)
- Checklist de testing y documentación
- Métricas de calidad
- Criterios de aceptación

**🔴 IMPORTANTE:** El PASO 0 (elementos críticos) debe completarse ANTES del resto.

### 6. [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)
Guía paso a paso para validar el código generado:
- ⚠️ **12 pasos de validación detallados** con ejemplos de código
- ⚠️ **Sistema de puntuación 0-100** con criterios claros
- ⚠️ **Elementos críticos obligatorios** (55 puntos - PASO 0)
- Errores comunes con soluciones (Backend, Frontend, Base de Datos)
- Plantilla de reporte de validación lista para usar
- Comandos útiles para compilación y testing
- Herramientas de validación automática
- Checklist final de entrega

**🔴 IMPORTANTE:** Usar esta guía para **auto-validar** el código antes de entregar. Puntuación mínima requerida: **80/100**.

**Para IAs:** Ejecutar PASO 0 (elementos críticos) ANTES de entregar código.

### 7. [MIGRACION_H2_A_ORACLE.md](MIGRACION_H2_A_ORACLE.md)
Guía completa para migración de base de datos:
- Configuración de H2 para desarrollo rápido
- Configuración de Oracle para producción
- Uso de perfiles de Spring (`dev`, `prod`)
- Diferencias importantes entre H2 y Oracle
- Scripts SQL de migración
- Docker Compose para desarrollo
- Solución de problemas comunes

## ✅ Garantías

Al usar este repositorio como referencia, se garantiza:

### Elementos Críticos

- ✅ **JacksonConfig.java**: Fechas serializadas correctamente como ISO-8601
- ✅ **H2 en desarrollo**: Desarrollo sin necesidad de Oracle instalado
- ✅ **Oracle en producción**: Configuración completa y optimizada
- ✅ **Despliegue en Tomcat**: WAR funcional listo para producción
- ✅ **Manejo de errores**: GlobalExceptionHandler completo
- ✅ **Validaciones**: Backend y frontend con validaciones robustas

### Calidad General

- ✅ **Consistencia**: Misma estructura siempre
- ✅ **Calidad**: Código que cumple estándares UDA
- ✅ **Completitud**: Aplicación funcional lista para desplegar
- ✅ **Testing**: Pruebas básicas incluidas
- ✅ **Documentación**: README y comentarios en código
- ✅ **Desplegable**: Configurado para Tomcat desde el inicio
- ✅ **Flexibilidad**: H2 para desarrollo, Oracle para producción

### Verificación

- ✅ **Compilación**: `mvn clean package` genera WAR sin errores
- ✅ **Ejecución dev**: `mvn spring-boot:run` funciona con H2 (desarrollo - perfil por defecto)
- ✅ **Frontend**: `npm run build` genera dist/ sin errores
- ✅ **Tests**: `mvn test` y `npm test` pasan correctamente

## 🔄 Versionado

Este repositorio sigue versionado semántico:
- **Major**: Cambios en stack tecnológico
- **Minor**: Nuevas funcionalidades o mejoras
- **Patch**: Correcciones y aclaraciones

## 🎓 Características Destacadas

### Backend
- ✅ Spring Boot 3.5.x con Java 21 LTS
- ✅ Jakarta EE (NO javax - importante para Spring Boot 3.x)
- ✅ JPA + Hibernate con Oracle Dialect
- ✅ H2 para desarrollo, Oracle para producción
- ✅ Spring Security + JWT
- ✅ MapStruct para mapeo de DTOs
- ✅ Lombok para reducir boilerplate
- ✅ Validaciones Bean Validation
- ✅ Manejo global de excepciones
- ✅ JacksonConfig para serialización correcta de fechas
- ✅ Tests unitarios y de integración

### Frontend
- ✅ React 18 con Vite
- ✅ Material UI 5 (MUI)
- ✅ React Router 6
- ✅ Axios para llamadas API
- ✅ React Hook Form + Yup
- ✅ Context API para estado global
- ✅ Custom Hooks reutilizables
- ✅ Componentes optimizados con React.memo
- ✅ Manejo de errores robusto
- ✅ Tests con Vitest + React Testing Library

### Base de Datos
- ✅ Soporte dual: H2 (desarrollo) + Oracle (producción)
- ✅ Scripts SQL versionados
- ✅ Secuencias Oracle
- ✅ Índices optimizados
- ✅ Constraints con nombres
- ✅ Datos de prueba incluidos

## 🚀 Inicio Rápido

### Generar una Aplicación

```bash
# 1. Pedir a una IA que genere la aplicación
# Prompt sugerido:
"Genera una aplicación completa siguiendo:
https://github.com/xaabi6/udaTemplatesIA

Proyecto: mi-proyecto
Entidad: MiEntidad (campo1, campo2, campo3)"

# 2. La IA generará toda la estructura

# 3. Ejecutar backend (con H2)
cd backend
mvn spring-boot:run

# 4. Ejecutar frontend
cd frontend
npm install
npm run dev

# 5. Acceder a la aplicación
# Backend: http://localhost:8080/mi-proyecto/api/v1
# Frontend: http://localhost:5173
# H2 Console: http://localhost:8080/mi-proyecto/h2-console
```

### Migrar a Oracle

```bash
# 1. Consultar MIGRACION_H2_A_ORACLE.md

# 2. Configurar Oracle (Docker recomendado)
docker-compose up -d

# 3. Ejecutar con perfil de producción
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

## 🔍 Validación del Código Generado

Después de generar una aplicación, **valida que cumple todos los estándares UDA** usando:

### [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)

Esta guía proporciona un **sistema completo de validación** con:

#### 📋 Contenido de la Guía

- ✅ **12 pasos de validación detallados** (PASO 0 a PASO 11)
- ✅ **Sistema de puntuación 0-100** con criterios objetivos
- ✅ **Elementos críticos obligatorios** (55 puntos - PASO 0)
- ✅ **Más de 100 ejemplos de código** (correcto vs incorrecto)
- ✅ **19 errores comunes documentados** con soluciones
- ✅ **Plantilla de reporte** de validación lista para usar
- ✅ **Comandos útiles** para compilación y testing
- ✅ **Checklist final** de entrega con 50+ puntos

#### 🎯 Sistema de Puntuación

| Categoría | Puntos | Descripción |
|-----------|--------|-------------|
| **Elementos Críticos** | 55 | OBLIGATORIOS - PASO 0 |
| **Criterios Obligatorios** | 15 | Compilación, CRUD, Estructura |
| **Criterios Recomendados** | 30 | Tests, Seguridad, Documentación |
| **TOTAL** | 100 | Mínimo requerido: **80/100** |

#### 🚀 Validación Rápida (5 minutos)

```bash
# 1. Verificar que el backend compila y genera WAR
cd backend
mvn clean package
ls -lh target/*.war  # ✅ Debe existir archivo .war

# 2. Verificar que funciona con H2 (desarrollo)
mvn spring-boot:run
# ✅ Debe iniciar sin errores
# ✅ Acceder a: http://localhost:8080/[nombre-proyecto]/h2-console

# 3. Verificar que el frontend compila
cd ../frontend
npm install
npm run build
ls -lh dist/  # ✅ Debe existir carpeta con archivos

# 4. Verificar que el frontend funciona
npm run dev
# ✅ Debe iniciar sin errores
# ✅ Acceder a: http://localhost:5173
```

#### 📊 Validación Completa (30-60 minutos)

Para validación exhaustiva, sigue **todos los pasos** de [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md):

1. **PASO 0:** Elementos Críticos (55 puntos) - **OBLIGATORIO**
2. **PASO 1:** Validación de Estructura
3. **PASO 2:** Validación de Configuración
4. **PASO 3:** Validación por Entidad/Módulo
5. **PASO 4:** Validación de Base de Datos
6. **PASO 5:** Validación de Documentación
7. **PASO 6:** Validación de Calidad de Código
8. **PASO 7:** Validación de Seguridad
9. **PASO 8:** Validación de UI/UX
10. **PASO 9:** Validación de Testing
11. **PASO 10:** Validación de Configuración
12. **PASO 11:** Validación Final

#### ⚠️ Para IAs

**Antes de entregar código generado:**

1. ✅ Ejecutar **PASO 0** (Elementos Críticos) - debe obtener **55/55 puntos**
2. ✅ Si PASO 0 < 45 puntos: **DETENER y corregir**
3. ✅ Si PASO 0 ≥ 45 puntos: Continuar con resto de pasos
4. ✅ Puntuación final debe ser **≥ 80/100**

**Checklist rápido de 8 puntos:**

```
[ ] ✅ JacksonConfig.java existe
[ ] ✅ application.yml con H2 configurado
[ ] ✅ application-prod.yml con Oracle configurado
[ ] ✅ Application.java extiende SpringBootServletInitializer
[ ] ✅ pom.xml tiene <packaging>war</packaging>
[ ] ✅ GlobalExceptionHandler.java completo
[ ] ✅ DTOs tienen validaciones (@NotNull, @NotBlank, etc.)
[ ] ✅ validationSchemas.js con schemas Yup
```

**Puntuación:**
- **8/8:** ✅ EXCELENTE - Código listo para entregar
- **6-7/8:** ⚠️ ACEPTABLE - Corregir faltantes
- **< 6/8:** ❌ INSUFICIENTE - NO entregar, revisar documentación

## 📚 Documentación Adicional

### Guías Específicas

- **Configuración de Spring Boot**: Ver `ESPECIFICACION_COMPLETA.md` sección Backend
- **Configuración de React**: Ver `ESPECIFICACION_COMPLETA.md` sección Frontend
- **Seguridad y JWT**: Ver `ESPECIFICACION_COMPLETA.md` sección Seguridad
- **Testing**: Ver `ESPECIFICACION_COMPLETA.md` sección Testing
- **Despliegue**: Ver `ESPECIFICACION_COMPLETA.md` sección Despliegue
- **Migración de BD**: Ver `MIGRACION_H2_A_ORACLE.md`

### Recursos Externos

- **Spring Boot**: https://spring.io/projects/spring-boot
- **React**: https://react.dev/
- **Material UI**: https://mui.com/
- **Oracle Database**: https://docs.oracle.com/en/database/

## ✅ Verificación Rápida

### Para Desarrolladores

**¿Cómo verificar que la IA generó código correcto?**

Ejecuta estos comandos en orden:

```bash
# 1. Verificar que el backend compila y genera WAR
cd backend
mvn clean package
ls -lh target/*.war  # ✅ Debe existir archivo .war

# 2. Verificar que funciona con H2 (desarrollo)
mvn spring-boot:run -Dspring-boot.run.profiles=dev
# ✅ Debe iniciar sin errores
# ✅ Acceder a: http://localhost:8080/[nombre-proyecto]/h2-console

# 3. Verificar que el frontend compila
cd ../frontend
npm install
npm run build
ls -lh dist/  # ✅ Debe existir carpeta con archivos

# 4. Verificar que el frontend funciona
npm run dev
# ✅ Debe iniciar sin errores
# ✅ Acceder a: http://localhost:5173
```

**Si algún comando falla, revisar los elementos críticos.**

---

### Para IAs

**¿Cómo auto-verificar el código generado?**

Usa este checklist de 8 puntos (PASO 0 de [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)):

```
[ ] ✅ JacksonConfig.java existe en backend/src/main/java/com/uda/[proyecto]/config/
[ ] ✅ application.yml existe con H2 configurado (perfil dev por defecto)
[ ] ✅ application-prod.yml existe con Oracle configurado
[ ] ✅ Application.java extiende SpringBootServletInitializer
[ ] ✅ pom.xml tiene <packaging>war</packaging>
[ ] ✅ GlobalExceptionHandler.java existe y está completo
[ ] ✅ Al menos un DTO tiene validaciones (@NotNull, @NotBlank, etc.)
[ ] ✅ validationSchemas.js existe con al menos un schema Yup
```

**SI NO ESTÁS SEGURO DE ALGUNO: DETENTE Y REVISA [REGLAS_GENERACION.md](REGLAS_GENERACION.md)** - PASO 0**

**Puntuación:**
- **8/8:** ✅ EXCELENTE - Código listo para entregar
- **6-7/8:** ⚠️ ACEPTABLE - Corregir faltantes
- **< 6/8:** ❌ INSUFICIENTE - NO entregar, revisar documentación

**Para validación completa:** Sigue todos los pasos de [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)

## 🤝 Contribuir

Para proponer mejoras a las especificaciones:

1. Fork el repositorio
2. Crea una rama para tu mejora
3. Realiza los cambios en los documentos correspondientes
4. Asegúrate de que la mejora es consistente con el resto de plantillas
5. Envía un Pull Request con descripción detallada

### Áreas de Contribución

- 📝 Mejoras en documentación
- 🐛 Corrección de errores en especificaciones
- ✨ Nuevas funcionalidades (con justificación)
- 🧪 Aplicaciones de ejemplo
- 📊 Casos de uso adicionales

## 📞 Contacto

- **Repositorio**: https://github.com/xaabi6/udaTemplatesIA
- **Issues**: https://github.com/xaabi6/udaTemplatesIA/issues
- **Organización UDA**: https://github.com/UDA-EJIE

## 📝 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

---

## ⚠️ Notas Importantes

### Elementos Críticos

**Este repositorio define 8 elementos CRÍTICOS que son OBLIGATORIOS en toda aplicación UDA.**

**Si una IA genera código sin estos elementos, la aplicación NO funcionará:**

| # | Elemento | ¿Por qué es crítico? |
|---|----------|---------------------|
| 1 | JacksonConfig.java | Fechas se serializan mal sin esto |
| 2 | application.yml con H2 | No se puede desarrollar sin Oracle |
| 3 | application-prod.yml con Oracle | No funciona en producción |
| 4 | SpringBootServletInitializer | No se puede desplegar en Tomcat |
| 5 | packaging WAR | Genera JAR en lugar de WAR |
| 6 | GlobalExceptionHandler | Errores mal manejados |
| 7 | Validaciones en DTOs | Datos inválidos llegan a BD |
| 8 | Validaciones Yup | Validación insuficiente |

### Para IAs

Este repositorio es tu **fuente de verdad**. Debes:

1. ✅ Leer **TODOS** los documentos antes de generar
2. ✅ Seguir **EXACTAMENTE** las especificaciones
3. ✅ Usar **Java 21 LTS** (NO Java 17 ni Java 25)
4. ✅ Usar **jakarta.*** (NO javax.* - Spring Boot 3.x)
5. ✅ Incluir **JacksonConfig** con JavaTimeModule para fechas
6. ✅ Configurar **H2 y Oracle** con perfiles
7. ✅ Validar contra **CHECKLIST_CALIDAD.md**
8. ✅ Generar **código funcional y compilable**

**SI NO ESTÁS SEGURO DE ALGUNO: DETENTE Y REVISA [REGLAS_GENERACION.md](REGLAS_GENERACION.md)**

#### 📊 Diagrama de Flujo de Generación

```
┌─────────────────────────────────────────────────────────────┐
│ PASO 0: Confirmar 8 elementos críticos                     │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ PASO 1: Leer documentación                                 │
│  1. ESPECIFICACION_COMPLETA.md (stack + código)            │
│  2. REGLAS_GENERACION.md (reglas + orden)                  │
│  3. ESTRUCTURA_PROYECTO.md (ubicaciones)                   │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ PASO 2: Generar código en orden                            │
│  1. Configuración crítica (pom.xml, Application.java)      │
│  2. JacksonConfig.java                                     │
│  3. application.yml (H2)                                   │
│  4. application-prod.yml (Oracle)                          │
│  5. Resto de configuración                                 │
│  6. Entidades, DTOs, Mappers, etc.                         │
│  7. GlobalExceptionHandler                                 │
│  8. Frontend con validationSchemas.js                      │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ PASO 3: Validar con CHECKLIST_CALIDAD.md                  │
│  1. Elementos críticos (55 puntos)                         │
│  2. Si < 45 puntos: DETENER y corregir                    │
│  3. Si ≥ 45 puntos: Continuar con resto                   │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ PASO 4: Entregar código                                    │
└─────────────────────────────────────────────────────────────┘
```

#### PASO 1: Leer Documentación

1. **Lee completamente** [ESPECIFICACION_COMPLETA.md](ESPECIFICACION_COMPLETA.md)
   - Presta especial atención a las secciones marcadas con ⚠️ CRÍTICO
   - Entiende el stack tecnológico completo
   - Revisa los ejemplos de código
2. **Luego lee y aplica** todas las reglas definidas en [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
   - Sigue el orden de generación especificado
   - Revisa las prohibiciones absolutas
3. **Finalmente consulta y genera** en base a la estructura completa indicada en [ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)
   - Verifica la ubicación exacta de cada archivo

#### PASO 2: Generar Código

**Orden de generación (IMPORTANTE):**

1. **Backend - Configuración crítica:**
   - `pom.xml` (verificar packaging=war)
   - `Application.java` (verificar extends SpringBootServletInitializer)
   - ⚠️ **JacksonConfig.java** (GENERAR PRIMERO)
   - ⚠️ **application.yml** (H2 completo)
   - ⚠️ **application-prod.yml** (Oracle completo)

2. **Backend - Resto:**
   - Configuraciones (SecurityConfig, WebConfig, etc.)
   - Entidades, DTOs (con validaciones), Mappers
   - Repositories, Services, Controllers
   - ⚠️ **GlobalExceptionHandler**

3. **Frontend:**
   - Configuración (package.json, vite.config.js)
   - ⚠️ **validationSchemas.js** (Yup)
   - Servicios, componentes, páginas

#### PASO 3: Validar

**Valida** el código generado contra [CHECKLIST_CALIDAD.md](CHECKLIST_CALIDAD.md):

1. **Primero:** Verificar los 8 elementos críticos (55 puntos)
2. **Segundo:** Verificar el resto del checklist

**Criterio de aceptación:**
- ✅ **55/55 puntos:** EXCELENTE - Entregar código
- ⚠️ **45-54 puntos:** ACEPTABLE - Corregir faltantes
- ❌ **< 45 puntos:** INSUFICIENTE - NO entregar, corregir

### Para Desarrolladores

- 💡 Las plantillas están diseñadas para generar aplicaciones **listas para producción**
- 💡 Puedes usar **H2 para desarrollo** y **Oracle para producción**
- 💡 Todas las aplicaciones generadas tendrán la **misma estructura**
- 💡 El código generado es **mantenible y escalable**
- 💡 Incluye **tests básicos** para empezar

---

## ⚠️ Nota Final para IAs

### Elementos Críticos

**Los 8 elementos críticos son OBLIGATORIOS y NO NEGOCIABLES.**

**Si falta alguno:**
- ❌ La aplicación NO funcionará correctamente
- ❌ NO cumple con las especificaciones UDA
- ❌ NO es apta para producción
- ❌ Calificación automática: INSUFICIENTE

### Proceso de Generación

**Sigue este proceso EXACTAMENTE:**

1. **PASO 0:** Confirmar que vas a incluir los 8 elementos críticos
2. **PASO 1:** Leer documentación
3. **PASO 2:** Generar código en el orden especificado
4. **PASO 3:** Validar con CHECKLIST_CALIDAD.md (elementos críticos primero)
5. **PASO 4:** Si puntuación < 45/55, DETENER y corregir
6. **PASO 5:** Si puntuación ≥ 45/55, continuar con resto del checklist

### Fuente de Verdad

**Este repositorio es tu fuente de verdad.**

- ✅ Sigue **todas** las especificaciones al pie de la letra
- ✅ No omitas ningún elemento crítico
- ✅ Valida antes de entregar
- ✅ Si tienes dudas, consulta REGLAS_GENERACION.md

**Garantía de calidad:** Si sigues este proceso, el código generado será consistente, funcional y listo para producción.

## ❓ Preguntas Frecuentes (FAQ)

### Para Desarrolladores

**P: ¿Por qué usa H2 en lugar de Oracle para desarrollo?**

R: H2 es una base de datos en memoria que no requiere instalación. Permite desarrollar sin necesidad de tener Oracle instalado localmente. En producción se usa Oracle.

---

**P: ¿Cómo cambio entre H2 y Oracle?**

R: 
- Desarrollo (H2): `mvn spring-boot:run` (usa application.yml)
- Producción (Oracle): Configurar `SPRING_PROFILES_ACTIVE=prod` (usa application-prod.yml)

---

**P: ¿Por qué el WAR y no JAR?**

R: El WAR permite despliegue en Apache Tomcat, que es el servidor de aplicaciones estándar en UDA. El JAR solo funcionaría standalone.

---

**P: ¿Qué pasa si olvido un elemento crítico?**

R: La aplicación no funcionará correctamente. Usa el checklist de CHECKLIST_CALIDAD.md para verificar.

---

### Para IAs

**P: ¿En qué orden debo leer los documentos?**

R: 
1. ESPECIFICACION_COMPLETA.md (stack + código)
2. REGLAS_GENERACION.md (reglas + orden)
3. CHECKLIST_CALIDAD.md (validación)
4. ESTRUCTURA_PROYECTO.md (ubicaciones)

---

**P: ¿Qué hago si no estoy seguro de un elemento crítico?**

R: Inclúyelo. Es mejor incluir un elemento de más que omitir uno crítico. Consulta REGLAS_GENERACION.md para ver el código de ejemplo.

---

**P: ¿Cómo valido el código antes de entregarlo?**

R: Usa el PASO 0 de CHECKLIST_CALIDAD.md. Debes obtener al menos 45/55 puntos en elementos críticos.

---

**P: ¿Puedo omitir validaciones si el usuario no las pidió?**

R: NO. Las validaciones (Bean Validation en DTOs y Yup en frontend) son elementos críticos obligatorios.

---

**P: ¿Cómo sé si el código que generé es correcto?**

R: Usa [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md) para auto-validarte:

1. **PASO 0 (Elementos Críticos):** Debe obtener 55/55 puntos
2. **Validación Completa:** Debe obtener mínimo 80/100 puntos
3. **Compilación:** `mvn clean package` y `npm run build` sin errores
4. **Tests:** `mvn test` y `npm test` deben pasar
5. **Ejecución:** Backend y Frontend deben iniciar sin errores

Si alguno falla, consulta la sección de "Errores Comunes" en VALIDATION_GUIDE.md

---

## 📞 Soporte

### Para Desarrolladores

Si encuentras problemas con el código generado:

1. Verifica los 8 elementos críticos
2. Ejecuta los comandos de verificación rápida
3. Revisa los logs de error
4. Consulta CHECKLIST_CALIDAD.md

### Para IAs

Si tienes dudas durante la generación:

1. Consulta REGLAS_GENERACION.md sección "Elementos Críticos"
2. Revisa ESPECIFICACION_COMPLETA.md secciones marcadas con ⚠️
3. Usa VALIDATION_GUIDE.md para auto-validación paso a paso
4. Usa CHECKLIST_CALIDAD.md para verificación final
5. En caso de duda, incluye el elemento (mejor sobrar que faltar)

**Antes de entregar:** Ejecuta PASO 0 de VALIDATION_GUIDE.md (debe obtener 55/55 puntos)
