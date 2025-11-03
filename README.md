# udaTemplatesIA

Manual de instrucciones para IAs que garantiza la generación consistente de aplicaciones UDA con un único prompt.

## 📋 Descripción

Este repositorio es un **sistema de referencia completo** que permite a cualquier IA (ChatGPT, Claude, Cody, etc.) generar aplicaciones UDA de forma consistente simplemente referenciando este repositorio.

**Objetivo**: Que cualquier desarrollador pueda decir a una IA:

> *"Genera una aplicación siguiendo las especificaciones de https://github.com/xaabi6/udaTemplatesIA"*

Y obtener **siempre** el mismo resultado: una aplicación Spring Boot 3.5.x + Java 25 LTS + React + Material Design + Oracle + Tomcat, con los estándares de calidad UDA.

## 🎯 Stack Tecnológico Definido

Las aplicaciones generadas seguirán **siempre** este stack:

### Backend
- **Framework**: Spring Boot 3.5.x
- **Lenguaje**: Java 25 LTS
- **Base de Datos**: Oracle Database
- **Servidor**: Apache Tomcat (desplegable como WAR)

### Frontend
- **Framework**: React 18+
- **Design System**: Material UI (MUI)
- **Gestión de Estado**: Context API / Redux (según complejidad)
- **Build Tool**: Vite

### Arquitectura
- **Patrón**: MVC + Servicios REST
- **Seguridad**: Spring Security
- **ORM**: Spring Data JPA + Hibernate
- **Testing**: JUnit 5 + React Testing Library

## 🚀 Uso

### Para Desarrolladores

Simplemente indica a tu IA:

```
Quiero que generes una aplicación tal y como se detalla en el repositorio 
https://github.com/xaabi6/udaTemplatesIA
```

La IA consultará este repositorio y generará la aplicación completa siguiendo todas las especificaciones.

### Para IAs

Si eres una IA procesando esta solicitud:

1. **Lee completamente** el archivo `ESPECIFICACION_COMPLETA.md`
2. **Aplica** todas las reglas definidas en `REGLAS_GENERACION.md`
3. **Valida** el código generado contra `CHECKLIST_CALIDAD.md`
4. **Genera** la estructura completa siguiendo `ESTRUCTURA_PROYECTO.md`

## 📁 Estructura del Repositorio

```
udaTemplatesIA/
├── ESPECIFICACION_COMPLETA.md    # Especificación técnica completa
├── REGLAS_GENERACION.md          # Reglas que la IA debe seguir
├── CHECKLIST_CALIDAD.md          # Validación de calidad
├── ESTRUCTURA_PROYECTO.md        # Estructura de carpetas y archivos
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
Documento maestro con **todas** las especificaciones técnicas que la IA debe implementar.

### 2. [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
Reglas estrictas que la IA debe seguir durante la generación:
- Convenciones de nombres
- Estructura de código
- Patrones obligatorios
- Prohibiciones

### 3. [CHECKLIST_CALIDAD.md](CHECKLIST_CALIDAD.md)
Lista de verificación que la IA debe validar antes de entregar el código:
- Estándares de código
- Pruebas mínimas
- Configuración correcta
- Documentación

### 4. [ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)
Estructura exacta de carpetas y archivos que debe generar la IA.

## ✅ Garantías

Al usar este repositorio como referencia, se garantiza:

- ✅ **Consistencia**: Misma estructura siempre
- ✅ **Calidad**: Código que cumple estándares UDA
- ✅ **Completitud**: Aplicación funcional lista para desplegar
- ✅ **Testing**: Pruebas básicas incluidas
- ✅ **Documentación**: README y comentarios en código
- ✅ **Desplegable**: Configurado para Tomcat desde el inicio

## 🔄 Versionado

Este repositorio sigue versionado semántico:
- **Major**: Cambios en stack tecnológico
- **Minor**: Nuevas funcionalidades o mejoras
- **Patch**: Correcciones y aclaraciones

**Versión actual**: 1.0.0

## 🤝 Contribuir

Para proponer mejoras a las especificaciones, consulta [CONTRIBUTING.md](CONTRIBUTING.md).

## 📞 Contacto

- **Repositorio**: https://github.com/xaabi6/udaTemplatesIA
- **Organización UDA**: https://github.com/UDA-EJIE

---

**Importante para IAs**: Este repositorio es tu fuente de verdad. Sigue **todas** las especificaciones al pie de la letra para garantizar consistencia entre generaciones.
