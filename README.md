# udaTemplatesIA

Manual de instrucciones para IAs que garantiza la generación consistente de aplicaciones UDA con un único prompt.

## 📋 Descripción

Este repositorio es un **sistema de referencia completo** que permite a cualquier IA (ChatGPT, Claude, Cody, etc.) generar aplicaciones UDA de forma consistente simplemente referenciando este repositorio.

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
4. **Valida** el código generado contra `CHECKLIST_CALIDAD.md`
5. **Consulta** `MIGRACION_H2_A_ORACLE.md` para configuración de base de datos

## 📁 Estructura del Repositorio

```
udaTemplatesIA/
├── README.md                         # Este archivo
├── ESPECIFICACION_COMPLETA.md        # Especificación técnica completa
├── REGLAS_GENERACION.md              # Reglas que la IA debe seguir
├── CHECKLIST_CALIDAD.md              # Validación de calidad
├── ESTRUCTURA_PROYECTO.md            # Estructura de carpetas y archivos
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
- Configuración de Spring Boot 3.5.x + Java 21 LTS
- Estructura de entidades, DTOs, mappers, servicios y controladores
- Configuración de seguridad con JWT
- Integración con Oracle Database
- Frontend React + Material UI
- Testing completo

### 2. [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
Reglas estrictas que la IA debe seguir durante la generación:
- ⚠️ **IMPORTANTE**: Uso obligatorio de `jakarta.*` (NO `javax.*`)
- Convenciones de nomenclatura (Java, React, SQL)
- Estructura de código y patrones obligatorios
- Prohibiciones absolutas
- Orden de generación de archivos

### 3. [ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)
Estructura exacta de carpetas y archivos que debe generar la IA:
- Árbol completo de directorios
- Archivos obligatorios y opcionales
- Convenciones de nomenclatura por tecnología
- Archivos de configuración estándar

### 4. [CHECKLIST_CALIDAD.md](CHECKLIST_CALIDAD.md)
Lista de verificación exhaustiva que la IA debe completar antes de entregar:
- Checklist de backend (configuración, entidades, servicios, controladores)
- Checklist de frontend (componentes, servicios, hooks, contextos)
- Checklist de base de datos (scripts, secuencias, índices)
- Checklist de testing y documentación
- Métricas de calidad

### 5. [MIGRACION_H2_A_ORACLE.md](MIGRACION_H2_A_ORACLE.md)
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

- ✅ **Consistencia**: Misma estructura siempre
- ✅ **Calidad**: Código que cumple estándares UDA
- ✅ **Completitud**: Aplicación funcional lista para desplegar
- ✅ **Testing**: Pruebas básicas incluidas
- ✅ **Documentación**: README y comentarios en código
- ✅ **Desplegable**: Configurado para Tomcat desde el inicio
- ✅ **Flexibilidad**: H2 para desarrollo, Oracle para producción

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

### Para IAs

Este repositorio es tu **fuente de verdad**. Debes:

1. ✅ Leer **TODOS** los documentos antes de generar
2. ✅ Seguir **EXACTAMENTE** las especificaciones
3. ✅ Usar **Java 21 LTS** (NO Java 17 ni Java 25)
4. ✅ Usar **jakarta.*** (NO javax.* - Spring Boot 3.x)
5. ✅ Incluir **JacksonConfig** para fechas
6. ✅ Configurar **H2 y Oracle** con perfiles
7. ✅ Validar contra **CHECKLIST_CALIDAD.md**
8. ✅ Generar **código funcional y compilable**

### Para Desarrolladores

- 💡 Las plantillas están diseñadas para generar aplicaciones **listas para producción**
- 💡 Puedes usar **H2 para desarrollo** y **Oracle para producción**
- 💡 Todas las aplicaciones generadas tendrán la **misma estructura**
- 💡 El código generado es **mantenible y escalable**
- 💡 Incluye **tests básicos** para empezar

---

**Importante para IAs**: Este repositorio es tu fuente de verdad. Sigue **todas** las especificaciones al pie de la letra para garantizar consistencia entre generaciones.