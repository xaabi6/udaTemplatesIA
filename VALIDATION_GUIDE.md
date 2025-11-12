# Guía de Validación UDA para IAs

## 📋 Propósito

Este documento proporciona **instrucciones de auto-validación** para que las IAs verifiquen que el código generado cumple con todos los estándares UDA definidos en:

- `ESPECIFICACION_COMPLETA.md`
- `ESTRUCTURA_PROYECTO.md`
- `UI_STANDARDS.md`
- `REGLAS_GENERACION.md`
- `CHECKLIST_CALIDAD.md`

**Audiencia**: Inteligencias Artificiales (ChatGPT, Claude, Cody, etc.)

---

## 🎯 Cuándo Validar

### Durante la Generación
Validar **cada componente** inmediatamente después de generarlo.

### Al Finalizar
Validar **toda la aplicación** antes de entregarla al usuario.

---

## ⚠️ PASO 0: Elementos Críticos (OBLIGATORIO)

**ANTES de continuar, verificar los 8 elementos críticos:**

**📖 Ver detalles completos en:** [REGLAS_GENERACION.md](REGLAS_GENERACION.md#elementos-críticos-obligatorios)

### Checklist Rápido (8 puntos)

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

**Puntuación:**
- **8/8:** ✅ EXCELENTE - Código listo para entregar
- **6-7/8:** ⚠️ ACEPTABLE - Corregir faltantes
- **< 6/8:** ❌ INSUFICIENTE - NO entregar, revisar documentación

**SI LA PUNTUACIÓN ES < 6/8: NO CONTINUAR**

---

## ✅ PASO 1: Validación de Estructura

### Backend - Estructura de Carpetas

**Verificar que existen:**

```
backend/
├── src/main/java/com/uda/[proyecto]/
│   ├── Application.java                    ✅ OBLIGATORIO
│   ├── config/
│   │   ├── JacksonConfig.java             ✅ OBLIGATORIO
│   │   ├── SecurityConfig.java            ✅ OBLIGATORIO
│   │   └── WebConfig.java                 ✅ OBLIGATORIO
│   ├── controller/                        ✅ OBLIGATORIO
│   ├── service/
│   │   └── impl/                          ✅ OBLIGATORIO
│   ├── repository/                        ✅ OBLIGATORIO
│   ├── entity/                            ✅ OBLIGATORIO
│   ├── dto/                               ✅ OBLIGATORIO
│   ├── mapper/                            ✅ OBLIGATORIO
│   └── exception/
│       ├── GlobalExceptionHandler.java    ✅ OBLIGATORIO
│       ├── ResourceNotFoundException.java ✅ OBLIGATORIO
│       └── ErrorResponse.java             ✅ OBLIGATORIO
├── src/main/resources/
│   ├── application.yml                    ✅ OBLIGATORIO
│   └── application-prod.yml               ✅ OBLIGATORIO
└── pom.xml                                ✅ OBLIGATORIO
```

**Checklist:**
- [ ] Todas las carpetas obligatorias existen
- [ ] Todos los archivos obligatorios existen
- [ ] Nombres de paquetes en minúsculas
- [ ] Estructura coincide con `ESTRUCTURA_PROYECTO.md`

---

### Frontend - Estructura de Carpetas

**Verificar que existen:**

```
frontend/
├── src/
│   ├── main.jsx                           ✅ OBLIGATORIO
│   ├── App.jsx                            ✅ OBLIGATORIO
│   ├── theme/
│   │   ├── index.js                       ✅ OBLIGATORIO
│   │   ├── palette.js                     ✅ OBLIGATORIO
│   │   ├── typography.js                  ✅ OBLIGATORIO
│   │   ├── components.js                  ✅ OBLIGATORIO
│   │   └── shadows.js                     ✅ OBLIGATORIO
│   ├── pages/                             ✅ OBLIGATORIO
│   ├── components/
│   │   └── common/                        ✅ OBLIGATORIO
│   ├── services/
│   │   └── api.js                         ✅ OBLIGATORIO
│   ├── hooks/                             ✅ OBLIGATORIO
│   ├── contexts/                          ✅ OBLIGATORIO
│   └── utils/
│       └── validationSchemas.js           ✅ OBLIGATORIO
├── package.json                           ✅ OBLIGATORIO
├── vite.config.js                         ✅ OBLIGATORIO
└── index.html                             ✅ OBLIGATORIO
```

**Checklist:**
- [ ] Todas las carpetas obligatorias existen
- [ ] Todos los archivos obligatorios existen
- [ ] Tema UDA completo en `src/theme/` (5 archivos)
- [ ] `validationSchemas.js` existe
- [ ] Estructura coincide con `ESTRUCTURA_PROYECTO.md`

---

## ✅ PASO 2: Validación de Configuración

### Backend - pom.xml

**Verificar versiones exactas:**

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.0</version>                    ✅ Exactamente 3.5.0
</parent>

<properties>
    <java.version>21</java.version>             ✅ Java 21
    <mapstruct.version>1.6.3</mapstruct.version>  ✅ MapStruct 1.6.3
    <lombok.version>1.18.34</lombok.version>    ✅ Lombok 1.18.34
</properties>

<packaging>war</packaging>                      ✅ WAR
```

**Dependencias obligatorias verificar que existen:**
- [ ] `spring-boot-starter-web`
- [ ] `spring-boot-starter-data-jpa`
- [ ] `spring-boot-starter-security`
- [ ] `spring-boot-starter-validation`
- [ ] `spring-boot-starter-tomcat` con `scope=provided`
- [ ] `ojdbc11` (Oracle JDBC)
- [ ] `h2` con `scope=runtime`
- [ ] `lombok`
- [ ] `mapstruct`
- [ ] `jjwt-api`, `jjwt-impl`, `jjwt-jackson`
- [ ] `spring-boot-starter-test`

**Annotation Processors:**
- [ ] Lombok processor configurado
- [ ] MapStruct processor configurado
- [ ] `<finalName>` sin versión

---

### Frontend - package.json

**Verificar versiones:**

```json
{
  "dependencies": {
    "react": "^18.3.0",                    ✅ React 18+
    "react-dom": "^18.3.0",                ✅ React 18+
    "@mui/material": "^5.15.0",            ✅ MUI 5+
    "@mui/icons-material": "^5.15.0",      ✅ MUI Icons 5+
    "@emotion/react": "^11.11.0",          ✅ Emotion
    "@emotion/styled": "^11.11.0",         ✅ Emotion
    "react-router-dom": "^6.22.0",         ✅ React Router 6+
    "axios": "^1.6.0",                     ✅ Axios
    "react-hook-form": "^7.50.0",          ✅ React Hook Form
    "yup": "^1.3.0",                       ✅ Yup
    "@hookform/resolvers": "^3.3.0"        ✅ Resolvers
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.0",     ✅ Vite React
    "vite": "^5.1.0"                       ✅ Vite
  }
}
```

**Checklist:**
- [ ] React 18+
- [ ] Material-UI 5+
- [ ] React Router 6+
- [ ] Axios presente
- [ ] React Hook Form presente
- [ ] Yup presente
- [ ] @hookform/resolvers presente
- [ ] Vite presente

---

### Frontend - main.jsx

**Verificar:**

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { ThemeProvider } from '@mui/material/styles';  ✅ OBLIGATORIO
import CssBaseline from '@mui/material/CssBaseline';   ✅ OBLIGATORIO
import theme from './theme';                           ✅ OBLIGATORIO
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>                      ✅ OBLIGATORIO
      <CssBaseline />                                  ✅ OBLIGATORIO
      <App />
    </ThemeProvider>
  </React.StrictMode>
);
```

**Checklist:**
- [ ] Importa `ThemeProvider` de `@mui/material/styles`
- [ ] Importa `CssBaseline` de `@mui/material/CssBaseline`
- [ ] Importa `theme` desde `./theme`
- [ ] `ThemeProvider` envuelve `<App />`
- [ ] `CssBaseline` está incluido dentro de `ThemeProvider`

---

## ✅ PASO 3: Validación por Entidad/Módulo

**Para cada entidad generada, verificar:**

### Backend - Entidad Completa

**Archivos que deben existir:**

```
src/main/java/com/uda/[proyecto]/
├── entity/[Entidad].java                  ✅ OBLIGATORIO
├── dto/[Entidad]DTO.java                  ✅ OBLIGATORIO
├── mapper/[Entidad]Mapper.java            ✅ OBLIGATORIO
├── repository/[Entidad]Repository.java    ✅ OBLIGATORIO
├── service/[Entidad]Service.java          ✅ OBLIGATORIO
├── service/impl/[Entidad]ServiceImpl.java ✅ OBLIGATORIO
└── controller/[Entidad]Controller.java    ✅ OBLIGATORIO
```

**Checklist:**
- [ ] Los 7 archivos existen
- [ ] Nombres siguen la convención PascalCase
- [ ] Ubicación correcta según `ESTRUCTURA_PROYECTO.md`

---

### Backend - Entity

**Verificar:**

```java
import jakarta.persistence.*;              ✅ jakarta (NO javax)
import jakarta.validation.constraints.*;   ✅ jakarta (NO javax)
import lombok.*;

@Entity                                    ✅ OBLIGATORIO
@Table(name = "PRODUCTOS")                 ✅ Nombre en MAYÚSCULAS
@Getter                                    ✅ Lombok
@Setter                                    ✅ Lombok
@NoArgsConstructor                         ✅ Lombok
@AllArgsConstructor                        ✅ Lombok
@Builder                                   ✅ Lombok
public class Producto {
    
    @Id                                    ✅ OBLIGATORIO
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "producto_seq")  ✅
    @SequenceGenerator(name = "producto_seq", sequenceName = "PRODUCTO_SEQ", allocationSize = 1)  ✅
    @Column(name = "ID")                   ✅ Nombre en MAYÚSCULAS
    private Long id;
    
    @NotBlank                              ✅ Validación
    @Column(name = "NOMBRE", nullable = false, length = 100)  ✅ MAYÚSCULAS
    private String nombre;
    
    @Column(name = "CREATED_AT")           ✅ MAYÚSCULAS
    private LocalDateTime createdAt;
    
    @PrePersist                            ✅ Callback
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
}
```

**Checklist:**
- [ ] Usa `jakarta.*` (NO `javax.*`)
- [ ] Tiene `@Entity` y `@Table`
- [ ] Nombre de tabla en MAYÚSCULAS
- [ ] Usa todas las anotaciones Lombok
- [ ] Tiene `@Id` con estrategia SEQUENCE
- [ ] Secuencia con formato `[TABLA]_SEQ`
- [ ] Todos los nombres de columnas en MAYÚSCULAS
- [ ] Tiene campos de auditoría (`createdAt`, `updatedAt`)
- [ ] Tiene campo `active` (Boolean)
- [ ] Tiene callbacks `@PrePersist` y `@PreUpdate`
- [ ] Tiene JavaDoc completo
- [ ] NO usa tipos primitivos (usar `Long`, `Integer`, `Boolean`)

---

### Backend - DTO

**Verificar:**

```java
import jakarta.validation.constraints.*;   ✅ jakarta (NO javax)
import lombok.*;

@Data                                      ✅ OBLIGATORIO
@NoArgsConstructor                         ✅ OBLIGATORIO
@AllArgsConstructor                        ✅ OBLIGATORIO
@Builder                                   ✅ OBLIGATORIO
public class ProductoDTO {
    
    private Long id;
    
    @NotBlank(message = "El nombre es obligatorio")           ✅ Validación + mensaje
    @Size(min = 3, max = 100, message = "Entre 3 y 100 caracteres")  ✅ Límites + mensaje
    private String nombre;
    
    @NotNull(message = "El precio es obligatorio")            ✅ Validación + mensaje
    @DecimalMin(value = "0.01", message = "Debe ser mayor a 0")  ✅ Validación + mensaje
    private BigDecimal precio;
    
    private Boolean active;
    private LocalDateTime createdAt;
}
```

**Checklist:**
- [ ] Usa `jakarta.validation.constraints.*` (NO `javax.*`)
- [ ] Tiene `@Data`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`
- [ ] Todos los campos obligatorios tienen `@NotNull` o `@NotBlank`
- [ ] Strings tienen `@Size` con límites
- [ ] Números tienen `@Min`, `@Max`, `@DecimalMin`, `@DecimalMax`, etc.
- [ ] Emails tienen `@Email`
- [ ] Todos los mensajes de validación son descriptivos en español
- [ ] NO expone campos sensibles (passwords sin encriptar)
- [ ] Usa tipos wrapper (`Long`, `Integer`, `Boolean`)
- [ ] Tiene JavaDoc completo

---

### Frontend - Módulo Completo

**Archivos que deben existir:**

```
src/
├── pages/
│   ├── [Entidad]List.jsx                  ✅ OBLIGATORIO
│   └── [Entidad]Form.jsx                  ✅ OBLIGATORIO
└── services/
    └── [entidad]Service.js                ✅ OBLIGATORIO
```

**Checklist:**
- [ ] Los 3 archivos existen
- [ ] Nombres siguen la convención (PascalCase para componentes, camelCase para servicios)

---

### Frontend - Servicio

**Verificar:**

```javascript
import api from './api';

/**
 * Servicio para gestión de productos
 */
const productoService = {
  
  async getAll() {                         ✅ Método CRUD
    const response = await api.get('/productos');
    return response.data;
  },
  
  async getById(id) {                      ✅ Método CRUD
    const response = await api.get(`/productos/${id}`);
    return response.data;
  },
  
  async create(data) {                     ✅ Método CRUD
    const response = await api.post('/productos', data);
    return response.data;
  },
  
  async update(id, data) {                 ✅ Método CRUD
    const response = await api.put(`/productos/${id}`, data);
    return response.data;
  },
  
  async delete(id) {                       ✅ Método CRUD
    await api.delete(`/productos/${id}`);
  }
};

export default productoService;
```

**Checklist:**
- [ ] Importa `api` desde `./api`
- [ ] Tiene métodos CRUD completos (getAll, getById, create, update, delete)
- [ ] Usa async/await
- [ ] Retorna response.data
- [ ] Tiene JSDoc
- [ ] Maneja errores apropiadamente

---

## ✅ PASO 4: Validación de Base de Datos

### Scripts SQL

**Verificar que existen:**

```
database/
├── schema/
│   ├── 01_create_tables.sql               ✅ OBLIGATORIO
│   ├── 02_create_sequences.sql            ✅ OBLIGATORIO
│   ├── 03_create_indexes.sql              ✅ OBLIGATORIO
│   └── 04_create_constraints.sql          ✅ OBLIGATORIO
└── data/
    └── 01_insert_roles.sql                ⚠️ RECOMENDADO
```

**Verificar contenido de scripts:**

```sql
-- 01_create_tables.sql
CREATE TABLE PRODUCTOS (                   ✅ MAYÚSCULAS
    ID NUMBER(19) NOT NULL,                ✅ Tipo Oracle
    NOMBRE VARCHAR2(100) NOT NULL,         ✅ VARCHAR2
    PRECIO NUMBER(10,2),                   ✅ Decimales
    CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 02_create_sequences.sql
CREATE SEQUENCE PRODUCTOS_SEQ              ✅ Formato [TABLA]_SEQ
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

-- 03_create_indexes.sql
CREATE INDEX IDX_PRODUCTOS_NOMBRE          ✅ Formato IDX_[TABLA]_[CAMPO]
    ON PRODUCTOS(NOMBRE);

-- 04_create_constraints.sql
ALTER TABLE PRODUCTOS
ADD CONSTRAINT PK_PRODUCTOS                ✅ Formato PK_[TABLA]
PRIMARY KEY (ID);
```

**Checklist:**
- [ ] Todos los nombres en MAYÚSCULAS
- [ ] Secuencias con formato `[TABLA]_SEQ`
- [ ] Índices con formato `IDX_[TABLA]_[CAMPO]`
- [ ] Constraints con nombres (PK_, FK_, UK_, CK_)
- [ ] Tipos de datos Oracle (NUMBER, VARCHAR2, TIMESTAMP)
- [ ] Scripts numerados correctamente

---

## ✅ PASO 5: Validación de Documentación

### README.md

**Verificar que contiene:**

```markdown
# Nombre del Proyecto

## Descripción
[Descripción del proyecto]

## Stack Tecnológico
- Backend: Spring Boot 3.5.0 + Java 21
- Frontend: React 18 + Material-UI 5
- Base de Datos: Oracle Database (H2 para desarrollo)

## Requisitos Previos
- Java 21
- Node.js 18+
- Maven 3.8+
- Oracle Database (opcional para desarrollo)

## Instalación y Ejecución

### Backend
```bash
cd backend
mvn spring-boot:run
```

### Frontend
```bash
cd frontend
npm install
npm run dev
```

## Acceso
- Backend: http://localhost:8080/[proyecto]/api/v1
- Frontend: http://localhost:5173
- H2 Console: http://localhost:8080/[proyecto]/h2-console
```

**Checklist:**
- [ ] Título del proyecto
- [ ] Descripción clara
- [ ] Stack tecnológico documentado
- [ ] Requisitos previos listados
- [ ] Instrucciones de instalación
- [ ] Instrucciones de ejecución
- [ ] URLs de acceso
- [ ] Información de H2 Console

---

## ✅ PASO 6: Validación Rápida (5 minutos)

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

---

## 📊 Sistema de Puntuación Completo

| Categoría | Puntos | Descripción |
|-----------|--------|-------------|
| **Elementos Críticos** | 55 | OBLIGATORIOS - PASO 0 |
| **Estructura** | 10 | Carpetas y archivos en ubicaciones correctas |
| **Configuración** | 10 | Versiones y dependencias correctas |
| **Funcionalidad CRUD** | 10 | Backend + Frontend funcionando |
| **Base de Datos** | 5 | Scripts SQL correctos |
| **Documentación** | 5 | README completo |
| **Testing** | 5 | Tests básicos |
| **TOTAL** | 100 | Mínimo requerido: **80/100** |

---

## 🚨 Errores Comunes y Soluciones

### Backend

#### Error 1: No extiende SpringBootServletInitializer
**Síntoma:** WAR no despliega en Tomcat
**Solución:**
```java
@SpringBootApplication
public class Application extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
}
```

#### Error 2: Falta JacksonConfig
**Síntoma:** Fechas como arrays `[2024,1,15]`
**Solución:** Generar JacksonConfig.java completo

#### Error 3: Sin validaciones en DTOs
**Síntoma:** Datos inválidos llegan a BD
**Solución:**
```java
@NotBlank(message = "Campo obligatorio")
@Size(min = 3, max = 100)
private String nombre;
```

### Frontend

#### Error 4: No usa ThemeProvider
**Síntoma:** Sin tema UDA
**Solución:**
```jsx
<ThemeProvider theme={theme}>
  <CssBaseline />
  <App />
</ThemeProvider>
```

#### Error 5: Falta validationSchemas.js
**Síntoma:** Validación solo HTML5
**Solución:** Generar schemas Yup completos

#### Error 6: HTML nativo en lugar de MUI
**Síntoma:** UI inconsistente
**Solución:** Usar solo componentes MUI

### Base de Datos

#### Error 7: Tablas en minúsculas
**Síntoma:** No sigue estándar Oracle
**Solución:**
```sql
CREATE TABLE PRODUCTOS (  -- MAYÚSCULAS
    ID NUMBER(19),
    NOMBRE VARCHAR2(100)
);
```

#### Error 8: Sin secuencias
**Síntoma:** IDs no funcionan
**Solución:**
```sql
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1;
```

---

## 📋 Plantilla de Reporte de Validación

```
## Reporte de Validación UDA

**Proyecto:** [nombre]
**Fecha:** [fecha]
**Validador:** [IA/Desarrollador]

### PASO 0: Elementos Críticos (55 pts)
- [ ] JacksonConfig.java (10 pts)
- [ ] application.yml H2 (10 pts)
- [ ] application-prod.yml Oracle (10 pts)
- [ ] SpringBootServletInitializer (5 pts)
- [ ] packaging WAR (5 pts)
- [ ] GlobalExceptionHandler (5 pts)
- [ ] Validaciones DTOs (5 pts)
- [ ] Validaciones Yup (5 pts)

**Subtotal:** ___/55

### PASO 1: Estructura (10 pts)
- [ ] Backend estructura correcta (5 pts)
- [ ] Frontend estructura correcta (5 pts)

**Subtotal:** ___/10

### PASO 2: Configuración (10 pts)
- [ ] pom.xml correcto (5 pts)
- [ ] package.json correcto (3 pts)
- [ ] main.jsx correcto (2 pts)

**Subtotal:** ___/10

### PASO 3: Funcionalidad (10 pts)
- [ ] CRUD Backend completo (5 pts)
- [ ] CRUD Frontend completo (5 pts)

**Subtotal:** ___/10

### PASO 4: Base de Datos (5 pts)
- [ ] Scripts SQL correctos (5 pts)

**Subtotal:** ___/5

### PASO 5: Documentación (5 pts)
- [ ] README.md completo (5 pts)

**Subtotal:** ___/5

### PASO 6: Validación Rápida (5 pts)
- [ ] Backend compila y ejecuta (3 pts)
- [ ] Frontend compila y ejecuta (2 pts)

**Subtotal:** ___/5

### TOTAL: ___/100

**Estado:** 
- [ ] ✅ APROBADO (≥80 pts)
- [ ] ⚠️ REVISAR (60-79 pts)
- [ ] ❌ RECHAZADO (<60 pts)

### Observaciones:
[Comentarios adicionales]

### Próximos pasos:
[Acciones requeridas si aplica]
```

---

## 🎯 Para IAs: Checklist Final

**Antes de entregar código generado:**

1. ✅ Ejecutar **PASO 0** (Elementos Críticos) - debe obtener **55/55 puntos**
2. ✅ Si PASO 0 < 45 puntos: **DETENER y corregir**
3. ✅ Si PASO 0 ≥ 45 puntos: Continuar con PASO 1-6
4. ✅ Ejecutar validación rápida - todos los comandos deben funcionar
5. ✅ Verificar puntuación total ≥ 80/100
6. ✅ Completar plantilla de reporte

**Si algún punto falla:** Corregir antes de entregar.

**Recuerda:** Es mejor generar menos código pero que funcione correctamente, que mucho código con errores.

---

## 🔧 Comandos Útiles para Validación

### Backend
```bash
# Compilar y generar WAR
mvn clean package

# Ejecutar con H2 (desarrollo)
mvn spring-boot:run

# Ejecutar con Oracle (producción)
mvn spring-boot:run -Dspring-boot.run.profiles=prod

# Ejecutar tests
mvn test

# Verificar dependencias
mvn dependency:tree
```

### Frontend
```bash
# Instalar dependencias
npm install

# Compilar para producción
npm run build

# Ejecutar en desarrollo
npm run dev

# Ejecutar tests
npm test

# Verificar dependencias
npm list
```

### Base de Datos
```bash
# Conectar a H2 Console
# URL: http://localhost:8080/[proyecto]/h2-console
# JDBC URL: jdbc:h2:mem:testdb
# Username: sa
# Password: (vacío)

# Conectar a Oracle (si está disponible)
sqlplus usuario/password@localhost:1521/XE
```

---

## 🎓 Herramientas de Validación Automática

### Verificación de Estructura
```bash
# Verificar que existen archivos críticos
ls backend/src/main/java/com/uda/*/config/JacksonConfig.java
ls backend/src/main/resources/application.yml
ls backend/src/main/resources/application-prod.yml
ls frontend/src/theme/index.js
ls frontend/src/utils/validationSchemas.js
```

### Verificación de Compilación
```bash
# Backend debe compilar sin errores
cd backend && mvn clean compile

# Frontend debe compilar sin errores  
cd frontend && npm run build
```

### Verificación de Ejecución
```bash
# Backend debe iniciar sin errores
cd backend && timeout 30s mvn spring-boot:run

# Frontend debe iniciar sin errores
cd frontend && timeout 10s npm run dev
```

---

## 📚 Referencias Adicionales

- **ESPECIFICACION_COMPLETA.md**: Detalles técnicos completos
- **REGLAS_GENERACION.md**: Reglas estrictas para IAs
- **CHECKLIST_CALIDAD.md**: Lista de verificación exhaustiva
- **ESTRUCTURA_PROYECTO.md**: Estructura exacta de archivos
- **UI_STANDARDS.md**: Estándares de Material-UI
- **MIGRACION_H2_A_ORACLE.md**: Guía de migración de BD

---

**Nota Final:** Esta guía debe usarse como herramienta de auto-validación. El objetivo es garantizar que el código generado cumple con todos los estándares UDA y es funcional desde el primer momento.