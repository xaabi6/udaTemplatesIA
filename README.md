# udaTemplatesIA

Manual de instrucciones para IAs que garantiza la generación consistente de aplicaciones UDA con un único prompt.

## 📋 Descripción

Este repositorio es un **sistema de referencia completo** que permite a cualquier IA (ChatGPT, Claude, Cody, etc.) generar aplicaciones UDA de forma consistente simplemente referenciando este repositorio.

**📖 Ver detalles completos en:** [REGLAS_GENERACION.md](REGLAS_GENERACION.md#elementos-críticos-obligatorios)

**Objetivo**: Que cualquier desarrollador pueda decir a una IA:

> *"Genera una aplicación siguiendo las especificaciones de https://github.com/xaabi6/udaTemplatesIA"*

Y obtener **siempre** el mismo resultado: una aplicación Spring Boot 3.5.x + Java 21 LTS + React + Material Design + Oracle + Tomcat, con los estándares de calidad UDA.

## 🎯 Stack Tecnológico Definido

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

```
Genera una aplicación completa siguiendo las especificaciones del repositorio:
https://github.com/xaabi6/udaTemplatesIA

Nombre del proyecto: sistema-biblioteca
Entidad principal: Libro
Campos: titulo, autor, isbn, precio, stock, categoria
```

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
├── docs/                             # Documentación técnica detallada
└── examples/                         # Aplicaciones de referencia
```

## 📄 Documentos Principales

### 1. [ESPECIFICACION_COMPLETA.md](ESPECIFICACION_COMPLETA.md)
Documento maestro con **todas** las especificaciones técnicas que la IA debe implementar.

**🔴 IMPORTANTE:** Todas las secciones marcadas con ⚠️ CRÍTICO son obligatorias.

### 2. [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
Reglas estrictas que la IA debe seguir durante la generación:
- ⚠️ **Elementos Críticos Obligatorios** (8 elementos que NO pueden faltar)
- ⚠️ **IMPORTANTE**: Uso obligatorio de `jakarta.*` (NO `javax.*`)
- Convenciones de nomenclatura y patrones obligatorios
- Prohibiciones absolutas y orden de generación

**🔴 IMPORTANTE:** Contiene la lista de 8 elementos críticos con código de ejemplo.

### 3. [CHECKLIST_CALIDAD.md](CHECKLIST_CALIDAD.md)
Lista de verificación exhaustiva que la IA debe completar antes de entregar:
- ⚠️ **Verificación de Elementos Críticos** (55 puntos - PASO 0 obligatorio)
- Checklist completo de backend, frontend, base de datos y testing

**🔴 IMPORTANTE:** El PASO 0 (elementos críticos) debe completarse ANTES del resto.

### 4. [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)
Guía paso a paso para validar el código generado:
- ⚠️ **6 pasos de validación detallados** con ejemplos de código
- ⚠️ **Sistema de puntuación 0-100** con criterios claros
- Errores comunes con soluciones y plantilla de reporte

**🔴 IMPORTANTE:** Usar esta guía para **auto-validar** el código antes de entregar. Puntuación mínima requerida: **80/100**.

### 5. [ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)
Estructura exacta de carpetas y archivos que debe generar la IA.

### 6. [UI_STANDARDS.md](UI_STANDARDS.md)
Estándares de diseño y UI usando Material-UI como design system oficial.

### 7. [MIGRACION_H2_A_ORACLE.md](MIGRACION_H2_A_ORACLE.md)
Guía completa para migración de base de datos entre H2 (desarrollo) y Oracle (producción).

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

## 🚀 Inicio Rápido

### Generar una Aplicación

```bash
# 1. Pedir a una IA que genere la aplicación
"Genera una aplicación completa siguiendo:
https://github.com/xaabi6/udaTemplatesIA

Proyecto: mi-proyecto
Entidad: MiEntidad (campo1, campo2, campo3)"

# 2. Ejecutar backend (con H2)
cd backend
mvn spring-boot:run

# 3. Ejecutar frontend
cd frontend
npm install
npm run dev

# 4. Acceder a la aplicación
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

Después de generar una aplicación, **valida que cumple todos los estándares UDA** usando [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md).

### Sistema de Puntuación

| Categoría | Puntos | Descripción |
|-----------|--------|-------------|
| **Elementos Críticos** | 55 | OBLIGATORIOS - PASO 0 |
| **Criterios Obligatorios** | 15 | Compilación, CRUD, Estructura |
| **Criterios Recomendados** | 30 | Tests, Seguridad, Documentación |
| **TOTAL** | 100 | Mínimo requerido: **80/100** |

### Validación Rápida (5 minutos)

```bash
# 1. Verificar que el backend compila y genera WAR
cd backend && mvn clean package
ls -lh target/*.war  # ✅ Debe existir archivo .war

# 2. Verificar que funciona con H2 (desarrollo)
mvn spring-boot:run
# ✅ Acceder a: http://localhost:8080/[nombre-proyecto]/h2-console

# 3. Verificar que el frontend compila
cd ../frontend && npm install && npm run build
ls -lh dist/  # ✅ Debe existir carpeta con archivos

# 4. Verificar que el frontend funciona
npm run dev
# ✅ Acceder a: http://localhost:5173
```

## ⚠️ Elementos Críticos Obligatorios

**Este repositorio define 8 elementos CRÍTICOS que son OBLIGATORIOS en toda aplicación UDA.**

Ver detalles completos en [REGLAS_GENERACION.md](REGLAS_GENERACION.md#elementos-críticos-obligatorios).

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

## 📊 Para IAs: Proceso de Generación

### Checklist Rápido de Validación

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

### Flujo de Generación

```
PASO 0: Confirmar 8 elementos críticos
    ↓
PASO 1: Leer documentación (ESPECIFICACION_COMPLETA.md, REGLAS_GENERACION.md)
    ↓
PASO 2: Generar código en orden (configuración crítica primero)
    ↓
PASO 3: Validar con CHECKLIST_CALIDAD.md (mínimo 45/55 puntos)
    ↓
PASO 4: Entregar código
```

**SI NO ESTÁS SEGURO DE ALGUNO: DETENTE Y REVISA [REGLAS_GENERACION.md](REGLAS_GENERACION.md)**

## ❓ Preguntas Frecuentes

### Para Desarrolladores

**P: ¿Por qué usa H2 en lugar de Oracle para desarrollo?**
R: H2 es una base de datos en memoria que no requiere instalación. Permite desarrollar sin necesidad de tener Oracle instalado localmente.

**P: ¿Cómo cambio entre H2 y Oracle?**
R: 
- Desarrollo (H2): `mvn spring-boot:run` (usa application.yml)
- Producción (Oracle): Configurar `SPRING_PROFILES_ACTIVE=prod` (usa application-prod.yml)

**P: ¿Por qué el WAR y no JAR?**
R: El WAR permite despliegue en Apache Tomcat, que es el servidor de aplicaciones estándar en UDA.

### Para IAs

**P: ¿En qué orden debo leer los documentos?**
R: 
1. ESPECIFICACION_COMPLETA.md (stack + código)
2. REGLAS_GENERACION.md (reglas + orden)
3. CHECKLIST_CALIDAD.md (validación)
4. ESTRUCTURA_PROYECTO.md (ubicaciones)

**P: ¿Qué hago si no estoy seguro de un elemento crítico?**
R: Inclúyelo. Es mejor incluir un elemento de más que omitir uno crítico. Consulta REGLAS_GENERACION.md para ver el código de ejemplo.

**P: ¿Puedo omitir validaciones si el usuario no las pidió?**
R: NO. Las validaciones (Bean Validation en DTOs y Yup en frontend) son elementos críticos obligatorios.

## 📞 Soporte

### Para Desarrolladores
Si encuentras problemas con el código generado:
1. Verifica los 8 elementos críticos
2. Ejecuta los comandos de verificación rápida
3. Consulta CHECKLIST_CALIDAD.md

### Para IAs
Si tienes dudas durante la generación:
1. Consulta REGLAS_GENERACION.md sección "Elementos Críticos"
2. Revisa ESPECIFICACION_COMPLETA.md secciones marcadas con ⚠️
3. Usa VALIDATION_GUIDE.md para auto-validación paso a paso
4. En caso de duda, incluye el elemento (mejor sobrar que faltar)

**Antes de entregar:** Ejecuta PASO 0 de VALIDATION_GUIDE.md (debe obtener 55/55 puntos)

## 🤝 Contribuir

Para proponer mejoras:
1. Fork el repositorio
2. Crea una rama para tu mejora
3. Realiza los cambios en los documentos correspondientes
4. Envía un Pull Request con descripción detallada

## 📞 Contacto

- **Repositorio**: https://github.com/xaabi6/udaTemplatesIA
- **Issues**: https://github.com/xaabi6/udaTemplatesIA/issues
- **Organización UDA**: https://github.com/UDA-EJIE

## 📝 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

---

## ⚠️ Nota Final

### Para IAs

**Los 8 elementos críticos son OBLIGATORIOS y NO NEGOCIABLES.**

**Este repositorio es tu fuente de verdad:**
- ✅ Sigue **todas** las especificaciones al pie de la letra
- ✅ No omitas ningún elemento crítico
- ✅ Valida antes de entregar
- ✅ Si tienes dudas, consulta REGLAS_GENERACION.md

**Garantía de calidad:** Si sigues este proceso, el código generado será consistente, funcional y listo para producción.

### Para Desarrolladores

- 💡 Las plantillas están diseñadas para generar aplicaciones **listas para producción**
- 💡 Puedes usar **H2 para desarrollo** y **Oracle para producción**
- 💡 Todas las aplicaciones generadas tendrán la **misma estructura**
- 💡 El código generado es **mantenible y escalable**
- 💡 Incluye **tests básicos** para empezar