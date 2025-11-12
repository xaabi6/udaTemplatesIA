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

### 1. JacksonConfig.java

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/config/JacksonConfig.java`

**Verificar que existe y contiene:**

```java
@Configuration
public class JacksonConfig {
    
    @Bean
    @Primary
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());                    ✅ OBLIGATORIO
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS); ✅ OBLIGATORIO
        return mapper;
    }
}
```

**Checklist:**
- [ ] Archivo existe en `config/JacksonConfig.java`
- [ ] Tiene `@Configuration`
- [ ] Tiene método `objectMapper()` con `@Bean` y `@Primary`
- [ ] Registra `JavaTimeModule`
- [ ] Desactiva `WRITE_DATES_AS_TIMESTAMPS`
- [ ] Tiene JavaDoc explicativo

**⚠️ SI FALTA: DETENER - Puntuación máxima: 45/100**

---

### 2. application.yml con H2

**Ubicación:** `backend/src/main/resources/application.yml`

**Verificar que existe y contiene:**

```yaml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}     ✅ Perfil dev por defecto
  
  datasource:
    url: jdbc:h2:mem:testdb                   ✅ H2 en memoria
    driver-class-name: org.h2.Driver          ✅ Driver H2
    username: sa                              ✅ Username
    password:                                 ✅ Password vacío
  
  h2:
    console:
      enabled: true                           ✅ Consola habilitada
      path: /h2-console                       ✅ Path configurado
  
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect  ✅ Dialect H2
    hibernate:
      ddl-auto: create-drop                   ✅ create-drop para dev
    show-sql: true                            ✅ Mostrar SQL en dev
```

**Checklist:**
- [ ] Archivo existe en `resources/application.yml`
- [ ] Perfil activo por defecto es `dev`
- [ ] Datasource configurado para H2 en memoria
- [ ] H2 Console habilitado
- [ ] JPA con dialect H2
- [ ] `ddl-auto: create-drop`
- [ ] `show-sql: true`

**Dependencia en pom.xml:**
- [ ] Existe `<dependency>` con `<artifactId>h2</artifactId>`
- [ ] Tiene `<scope>runtime</scope>`

**⚠️ SI FALTA: DETENER - Puntuación máxima: 45/100**

---

### 3. application-prod.yml con Oracle

**Ubicación:** `backend/src/main/resources/application-prod.yml`

**Verificar que existe y contiene:**

```yaml
spring:
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:ORCL}  ✅
    username: ${DB_USERNAME:uda_user}         ✅ Variable de entorno
    password: ${DB_PASSWORD:uda_password}     ✅ Variable de entorno
    driver-class-name: oracle.jdbc.OracleDriver  ✅
    hikari:
      maximum-pool-size: ${DB_POOL_SIZE:20}   ✅ Pool configurado
      minimum-idle: ${DB_POOL_MIN_IDLE:5}     ✅
  
  jpa:
    database-platform: org.hibernate.dialect.Oracle12cDialect  ✅ Dialect Oracle
    hibernate:
      ddl-auto: validate                      ✅ validate en producción
    show-sql: false                           ✅ No mostrar SQL
```

**Checklist:**
- [ ] Archivo existe en `resources/application-prod.yml`
- [ ] Datasource configurado para Oracle
- [ ] Usa variables de entorno con valores por defecto
- [ ] HikariCP configurado
- [ ] JPA con dialect Oracle
- [ ] `ddl-auto: validate` (NO create, NO update)
- [ ] `show-sql: false`

**⚠️ SI FALTA: DETENER - Puntuación máxima: 45/100**

---

### 4. Application.java extiende SpringBootServletInitializer

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/Application.java`

**Verificar:**

```java
@SpringBootApplication
public class Application extends SpringBootServletInitializer {  ✅ OBLIGATORIO
    
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);  ✅ OBLIGATORIO
    }
    
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);  ✅ OBLIGATORIO
    }
}
```

**Checklist:**
- [ ] Clase extiende `SpringBootServletInitializer`
- [ ] Método `configure()` sobrescrito
- [ ] Método `main()` presente
- [ ] Tiene `@SpringBootApplication`
- [ ] Tiene JavaDoc

**⚠️ SI NO EXTIENDE: DETENER - Puntuación máxima: 45/100**

---

### 5. pom.xml con packaging WAR

**Ubicación:** `backend/pom.xml`

**Verificar:**

```xml
<packaging>war</packaging>                    ✅ OBLIGATORIO

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>                   ✅ OBLIGATORIO
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>                    ✅ OBLIGATORIO
</dependency>

<build>
    <finalName>${project.artifactId}</finalName>  ✅ RECOMENDADO
</build>
```

**Checklist:**
- [ ] `<packaging>war</packaging>` presente
- [ ] Tomcat con `scope=provided`
- [ ] H2 con `scope=runtime`
- [ ] `<finalName>` sin versión

**⚠️ SI NO ES WAR: DETENER - Puntuación máxima: 45/100**

---

### 6. GlobalExceptionHandler

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/exception/GlobalExceptionHandler.java`

**Verificar que existe y contiene:**

```java
@RestControllerAdvice                         ✅ OBLIGATORIO
@Slf4j                                        ✅ OBLIGATORIO
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)  ✅ Maneja 404
    public ResponseEntity<ErrorResponse> handleResourceNotFound(...) {
        log.error("Recurso no encontrado: {}", ex.getMessage());
        // Retorna ErrorResponse con status 404
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)  ✅ Maneja validación
    public ResponseEntity<ErrorResponse> handleValidationErrors(...) {
        // Extrae errores de validación
        // Retorna ErrorResponse con status 400
    }
    
    @ExceptionHandler(Exception.class)        ✅ Maneja genéricos
    public ResponseEntity<ErrorResponse> handleGenericException(...) {
        log.error("Error interno del servidor", ex);
        // NO expone stack trace
        // Retorna ErrorResponse con status 500
    }
}
```

**Archivos relacionados:**
- [ ] `ResourceNotFoundException.java` existe
- [ ] `ErrorResponse.java` existe con campos: `timestamp`, `status`, `error`, `message`, `validationErrors`

**Checklist:**
- [ ] `GlobalExceptionHandler` existe
- [ ] Tiene `@RestControllerAdvice` y `@Slf4j`
- [ ] Maneja `ResourceNotFoundException` → 404
- [ ] Maneja `MethodArgumentNotValidException` → 400
- [ ] Maneja `Exception` genérica → 500
- [ ] NO expone stack traces
- [ ] Loguea errores apropiadamente

**⚠️ SI FALTA: Advertencia - Manejo de errores deficiente**

---

### 7. Validaciones en DTOs

**Verificar en TODOS los DTOs:**

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ProductoDTO {
    
    @NotBlank(message = "El nombre es obligatorio")           ✅ Validación presente
    @Size(min = 3, max = 100, message = "Entre 3 y 100 caracteres")  ✅ Con mensaje
    private String nombre;
    
    @NotNull(message = "El precio es obligatorio")            ✅ Validación presente
    @DecimalMin(value = "0.01", message = "Mayor a 0")        ✅ Con mensaje
    private BigDecimal precio;
}
```

**En Controllers:**

```java
@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {  ✅ @Valid
    // ...
}
```

**Checklist:**
- [ ] Todos los DTOs tienen validaciones Bean Validation
- [ ] Campos obligatorios tienen `@NotNull` o `@NotBlank`
- [ ] Strings tienen `@Size` con límites
- [ ] Números tienen `@Min`, `@Max`, `@DecimalMin`, etc.
- [ ] Todos los mensajes son descriptivos en español
- [ ] Todos los `@PostMapping` y `@PutMapping` usan `@Valid`

**⚠️ SI FALTAN: Advertencia - Datos inválidos pueden llegar a BD**

---

### 8. Validaciones Yup en Frontend

**Ubicación:** `frontend/src/utils/validationSchemas.js`

**Verificar que existe y contiene:**

```javascript
import * as yup from 'yup';                   ✅ Importa yup

export const productoSchema = yup.object({
  nombre: yup
    .string()
    .required('El nombre es obligatorio')     ✅ Mensaje en español
    .min(3, 'Mínimo 3 caracteres')            ✅ Validación con mensaje
    .max(100, 'Máximo 100 caracteres'),       ✅ Validación con mensaje
  
  precio: yup
    .number()
    .required('El precio es obligatorio')     ✅ Mensaje en español
    .positive('Debe ser mayor a 0')           ✅ Validación con mensaje
    .typeError('Ingrese un número válido'),   ✅ Mensaje de tipo
}).required();

export default {
  productoSchema,
};
```

**En formularios:**

```javascript
import { yupResolver } from '@hookform/resolvers/yup';
import { productoSchema } from '../utils/validationSchemas';

const { control, handleSubmit } = useForm({
  resolver: yupResolver(productoSchema),      ✅ OBLIGATORIO
});
```

**Checklist:**
- [ ] `validationSchemas.js` existe
- [ ] Importa `yup`
- [ ] Al menos un schema por formulario
- [ ] Validaciones completas (required, min, max, etc.)
- [ ] Mensajes en español
- [ ] Formularios usan `yupResolver`

**Dependencias en package.json:**
- [ ] `yup` instalado
- [ ] `@hookform/resolvers` instalado

**⚠️ SI FALTA: Advertencia - Validación solo HTML5 (insuficiente)**

---

## 📊 Puntuación de Elementos Críticos

| # | Elemento | Puntos | Estado |
|---|----------|--------|--------|
| 1 | JacksonConfig.java | 10 | [ ] |
| 2 | application.yml (H2) | 10 | [ ] |
| 3 | application-prod.yml (Oracle) | 10 | [ ] |
| 4 | SpringBootServletInitializer | 5 | [ ] |
| 5 | packaging WAR | 5 | [ ] |
| 6 | GlobalExceptionHandler | 5 | [ ] |
| 7 | Validaciones DTOs | 5 | [ ] |
| 8 | Validaciones Yup | 5 | [ ] |
| **TOTAL** | | **55** | **___/55** |

**Criterio de aceptación:**

- ✅ **55/55 puntos (8/8 elementos):** EXCELENTE - Continuar con validación completa
- ⚠️ **45-54 puntos (6-7/8 elementos):** ACEPTABLE - Corregir faltantes antes de continuar
- ❌ **< 45 puntos (< 6/8 elementos):** INSUFICIENTE - DETENER y corregir inmediatamente

**SI LA PUNTUACIÓN ES < 45/55: NO CONTINUAR**

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

### Base de Datos - Estructura

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

**Checklist:**
- [ ] Carpetas `schema/` y `data/` existen
- [ ] Scripts numerados correctamente (01, 02, 03, 04)
- [ ] Al menos un script de datos iniciales

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

**Dependencias obligatorias:**

```xml
<!-- Spring Boot -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>        ✅
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>   ✅
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>   ✅
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId> ✅
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>                                 ✅
</dependency>

<!-- Bases de datos -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc11</artifactId>                        ✅
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>                                  ✅
</dependency>

<!-- Utilidades -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>                         ✅
</dependency>
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>                      ✅
</dependency>

<!-- JWT -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>                               ✅
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>                                  ✅
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>                                  ✅
</dependency>

<!-- Testing -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>                                     ✅
</dependency>
```

**Annotation Processors:**

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>21</source>
        <target>21</target>
        <annotationProcessorPaths>
            <path>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>        ✅
            </path>
            <path>
                <groupId>org.mapstruct</groupId>
                <artifactId>mapstruct-processor</artifactId>
                <version>${mapstruct.version}</version>     ✅
            </path>
        </annotationProcessorPaths>
    </configuration>
</plugin>
```

**Checklist:**
- [ ] Spring Boot 3.5.0
- [ ] Java 21
- [ ] Packaging `war`
- [ ] Todas las dependencias obligatorias presentes
- [ ] Tomcat con `scope=provided`
- [ ] H2 con `scope=runtime`
- [ ] Annotation processors configurados (Lombok + MapStruct)
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

### Frontend - Tema UDA

**Verificar estructura completa:**

```
src/theme/
├── index.js                               ✅ Tema principal
├── palette.js                             ✅ Colores
├── typography.js                          ✅ Tipografía
├── components.js                          ✅ Overrides
└── shadows.js                             ✅ Sombras
```

**theme/index.js:**

```javascript
import { createTheme } from '@mui/material/styles';
import palette from './palette';           ✅ Importa palette
import typography from './typography';     ✅ Importa typography
import components from './components';     ✅ Importa components
import shadows from './shadows';           ✅ Importa shadows

const theme = createTheme({
  palette,                                 ✅
  typography,                              ✅
  components,                              ✅
  shadows,                                 ✅
  shape: {
    borderRadius: 8,                       ✅
  },
  spacing: 8,                              ✅
});

export default theme;
```

**theme/palette.js:**

```javascript
const palette = {
  mode: 'light',
  primary: {
    main: '#1976d2',                       ✅ Color primario UDA
    light: '#42a5f5',
    dark: '#1565c0',
    contrastText: '#ffffff',
  },
  secondary: {
    main: '#dc004e',                       ✅ Color secundario
    // ...
  },
  // ... resto de colores
};

export default palette;
```

**Checklist:**
- [ ] Los 5 archivos del tema existen
- [ ] `index.js` importa todos los módulos
- [ ] `palette.js` define colores UDA
- [ ] `typography.js` define tipografía
- [ ] `components.js` define overrides de MUI
- [ ] `shadows.js` define sombras
- [ ] Tema exportado correctamente

**📖 Referencia completa:** `UI_STANDARDS.md`

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
- [ ] Usa todas las anotaciones Lombok (`@Getter`, `@Setter`, `@NoArgsConstructor`, `@AllArgsConstructor`, `@Builder`)
- [ ] Tiene `@Id` con estrategia SEQUENCE
- [ ] Secuencia con formato `[TABLA]_SEQ`
- [ ] Todos los nombres de columnas en MAYÚSCULAS
- [ ] Tiene campos de auditoría (`createdAt`, `updatedAt`)
- [ ] Tiene campo `active` (Boolean)
- [ ] Tiene callbacks `@PrePersist` y `@PreUpdate`
- [ ] Tiene JavaDoc completo
- [ ] NO usa tipos primitivos (usar `Long`, `Integer`, `Boolean` en lugar de `long`, `int`, `boolean`)

---

### Backend - DTO

**Verificar:**

```java
import jakarta.validation.constraints.*;   ✅ jakarta (NO javax)
import lombok.*;
import java.time.LocalDateTime;

/**
 * DTO para Producto.
 * Objeto de transferencia de datos para la API REST.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Data                                      ✅ OBLIGATORIO
@NoArgsConstructor                         ✅ OBLIGATORIO
@AllArgsConstructor                        ✅ OBLIGATORIO
@Builder                                   ✅ OBLIGATORIO
public class ProductoDTO {
    
    private Long id;
    
    @NotBlank(message = "El nombre es obligatorio")           ✅ Validación + mensaje
    @Size(min = 3, max = 100, message = "Entre 3 y 100 caracteres")  ✅ Límites + mensaje
    private String nombre;
    
    @Size(max = 500, message = "Máximo 500 caracteres")       ✅ Límite + mensaje
    private String descripcion;
    
    @NotNull(message = "El precio es obligatorio")            ✅ Validación + mensaje
    @DecimalMin(value = "0.01", message = "Debe ser mayor a 0")  ✅ Validación + mensaje
    @Digits(integer = 10, fraction = 2, message = "Formato inválido")  ✅ Formato + mensaje
    private BigDecimal precio;
    
    @Min(value = 0, message = "No puede ser negativo")        ✅ Validación + mensaje
    private Integer stock;
    
    private Boolean active;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
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

### Backend - Mapper

**Verificar:**

```java
import org.mapstruct.*;
import java.util.List;

/**
 * Mapper para conversión entre Producto y ProductoDTO.
 * Utiliza MapStruct para generación automática de código.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Mapper(                                   ✅ OBLIGATORIO
    componentModel = "spring",             ✅ OBLIGATORIO
    nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE  ✅ OBLIGATORIO
)
public interface ProductoMapper {          ✅ Es interfaz (NO clase)
    
    /**
     * Convierte una entidad Producto a ProductoDTO.
     */
    ProductoDTO toDTO(Producto entity);                              ✅ OBLIGATORIO
    
    /**
     * Convierte un ProductoDTO a entidad Producto.
     */
    Producto toEntity(ProductoDTO dto);                              ✅ OBLIGATORIO
    
    /**
     * Convierte una lista de entidades a lista de DTOs.
     */
    List<ProductoDTO> toDTOList(List<Producto> entities);           ✅ OBLIGATORIO
    
    /**
     * Actualiza una entidad existente con datos del DTO.
     * Ignora valores null del DTO.
     */
    @BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
    void updateEntityFromDTO(ProductoDTO dto, @MappingTarget Producto entity);  ✅ OBLIGATORIO
}
```

**Checklist:**
- [ ] Es una **interfaz** (NO clase)
- [ ] Tiene `@Mapper(componentModel = "spring")`
- [ ] Tiene `nullValuePropertyMappingStrategy = IGNORE`
- [ ] Tiene método `toDTO(Entity)` → `DTO`
- [ ] Tiene método `toEntity(DTO)` → `Entity`
- [ ] Tiene método `toDTOList(List<Entity>)` → `List<DTO>`
- [ ] Tiene método `updateEntityFromDTO(DTO, @MappingTarget Entity)`
- [ ] Todos los métodos tienen JavaDoc
- [ ] Archivo tiene JavaDoc de clase

---

### Backend - Repository

**Verificar:**

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import java.util.List;
import java.util.Optional;

/**
 * Repositorio para la entidad Producto.
 * Proporciona acceso a datos mediante Spring Data JPA.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Repository                                ✅ OBLIGATORIO
public interface ProductoRepository extends JpaRepository<Producto, Long> {  ✅ OBLIGATORIO
    
    /**
     * Busca todos los productos activos.
     */
    List<Producto> findByActiveTrue();                              ✅ RECOMENDADO
    
    /**
     * Busca un producto por ID solo si está activo.
     */
    Optional<Producto> findByIdAndActiveTrue(Long id);              ✅ RECOMENDADO
    
    /**
     * Busca productos por nombre (case insensitive).
     */
    @Query("SELECT p FROM Producto p WHERE LOWER(p.nombre) LIKE LOWER(CONCAT('%', :nombre, '%')) AND p.active = true")
    List<Producto> findByNombreContainingIgnoreCase(@Param("nombre") String nombre);  ✅ RECOMENDADO
}
```

**Checklist:**
- [ ] Es una **interfaz** (NO clase)
- [ ] Tiene `@Repository`
- [ ] Extiende `JpaRepository<Entidad, Long>`
- [ ] Métodos personalizados siguen nomenclatura Spring Data
- [ ] Queries con `@Query` usan JPQL (NO SQL nativo)
- [ ] Parámetros con `@Param`
- [ ] Métodos retornan `Optional` para búsquedas por ID
- [ ] Todos los métodos tienen JavaDoc
- [ ] Archivo tiene JavaDoc de clase

---

### Backend - Service Interface

**Verificar:**

```java
import java.util.List;

/**
 * Interfaz de servicio para Producto.
 * Define las operaciones de negocio disponibles.
 * 
 * @author UDA
 * @version 1.0.0
 */
public interface ProductoService {        ✅ Es interfaz (NO clase)
    
    /**
     * Obtiene todos los productos activos.
     * 
     * @return Lista de DTOs
     */
    List<ProductoDTO> findAll();           ✅ Retorna DTO
    
    /**
     * Obtiene un producto por su ID.
     * 
     * @param id ID del producto
     * @return DTO del producto
     * @throws ResourceNotFoundException si no se encuentra
     */
    ProductoDTO findById(Long id);         ✅ Retorna DTO
    
    /**
     * Crea un nuevo producto.
     * 
     * @param dto DTO con los datos
     * @return DTO del producto creado
     */
    ProductoDTO create(ProductoDTO dto);   ✅ Recibe y retorna DTO
    
    /**
     * Actualiza un producto existente.
     * 
     * @param id ID del producto
     * @param dto DTO con los nuevos datos
     * @return DTO del producto actualizado
     * @throws ResourceNotFoundException si no se encuentra
     */
    ProductoDTO update(Long id, ProductoDTO dto);  ✅ Recibe y retorna DTO
    
    /**
     * Elimina lógicamente un producto.
     * 
     * @param id ID del producto
     * @throws ResourceNotFoundException si no se encuentra
     */
    void delete(Long id);                  ✅ Void
}
```

**Checklist:**
- [ ] Es una **interfaz** (NO clase)
- [ ] Métodos retornan DTOs (NO Entities)
- [ ] Métodos reciben DTOs (NO Entities)
- [ ] Tiene métodos CRUD básicos (findAll, findById, create, update, delete)
- [ ] Todos los métodos tienen JavaDoc completo con `@param`, `@return`, `@throws`
- [ ] Archivo tiene JavaDoc de clase

---

### Backend - Service Implementation

**Verificar:**

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.stream.Collectors;

/**
 * Implementación del servicio para Producto.
 * Contiene la lógica de negocio.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Service                                   ✅ OBLIGATORIO
@RequiredArgsConstructor                   ✅ OBLIGATORIO
@Slf4j                                     ✅ OBLIGATORIO
@Transactional(readOnly = true)            ✅ OBLIGATORIO (nivel clase)
public class ProductoServiceImpl implements ProductoService {
    
    private final ProductoRepository repository;  ✅ Inyección por constructor
    private final ProductoMapper mapper;          ✅ Inyección por constructor
    
    @Override
    public List<ProductoDTO> findAll() {
        log.debug("Obteniendo todos los productos activos");  ✅ Usa logger
        return repository.findByActiveTrue().stream()
            .map(mapper::toDTO)                    ✅ Convierte a DTO
            .collect(Collectors.toList());
    }
    
    @Override
    public ProductoDTO findById(Long id) {
        log.debug("Buscando producto con ID: {}", id);  ✅ Usa logger
        return repository.findByIdAndActiveTrue(id)
            .map(mapper::toDTO)                    ✅ Convierte a DTO
            .orElseThrow(() -> new ResourceNotFoundException(
                "Producto no encontrado con ID: " + id));  ✅ Lanza excepción
    }
    
    @Override
    @Transactional                         ✅ Método de escritura
    public ProductoDTO create(ProductoDTO dto) {
        log.info("Creando nuevo producto: {}", dto.getNombre());  ✅ Usa logger
        Producto entity = mapper.toEntity(dto);    ✅ Convierte de DTO
        entity.setActive(true);
        Producto saved = repository.save(entity);
        log.info("Producto creado con ID: {}", saved.getId());
        return mapper.toDTO(saved);                ✅ Convierte a DTO
    }
    
    @Override
    @Transactional                         ✅ Método de escritura
    public ProductoDTO update(Long id, ProductoDTO dto) {
        log.info("Actualizando producto con ID: {}", id);
        Producto entity = repository.findByIdAndActiveTrue(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "Producto no encontrado con ID: " + id));
        
        mapper.updateEntityFromDTO(dto, entity);   ✅ Usa mapper para actualizar
        Producto updated = repository.save(entity);
        log.info("Producto actualizado con ID: {}", id);
        return mapper.toDTO(updated);              ✅ Convierte a DTO
    }
    
    @Override
    @Transactional                         ✅ Método de escritura
    public void delete(Long id) {
        log.info("Eliminando producto con ID: {}", id);
        Producto entity = repository.findByIdAndActiveTrue(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "Producto no encontrado con ID: " + id));
        
        entity.setActive(false);               ✅ Soft delete
        repository.save(entity);
        log.info("Producto eliminado (soft delete) con ID: {}", id);
    }
}
```

**Checklist:**
- [ ] Tiene `@Service`, `@RequiredArgsConstructor`, `@Slf4j`
- [ ] Tiene `@Transactional(readOnly = true)` a nivel de clase
- [ ] Inyección por constructor (NO `@Autowired` en campos)
- [ ] Métodos de escritura tienen `@Transactional` (sin readOnly)
- [ ] Usa logger (`log.debug`, `log.info`, `log.error`)
- [ ] Convierte Entity ↔ DTO con mapper (NO manualmente)
- [ ] Lanza `ResourceNotFoundException` cuando no encuentra
- [ ] Implementa soft delete (setActive(false))
- [ ] NO retorna Entities (solo DTOs)
- [ ] Todos los métodos tienen JavaDoc
- [ ] Archivo tiene JavaDoc de clase

---

### Backend - Controller

**Verificar:**

```java
import jakarta.validation.Valid;           ✅ jakarta (NO javax)
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

/**
 * Controlador REST para Producto.
 * Expone los endpoints de la API.
 * 
 * @author UDA
 * @version 1.0.0
 */
@RestController                            ✅ OBLIGATORIO
@RequestMapping("/api/v1/productos")       ✅ OBLIGATORIO
@RequiredArgsConstructor                   ✅ OBLIGATORIO
@Slf4j                                     ✅ OBLIGATORIO
@CrossOrigin(origins = "*")                ✅ OBLIGATORIO
public class ProductoController {
    
    private final ProductoService service;  ✅ Inyección por constructor
    
    /**
     * Obtiene todos los productos.
     */
    @GetMapping                            ✅ Endpoint GET /
    public ResponseEntity<List<ProductoDTO>> findAll() {
        log.info("GET /api/v1/productos - Obteniendo todos los productos");  ✅ Loguea
        return ResponseEntity.ok(service.findAll());  ✅ ResponseEntity
    }
    
    /**
     * Obtiene un producto por ID.
     */
    @GetMapping("/{id}")                   ✅ Endpoint GET /{id}
    public ResponseEntity<ProductoDTO> findById(@PathVariable Long id) {
        log.info("GET /api/v1/productos/{}", id);
        return ResponseEntity.ok(service.findById(id));
    }
    
    /**
     * Crea un nuevo producto.
     */
    @PostMapping                           ✅ Endpoint POST /
    public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {  ✅ @Valid
        log.info("POST /api/v1/productos - Creando producto: {}", dto.getNombre());
        ProductoDTO created = service.create(dto);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);  ✅ 201 CREATED
    }
    
    /**
     * Actualiza un producto existente.
     */
    @PutMapping("/{id}")                   ✅ Endpoint PUT /{id}
    public ResponseEntity<ProductoDTO> update(
            @PathVariable Long id,
            @Valid @RequestBody ProductoDTO dto) {  ✅ @Valid
        log.info("PUT /api/v1/productos/{}", id);
        return ResponseEntity.ok(service.update(id, dto));  ✅ 200 OK
    }
    
    /**
     * Elimina un producto.
     */
    @DeleteMapping("/{id}")                ✅ Endpoint DELETE /{id}
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        log.info("DELETE /api/v1/productos/{}", id);
        service.delete(id);
        return ResponseEntity.noContent().build();  ✅ 204 NO_CONTENT
    }
    
    /**
     * Busca productos por nombre.
     */
    @GetMapping("/search")                 ✅ Endpoint GET /search
    public ResponseEntity<List<ProductoDTO>> search(@RequestParam String nombre) {
        log.info("GET /api/v1/productos/search?nombre={}", nombre);
        return ResponseEntity.ok(service.searchByNombre(nombre));
    }
}
```

**Checklist:**
- [ ] Usa `jakarta.validation.Valid` (NO `javax.*`)
- [ ] Tiene todas las anotaciones obligatorias (`@RestController`, `@RequestMapping`, `@RequiredArgsConstructor`, `@Slf4j`, `@CrossOrigin`)
- [ ] Inyección por constructor (NO `@Autowired` en campos)
- [ ] Endpoints CRUD completos:
  - [ ] `GET /` → findAll()
  - [ ] `GET /{id}` → findById()
  - [ ] `POST /` → create()
  - [ ] `PUT /{id}` → update()
  - [ ] `DELETE /{id}` → delete()
- [ ] Usa `@Valid` para validación en POST y PUT
- [ ] Retorna `ResponseEntity` con códigos HTTP correctos:
  - [ ] 200 OK para GET, PUT
  - [ ] 201 CREATED para POST
  - [ ] 204 NO_CONTENT para DELETE
- [ ] Loguea requests importantes
- [ ] Todos los métodos tienen JavaDoc
- [ ] Archivo tiene JavaDoc de clase

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
import api from './api';                   ✅ Importa api

/**
 * Servicio para operaciones CRUD de Producto.
 * 
 * @author UDA
 * @version 1.0.0
 */
const productoService = {
  /**
   * Obtiene todos los productos.
   * @returns {Promise} Lista de productos
   */
  getAll: async () => {                    ✅ Método obligatorio
    const response = await api.get('/productos');
    return response.data;
  },

  /**
   * Obtiene un producto por ID.
   * @param {number} id - ID del producto
   * @returns {Promise} Producto encontrado
   */
  getById: async (id) => {                 ✅ Método obligatorio
    const response = await api.get(`/productos/${id}`);
    return response.data;
  },

  /**
   * Crea un nuevo producto.
   * @param {Object} data - Datos del producto
   * @returns {Promise} Producto creado
   */
  create: async (data) => {                ✅ Método obligatorio
    const response = await api.post('/productos', data);
    return response.data;
  },

  /**
   * Actualiza un producto existente.
   * @param {number} id - ID del producto
   * @param {Object} data - Nuevos datos
   * @returns {Promise} Producto actualizado
   */
  update: async (id, data) => {            ✅ Método obligatorio
    const response = await api.put(`/productos/${id}`, data);
    return response.data;
  },

  /**
   * Elimina un producto.
   * @param {number} id - ID del producto
   * @returns {Promise}
   */
  delete: async (id) => {                  ✅ Método obligatorio
    await api.delete(`/productos/${id}`);
  },

  /**
   * Busca productos por nombre.
   * @param {string} nombre - Nombre a buscar
   * @returns {Promise} Lista de productos encontrados
   */
  search: async (nombre) => {              ⚠️ Recomendado
    const response = await api.get('/productos/search', {
      params: { nombre }
    });
    return response.data;
  },
};

export default productoService;            ✅ Export default
```

**Checklist:**
- [ ] Importa `api` desde `./api`
- [ ] Tiene los 5 métodos CRUD obligatorios (getAll, getById, create, update, delete)
- [ ] Usa `async/await`
- [ ] Retorna `response.data`
- [ ] Export default
- [ ] Todos los métodos tienen JSDoc con `@param` y `@returns`
- [ ] Archivo tiene JSDoc de módulo

---

### Frontend - Página de Listado

**Verificar:**

```jsx
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import {
  Box,
  Button,
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  Paper,
  IconButton,
  CircularProgress,
  Container,
  Typography,
  TextField,
} from '@mui/material';                    ✅ Solo componentes de MUI
import {
  Add as AddIcon,
  Edit as EditIcon,
  Delete as DeleteIcon,
  Search as SearchIcon,
} from '@mui/icons-material';              ✅ Solo iconos de MUI
import productoService from '../services/productoService';

/**
 * Página de listado de productos.
 * Muestra tabla con opciones de búsqueda, crear, editar y eliminar.
 * 
 * @author UDA
 * @version 1.0.0
 */
function ProductoList() {
  const [productos, setProductos] = useState([]);  ✅ Estado de datos
  const [loading, setLoading] = useState(true);    ✅ Estado de loading
  const [searchTerm, setSearchTerm] = useState('');
  const navigate = useNavigate();

  useEffect(() => {
    loadProductos();
  }, []);

  const loadProductos = async () => {
    try {
      setLoading(true);
      const data = await productoService.getAll();
      setProductos(data);
    } catch (error) {
      console.error('Error al cargar productos:', error);
      alert('Error al cargar los productos');  ⚠️ Mejor usar Snackbar
    } finally {
      setLoading(false);
    }
  };

  const handleDelete = async (id) => {
    if (window.confirm('¿Está seguro de eliminar este producto?')) {
      try {
        await productoService.delete(id);
        alert('Producto eliminado correctamente');
        loadProductos();
      } catch (error) {
        console.error('Error al eliminar:', error);
        alert('Error al eliminar el producto');
      }
    }
  };

  if (loading) {
    return (
      <Box display="flex" justifyContent="center" alignItems="center" minHeight="400px">
        <CircularProgress />               ✅ Loading state
      </Box>
    );
  }

  return (
    <Container maxWidth="lg" sx={{ mt: 4 }}>  ✅ Usa sx prop
      <Box display="flex" justifyContent="space-between" alignItems="center" mb={3}>
        <Typography variant="h4" component="h1">  ✅ Typography (NO <h1>)
          Productos
        </Typography>
        <Button
          variant="contained"
          startIcon={<AddIcon />}
          onClick={() => navigate('/productos/new')}
        >
          Nuevo Producto
        </Button>
      </Box>

      <Box display="flex" gap={2} mb={3}>
        <TextField
          fullWidth
          label="Buscar por nombre"
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && handleSearch()}
        />
        <Button
          variant="outlined"
          startIcon={<SearchIcon />}
          onClick={handleSearch}
        >
          Buscar
        </Button>
      </Box>

      <TableContainer component={Paper}>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>ID</TableCell>
              <TableCell>Nombre</TableCell>
              <TableCell>Descripción</TableCell>
              <TableCell>Precio</TableCell>
              <TableCell align="right">Acciones</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {productos.length === 0 ? (
              <TableRow>
                <TableCell colSpan={5} align="center">
                  <Typography variant="body2" color="text.secondary">
                    No hay productos disponibles  ✅ Estado vacío
                  </Typography>
                </TableCell>
              </TableRow>
            ) : (
              productos.map((producto) => (
                <TableRow key={producto.id} hover>
                  <TableCell>{producto.id}</TableCell>
                  <TableCell>{producto.nombre}</TableCell>
                  <TableCell>{producto.descripcion}</TableCell>
                  <TableCell>${producto.precio}</TableCell>
                  <TableCell align="right">
                    <IconButton
                      color="primary"
                      onClick={() => navigate(`/productos/${producto.id}/edit`)}
                      size="small"
                    >
                      <EditIcon />
                    </IconButton>
                    <IconButton
                      color="error"
                      onClick={() => handleDelete(producto.id)}
                      size="small"
                    >
                      <DeleteIcon />
                    </IconButton>
                  </TableCell>
                </TableRow>
              ))
            )}
          </TableBody>
        </Table>
      </TableContainer>
    </Container>
  );
}

export default ProductoList;
```

**Checklist:**
- [ ] Importa **solo** componentes de MUI (NO HTML nativo como `<div>`, `<h1>`, `<button>`)
- [ ] Usa `sx` prop para estilos (NO `style` inline)
- [ ] NO hay colores hardcodeados (ej: `#1976d2`)
- [ ] NO hay espaciado hardcodeado (ej: `padding: '16px'`)
- [ ] Usa `Typography` para texto (NO `<h1>`, `<p>`, `<span>`)
- [ ] Usa `Button` de MUI (NO `<button>`)
- [ ] Usa `Box` para layout (NO `<div>`)
- [ ] Tiene estado de loading
- [ ] Tiene manejo de errores
- [ ] Tiene estado vacío (cuando no hay datos)
- [ ] Usa `useNavigate` para navegación
- [ ] Responsive design (usa `Container`, `Grid`, etc.)
- [ ] Tiene JSDoc

**📖 Referencia completa:** `UI_STANDARDS.md`

---

### Frontend - Página de Formulario

**Verificar:**

````jsx
import { useState, useEffect } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import { useForm, Controller } from 'react-hook-form';  ✅ react-hook-form
import { yupResolver } from '@hookform/resolvers/yup';  ✅ yup resolver
import * as yup from 'yup';
import {
  Box,
  Button,
  Paper,
  TextField,
  Typography,
  Grid,
} from '@mui/material';
import { Save as SaveIcon, Cancel as CancelIcon } from '@mui/icons-material';
import productoService from '../services/productoService';

/**
 * Esquema de validación para el formulario de producto.
 */
const schema = yup.object({                ✅ Esquema Yup
  nombre: yup
    .string()
    .required('El nombre es obligatorio')
    .min(3, 'Mínimo 3 caracteres')
    .max(100, 'Máximo 100 caracteres'),
  descripcion: yup
    .string()
    .max(500, 'Máximo 500 caracteres'),
  precio: yup
    .number()
    .required('El precio es obligatorio')
    .positive('Debe ser mayor a 0')
    .typeError('Ingrese un número válido'),
  stock: yup
    .number()
    .min(0, 'No puede ser negativo')
    .integer('Debe ser un número entero')
    .typeError('Ingrese un número válido'),
}).required();

/**
 * Página de formulario para crear/editar producto.
 * 
 * @author UDA
 * @version 1.0.0
 */
function ProductoForm() {
  const { id } = useParams();
  const navigate = useNavigate();
  const [loading, setLoading] = useState(false);
  const isEdit = Boolean(id);              ✅ Detecta modo

  const {
    control,
    handleSubmit,
    reset,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(schema),         ✅ Usa yupResolver
    defaultValues: {
      nombre: '',
      descripcion: '',
      precio: '',
      stock: 0,
    },
  });

  useEffect(() => {
    if (isEdit) {
      loadProducto();                      ✅ Carga datos en edición
    }
  }, [id]);

  const loadProducto = async () => {
    try {
      setLoading(true);
      const data = await productoService.getById(id);
      reset(data);                         ✅ Resetea formulario con datos
    } catch (error) {
      console.error('Error al cargar producto:', error);
      alert('Error al cargar el producto');
      navigate('/productos');
    } finally {
      setLoading(false);
    }
  };

  const onSubmit = async (data) => {
    try {
      setLoading(true);
      if (isEdit) {
        await productoService.update(id, data);
        alert('Producto actualizado correctamente');
      } else {
        await productoService.create(data);
        alert('Producto creado correctamente');
      }
      navigate('/productos');              ✅ Navega después de guardar
    } catch (error) {
      console.error('Error al guardar:', error);
      alert('Error al guardar el producto');
    } finally {
      setLoading(false);
    }
  };

  if (loading && isEdit) {
    return (
      <Box display="flex" justifyContent="center" alignItems="center" minHeight="400px">
        <CircularProgress />
      </Box>
    );
  }

  return (
    <Container maxWidth="md" sx={{ mt: 4 }}>
      <Typography variant="h4" component="h1" mb={3}>
        {isEdit ? 'Editar Producto' : 'Nuevo Producto'}  ✅ Título dinámico
      </Typography>

      <Paper sx={{ p: 3 }}>
        <form onSubmit={handleSubmit(onSubmit)}>  ✅ handleSubmit de react-hook-form
          <Grid container spacing={3}>
            <Grid item xs={12}>
              <Controller                  ✅ Controller de react-hook-form
                name="nombre"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}             ✅ Spread field props
                    label="Nombre"
                    fullWidth
                    required
                    error={!!errors.nombre}  ✅ Muestra error
                    helperText={errors.nombre?.message}  ✅ Mensaje de error
                  />
                )}
              />
            </Grid>

            <Grid item xs={12}>
              <Controller
                name="descripcion"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}
                    label="Descripción"
                    fullWidth
                    multiline
                    rows={3}
                    error={!!errors.descripcion}
                    helperText={errors.descripcion?.message}
                  />
                )}
              />
            </Grid>

            <Grid item xs={12} sm={6}>
              <Controller
                name="precio"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}
                    label="Precio"
                    fullWidth
                    required
                    type="number"
                    error={!!errors.precio}
                    helperText={errors.precio?.message}
                  />
                )}
              />
            </Grid>

            <Grid item xs={12} sm={6}>
              <Controller
                name="stock"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}
                    label="Stock"
                    fullWidth
                    type="number"
                    error={!!errors.stock}
                    helperText={errors.stock?.message}
                  />
                )}
              />
            </Grid>

            <Grid item xs={12}>
              <Box display="flex" gap={2} justifyContent="flex-end">
                <Button
                  variant="outlined"
                  startIcon={<CancelIcon />}
                  onClick={() => navigate('/productos')}
                  disabled={loading}
                >
                  Cancelar
                </Button>
                <Button
                  type="submit"
                  variant="contained"
                  startIcon={<SaveIcon />}
                  disabled={loading}
                >
                  {loading ? 'Guardando...' : 'Guardar'}  ✅ Texto dinámico
                </Button>
              </Box>
            </Grid>
          </Grid>
        </form>
      </Paper>
    </Container>
  );
}

export default ProductoForm;
```

**Checklist:**
- [ ] Usa `react-hook-form` con `useForm`
- [ ] Usa `yupResolver` con esquema Yup
- [ ] Esquema Yup definido fuera del componente
- [ ] Validaciones completas en esquema Yup
- [ ] Todos los campos usan `Controller`
- [ ] Todos los campos muestran errores (`error` y `helperText`)
- [ ] Detecta modo edición/creación con `useParams`
- [ ] Carga datos en modo edición
- [ ] Usa `reset()` para cargar datos
- [ ] Navega después de guardar
- [ ] Deshabilita botones durante loading
- [ ] Usa `Grid` para layout responsive
- [ ] Importa **solo** componentes de MUI
- [ ] NO usa HTML nativo
- [ ] Tiene JSDoc

---

## ✅ PASO 4: Validación de Base de Datos

### Scripts SQL - Estructura

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

**Checklist:**
- [ ] Carpetas `schema/` y `data/` existen
- [ ] Scripts numerados correctamente (01, 02, 03, 04)
- [ ] Al menos un script de datos iniciales

---

### Script de Tablas

**Verificar:**

```sql
-- 01_create_tables.sql

-- Crear tabla PRODUCTOS
CREATE TABLE PRODUCTOS (                   ✅ Nombre en MAYÚSCULAS
    ID NUMBER(19) NOT NULL,                ✅ ID tipo NUMBER(19)
    NOMBRE VARCHAR2(100) NOT NULL,         ✅ Columnas en MAYÚSCULAS
    DESCRIPCION VARCHAR2(500),
    PRECIO NUMBER(10,2) NOT NULL,
    STOCK NUMBER(10) DEFAULT 0,
    ACTIVE NUMBER(1) DEFAULT 1 NOT NULL,   ✅ Campo ACTIVE
    CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,  ✅ CREATED_AT
    UPDATED_AT TIMESTAMP                   ✅ UPDATED_AT
);

-- Comentarios
COMMENT ON TABLE PRODUCTOS IS 'Tabla de productos del sistema';  ✅ Comentario de tabla
COMMENT ON COLUMN PRODUCTOS.ID IS 'Identificador único del producto';  ✅ Comentarios de columnas
COMMENT ON COLUMN PRODUCTOS.NOMBRE IS 'Nombre del producto';
COMMENT ON COLUMN PRODUCTOS.ACTIVE IS 'Indica si el producto está activo (1) o inactivo (0)';
```

**Checklist:**
- [ ] Nombres de tablas en MAYÚSCULAS
- [ ] Nombres de columnas en MAYÚSCULAS
- [ ] ID tipo `NUMBER(19)`
- [ ] Strings tipo `VARCHAR2(n)`
- [ ] Decimales tipo `NUMBER(p,s)`
- [ ] Booleanos tipo `NUMBER(1)`
- [ ] Fechas tipo `TIMESTAMP`
- [ ] Tiene campo `ACTIVE NUMBER(1) DEFAULT 1`
- [ ] Tiene campo `CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP`
- [ ] Tiene campo `UPDATED_AT TIMESTAMP`
- [ ] Tiene comentarios en tabla y columnas

---

### Script de Secuencias

**Verificar:**

```sql
-- 02_create_sequences.sql

-- Crear secuencia para PRODUCTOS
CREATE SEQUENCE PRODUCTOS_SEQ              ✅ Formato [TABLA]_SEQ
    START WITH 1                           ✅ START WITH 1
    INCREMENT BY 1                         ✅ INCREMENT BY 1
    NOCACHE                                ✅ NOCACHE
    NOCYCLE;                               ✅ NOCYCLE
```

**Checklist:**
- [ ] Una secuencia por tabla
- [ ] Formato: `[TABLA]_SEQ`
- [ ] `START WITH 1`
- [ ] `INCREMENT BY 1`
- [ ] `NOCACHE`
- [ ] `NOCYCLE`

---

### Script de Índices

**Verificar:**

```sql
-- 03_create_indexes.sql

-- Índices para PRODUCTOS
CREATE INDEX IDX_PRODUCTOS_NOMBRE ON PRODUCTOS(NOMBRE);  ✅ Formato IDX_[TABLA]_[COLUMNA]
CREATE INDEX IDX_PRODUCTOS_ACTIVE ON PRODUCTOS(ACTIVE);  ✅ Índice en ACTIVE
CREATE INDEX IDX_PRODUCTOS_CREATED_AT ON PRODUCTOS(CREATED_AT);  ✅ Índice en CREATED_AT
```

**Checklist:**
- [ ] Formato: `IDX_[TABLA]_[COLUMNA]`
- [ ] Índice en columnas de búsqueda frecuente
- [ ] Índice en `ACTIVE`
- [ ] Índice en Foreign Keys (si existen)

---

### Script de Constraints

**Verificar:**

```sql
-- 04_create_constraints.sql

-- Primary Key
ALTER TABLE PRODUCTOS
    ADD CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID);  ✅ Formato PK_[TABLA]

-- Check Constraints
ALTER TABLE PRODUCTOS
    ADD CONSTRAINT CK_PRODUCTOS_ACTIVE CHECK (ACTIVE IN (0, 1));  ✅ Formato CK_[TABLA]_[COLUMNA]

ALTER TABLE PRODUCTOS
    ADD CONSTRAINT CK_PRODUCTOS_PRECIO CHECK (PRECIO > 0);

ALTER TABLE PRODUCTOS
    ADD CONSTRAINT CK_PRODUCTOS_STOCK CHECK (STOCK >= 0);

-- Foreign Keys (si existen)
-- ALTER TABLE PRODUCTOS
--     ADD CONSTRAINT FK_PRODUCTOS_CATEGORIA FOREIGN KEY (CATEGORIA_ID)
--     REFERENCES CATEGORIAS(ID);           ✅ Formato FK_[TABLA]_[REFERENCIA]

-- Unique Constraints (si existen)
-- ALTER TABLE PRODUCTOS
--     ADD CONSTRAINT UK_PRODUCTOS_SKU UNIQUE (SKU);  ✅ Formato UK_[TABLA]_[COLUMNA]
```

**Checklist:**
- [ ] Primary Key: `PK_[TABLA]`
- [ ] Foreign Keys: `FK_[TABLA]_[REFERENCIA]`
- [ ] Unique: `UK_[TABLA]_[COLUMNA]`
- [ ] Check: `CK_[TABLA]_[COLUMNA]`
- [ ] Check en `ACTIVE IN (0, 1)`
- [ ] Todos los constraints tienen nombres

---

### Script de Datos Iniciales

**Verificar:**

```sql
-- 01_insert_roles.sql

-- Insertar roles iniciales
INSERT INTO ROLES (ID, NOMBRE, DESCRIPCION)
VALUES (ROLES_SEQ.NEXTVAL, 'ROLE_USER', 'Usuario estándar del sistema');  ✅ Usa secuencia

INSERT INTO ROLES (ID, NOMBRE, DESCRIPCION)
VALUES (ROLES_SEQ.NEXTVAL, 'ROLE_ADMIN', 'Administrador del sistema');

INSERT INTO ROLES (ID, NOMBRE, DESCRIPCION)
VALUES (ROLES_SEQ.NEXTVAL, 'ROLE_MODERATOR', 'Moderador del sistema');

COMMIT;                                    ✅ COMMIT al final
```

**Checklist:**
- [ ] Usa secuencias para IDs (`[TABLA]_SEQ.NEXTVAL`)
- [ ] Inserta roles básicos (ROLE_USER, ROLE_ADMIN)
- [ ] Tiene `COMMIT` al final
- [ ] NO usa IDs hardcodeados

---

## ✅ PASO 5: Validación de Documentación

### README.md

**Verificar que existe y contiene:**

```markdown
# [Nombre del Proyecto]

Descripción breve del proyecto.

## 📋 Características

- ✅ CRUD completo de [entidades]
- ✅ Autenticación con JWT
- ✅ Interfaz responsive con Material-UI
- ✅ Base de datos Oracle/H2

## 🚀 Tecnologías

### Backend
- Java 21
- Spring Boot 3.5.0
- Oracle Database / H2
- Maven

### Frontend
- React 18
- Material-UI 5
- Vite

## 📦 Requisitos Previos

- Java 21 o superior
- Node.js 18 o superior
- Maven 3.9 o superior
- Oracle Database 19c+ (producción) / H2 (desarrollo)

## 🔧 Instalación

### Backend

1. Clonar el repositorio
2. Configurar base de datos
3. Ejecutar: `mvn spring-boot:run`

### Frontend

1. Navegar a `frontend/`
2. Ejecutar: `npm install`
3. Ejecutar: `npm run dev`

## ⚙️ Configuración

### Variables de Entorno

- `DB_HOST`: Host de la base de datos
- `DB_PORT`: Puerto de la base de datos
- `DB_USERNAME`: Usuario de la base de datos
- `DB_PASSWORD`: Contraseña de la base de datos
- `JWT_SECRET`: Secreto para JWT

## 📖 Uso

Acceder a:
- Frontend: http://localhost:5173
- Backend: http://localhost:8080
- H2 Console: http://localhost:8080/h2-console

## 🧪 Testing

### Backend
```bash
mvn test
```

### Frontend
```bash
npm test
```

## 🚢 Despliegue

### Generar WAR
```bash
mvn clean package
```

El archivo WAR se genera en `target/[nombre-proyecto].war`

## 📝 Licencia

[Tipo de licencia]

## 👥 Autores

- Universidad del Azuay
```

**Checklist:**
- [ ] README.md existe en la raíz
- [ ] Tiene título y descripción
- [ ] Lista características principales
- [ ] Lista tecnologías usadas
- [ ] Incluye requisitos previos
- [ ] Incluye instrucciones de instalación (backend y frontend)
- [ ] Incluye instrucciones de configuración
- [ ] Incluye instrucciones de uso
- [ ] Incluye instrucciones de testing
- [ ] Incluye instrucciones de despliegue
- [ ] Formato Markdown correcto

---

### JavaDoc y JSDoc

**Backend - Verificar que TODAS las clases públicas tienen:**

```java
/**
 * Descripción de la clase.
 * Explicación adicional si es necesario.
 * 
 * @author UDA
 * @version 1.0.0
 */
public class MiClase {
    
    /**
     * Descripción del método.
     * 
     * @param parametro Descripción del parámetro
     * @return Descripción del retorno
     * @throws ExcepcionTipo Cuándo se lanza
     */
    public TipoRetorno miMetodo(TipoParametro parametro) {
        // ...
    }
}
```

**Frontend - Verificar que TODAS las funciones exportadas tienen:**

```javascript
/**
 * Descripción de la función.
 * 
 * @param {tipo} parametro - Descripción del parámetro
 * @returns {tipo} Descripción del retorno
 * 
 * @example
 * const resultado = miFuncion(valor);
 */
export const miFuncion = (parametro) => {
  // ...
};
```

**Checklist:**
- [ ] Todas las clases públicas tienen JavaDoc
- [ ] Todos los métodos públicos tienen JavaDoc con `@param`, `@return`, `@throws`
- [ ] Todas las funciones exportadas tienen JSDoc
- [ ] Todos los servicios tienen JSDoc con `@param` y `@returns`
- [ ] Todos los componentes React tienen JSDoc

---

## ✅ PASO 6: Validación de Calidad de Código

### Nomenclatura

**Backend:**

```java
// ✅ CORRECTO
public class ProductoService { }           // PascalCase
public interface ProductoRepository { }    // PascalCase
public ProductoDTO findById(Long id) { }   // camelCase
private ProductoMapper productoMapper;     // camelCase
public static final String DEFAULT_STATUS = "ACTIVE";  // UPPER_SNAKE_CASE
package com.uda.proyecto.service;          // lowercase

// ❌ INCORRECTO
public class productoService { }           // Debe ser PascalCase
public void FindById() { }                 // Debe ser camelCase
private String DEFAULT_status;             // Debe ser UPPER_SNAKE_CASE
package com.uda.Proyecto.Service;          // Debe ser lowercase
```

**Frontend:**

```javascript
// ✅ CORRECTO
function ProductoList() { }                // PascalCase (componentes)
const productoService = { };               // camelCase (servicios)
const handleSubmit = () => { };            // camelCase (funciones)
const API_BASE_URL = 'http://...';         // UPPER_SNAKE_CASE (constantes)

// ❌ INCORRECTO
function productoList() { }                // Debe ser PascalCase
const ProductoService = { };               // Debe ser camelCase
const HandleSubmit = () => { };            // Debe ser camelCase
const apiBaseUrl = 'http://...';           // Debe ser UPPER_SNAKE_CASE
```

**Base de Datos:**

```sql
-- ✅ CORRECTO
CREATE TABLE PRODUCTOS ( );                -- MAYÚSCULAS
CREATE SEQUENCE PRODUCTOS_SEQ;             -- MAYÚSCULAS
CREATE INDEX IDX_PRODUCTOS_NOMBRE;         -- MAYÚSCULAS
ALTER TABLE PRODUCTOS ADD CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID);  -- MAYÚSCULAS

-- ❌ INCORRECTO
CREATE TABLE productos ( );                -- Debe ser MAYÚSCULAS
CREATE SEQUENCE Productos_Seq;             -- Debe ser MAYÚSCULAS
CREATE INDEX idx_productos_nombre;         -- Debe ser MAYÚSCULAS
```

**Checklist:**
- [ ] Clases Java en PascalCase
- [ ] Métodos Java en camelCase
- [ ] Variables Java en camelCase
- [ ] Constantes Java en UPPER_SNAKE_CASE
- [ ] Paquetes Java en lowercase
- [ ] Componentes React en PascalCase
- [ ] Funciones JS en camelCase
- [ ] Constantes JS en UPPER_SNAKE_CASE
- [ ] Tablas SQL en MAYÚSCULAS
- [ ] Columnas SQL en MAYÚSCULAS
- [ ] Secuencias SQL en MAYÚSCULAS
- [ ] Constraints SQL en MAYÚSCULAS

---

### Inyección de Dependencias

**Verificar que NO se usa `@Autowired` en campos:**

```java
// ✅ CORRECTO - Inyección por constructor
@Service
@RequiredArgsConstructor                   ✅ Lombok genera constructor
public class ProductoServiceImpl {
    private final ProductoRepository repository;  ✅ final
    private final ProductoMapper mapper;          ✅ final
}

// ❌ INCORRECTO - Inyección por campo
@Service
public class ProductoServiceImpl {
    @Autowired                             ❌ NO usar @Autowired en campos
    private ProductoRepository repository;
    
    @Autowired                             ❌ NO usar @Autowired en campos
    private ProductoMapper mapper;
}
```

**Checklist:**
- [ ] NO hay `@Autowired` en campos
- [ ] Todas las dependencias son `final`
- [ ] Usa `@RequiredArgsConstructor` de Lombok
- [ ] O tiene constructor explícito con todas las dependencias

---

### Manejo de Excepciones

**Verificar:**

```java
// ✅ CORRECTO
@Override
public ProductoDTO findById(Long id) {
    return repository.findById(id)
        .map(mapper::toDTO)
        .orElseThrow(() -> new ResourceNotFoundException(  ✅ Lanza excepción específica
            "Producto no encontrado con ID: " + id));
}

// ❌ INCORRECTO
@Override
public ProductoDTO findById(Long id) {
    try {
        Producto producto = repository.findById(id).get();  ❌ Puede lanzar NoSuchElementException
        return mapper.toDTO(producto);
    } catch (Exception e) {                ❌ Catch genérico
        e.printStackTrace();               ❌ printStackTrace
        return null;                       ❌ Retorna null
    }
}
```

**Checklist:**
- [ ] NO usa `try-catch` genérico sin necesidad
- [ ] NO usa `e.printStackTrace()`
- [ ] NO retorna `null` (usa `Optional` o lanza excepción)
- [ ] Lanza excepciones específicas (`ResourceNotFoundException`, etc.)
- [ ] `GlobalExceptionHandler` captura todas las excepciones

---

### Logging

**Verificar:**

```java
// ✅ CORRECTO
@Slf4j                                     ✅ Usa @Slf4j de Lombok
public class ProductoServiceImpl {
    
    public ProductoDTO findById(Long id) {
        log.debug("Buscando producto con ID: {}", id);  ✅ Usa placeholders
        // ...
        log.info("Producto encontrado: {}", producto.getNombre());
    }
}

// ❌ INCORRECTO
public class ProductoServiceImpl {
    
    public ProductoDTO findById(Long id) {
        System.out.println("Buscando producto: " + id);  ❌ System.out
        // ...
        log.info("Producto encontrado: " + producto.getNombre());  ❌ Concatenación
    }
}
```

**Checklist:**
- [ ] NO usa `System.out.println()`
- [ ] Usa `@Slf4j` de Lombok
- [ ] Usa placeholders `{}` (NO concatenación con `+`)
- [ ] Niveles apropiados (DEBUG, INFO, WARN, ERROR)
- [ ] NO loguea información sensible (passwords, tokens)

---

### Validaciones

**Backend - Verificar que TODOS los DTOs tienen validaciones:**

```java
// ✅ CORRECTO
@Data
public class ProductoDTO {
    @NotBlank(message = "El nombre es obligatorio")  ✅ Validación + mensaje
    @Size(min = 3, max = 100)                        ✅ Límites
    private String nombre;
    
    @NotNull(message = "El precio es obligatorio")   ✅ Validación + mensaje
    @DecimalMin(value = "0.01")                      ✅ Validación numérica
    private BigDecimal precio;
}

// ❌ INCORRECTO
@Data
public class ProductoDTO {
    private String nombre;                 ❌ Sin validación
    private BigDecimal precio;             ❌ Sin validación
}
```

**Frontend - Verificar que TODOS los formularios tienen validaciones Yup:**

```javascript
// ✅ CORRECTO
const schema = yup.object({
  nombre: yup
    .string()
    .required('El nombre es obligatorio')  ✅ Validación + mensaje
    .min(3, 'Mínimo 3 caracteres')         ✅ Validación + mensaje
    .max(100, 'Máximo 100 caracteres'),
  precio: yup
    .number()
    .required('El precio es obligatorio')
    .positive('Debe ser mayor a 0')
    .typeError('Ingrese un número válido'),
}).required();

const { control } = useForm({
  resolver: yupResolver(schema),           ✅ Usa yupResolver
});

// ❌ INCORRECTO
const { control } = useForm({              ❌ Sin validación
  defaultValues: { nombre: '', precio: '' }
});
```

**Checklist:**
- [ ] Todos los DTOs tienen validaciones Bean Validation
- [ ] Todos los campos obligatorios tienen `@NotNull` o `@NotBlank`
- [ ] Todos los mensajes de validación son descriptivos
- [ ] Todos los formularios tienen esquema Yup
- [ ] Todos los formularios usan `yupResolver`
- [ ] Todas las validaciones tienen mensajes en español

---

### Transacciones

**Verificar:**

```java
// ✅ CORRECTO
@Service
@Transactional(readOnly = true)            ✅ readOnly a nivel de clase
public class ProductoServiceImpl {
    
    @Override
    public ProductoDTO findById(Long id) {  ✅ Hereda readOnly
        // ...
    }
    
    @Override
    @Transactional                         ✅ Sobrescribe para escritura
    public ProductoDTO create(ProductoDTO dto) {
        // ...
    }
}

// ❌ INCORRECTO
@Service                                   ❌ Sin @Transactional
public class ProductoServiceImpl {
    
    public ProductoDTO findById(Long id) {  ❌ Sin transacción
        // ...
    }
    
    public ProductoDTO create(ProductoDTO dto) {  ❌ Sin transacción
        // ...
    }
}
```

**Checklist:**
- [ ] Service tiene `@Transactional(readOnly = true)` a nivel de clase
- [ ] Métodos de escritura (create, update, delete) tienen `@Transactional` sin readOnly
- [ ] Métodos de lectura heredan `readOnly = true`

---

## ✅ PASO 7: Validación de Seguridad

### CORS

**Verificar en SecurityConfig.java:**

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(List.of(
        "http://localhost:5173",           ✅ Orígenes específicos
        "http://localhost:3000"
    ));
    configuration.setAllowedMethods(Arrays.asList(
        "GET", "POST", "PUT", "DELETE", "OPTIONS"  ✅ Métodos específicos
    ));
    configuration.setAllowedHeaders(List.of("*"));
    configuration.setAllowCredentials(true);
    
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);
    return source;
}
```

**Checklist:**
- [ ] CORS configurado en `SecurityConfig`
- [ ] Orígenes específicos (NO `"*"` en producción)
- [ ] Métodos HTTP específicos
- [ ] `allowCredentials` configurado

---

### Validación de Entrada

**Verificar:**

```java
// ✅ CORRECTO
@PostMapping
public ResponseEntity<ProductoDTO> create(
    @Valid @RequestBody ProductoDTO dto) {  ✅ @Valid presente
    // ...
}

// ❌ INCORRECTO
@PostMapping
public ResponseEntity<ProductoDTO> create(
    @RequestBody ProductoDTO dto) {        ❌ Sin @Valid
    // ...
}
```

**Checklist:**
- [ ] Todos los `@PostMapping` usan `@Valid`
- [ ] Todos los `@PutMapping` usan `@Valid`
- [ ] DTOs tienen validaciones Bean Validation
- [ ] Frontend tiene validaciones Yup

---

### Passwords

**Verificar:**

```java
// ✅ CORRECTO
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();    ✅ BCrypt
}

// En el servicio
String encodedPassword = passwordEncoder.encode(plainPassword);  ✅ Encripta

// ❌ INCORRECTO
usuario.setPassword(plainPassword);        ❌ Password en texto plano
```

**Checklist:**
- [ ] Usa `BCryptPasswordEncoder`
- [ ] Passwords encriptados antes de guardar
- [ ] NO se loguean passwords
- [ ] NO se retornan passwords en DTOs

---

### JWT

**Verificar:**

```java
// ✅ CORRECTO
@Value("${jwt.secret}")                    ✅ Desde configuración
private String secret;

@Value("${jwt.expiration}")                ✅ Desde configuración
private Long expiration;

// ❌ INCORRECTO
private String secret = "mi-secreto";      ❌ Hardcodeado
private Long expiration = 86400000L;       ❌ Hardcodeado
```

**Checklist:**
- [ ] JWT secret desde variable de entorno
- [ ] JWT expiration desde variable de entorno
- [ ] NO hay secretos hardcodeados
- [ ] Tokens tienen expiración

---

## ✅ PASO 8: Validación de UI/UX

### Tema UDA

**Verificar que el tema está completo:**

```
src/theme/
├── index.js                               ✅ Existe
├── palette.js                             ✅ Existe
├── typography.js                          ✅ Existe
├── components.js                          ✅ Existe
└── shadows.js                             ✅ Existe
```

**Verificar colores UDA:**

```javascript
// palette.js
const palette = {
  primary: {
    main: '#1976d2',                       ✅ Color primario UDA
    // ...
  },
  secondary: {
    main: '#dc004e',                       ✅ Color secundario UDA
    // ...
  },
  // ...
};
```

**Checklist:**
- [ ] Los 5 archivos del tema existen
- [ ] Colores UDA configurados
- [ ] Tipografía configurada
- [ ] Overrides de componentes configurados
- [ ] Sombras configuradas
- [ ] Tema importado en `main.jsx`
- [ ] `ThemeProvider` envuelve `<App />`

**📖 Referencia completa:** `UI_STANDARDS.md`

---

### Componentes MUI

**Verificar que NO se usa HTML nativo:**

````jsx
// ✅ CORRECTO
import { Box, Typography, Button } from '@mui/material';

function MiComponente() {
  return (
    <Box>                                  ✅ Box (NO <div>)
      <Typography variant="h4">           ✅ Typography (NO <h1>)
        Título
      </Typography>
      <Button variant="contained">        ✅ Button (NO <button>)
        Guardar
      </Button>
    </Box>
  );
}

// ❌ INCORRECTO
function MiComponente() {
  return (
    <div>                                  ❌ HTML nativo
      <h1>Título</h1>                      ❌ HTML nativo
      <button>Guardar</button>             ❌ HTML nativo
      <input type="text" />                ❌ HTML nativo
      <span>Texto</span>                   ❌ HTML nativo
    </div>
  );
}
```

**Checklist:**
- [ ] NO hay `<div>` (usar `<Box>`)
- [ ] NO hay `<h1>`, `<h2>`, `<p>`, `<span>` (usar `<Typography>`)
- [ ] NO hay `<button>` (usar `<Button>`)
- [ ] NO hay `<input>` (usar `<TextField>`)
- [ ] NO hay `<a>` (usar `<Link>` de MUI o React Router)
- [ ] NO hay `<img>` (usar `<Avatar>` o `<CardMedia>`)
- [ ] NO hay `<table>`, `<tr>`, `<td>` (usar `<Table>` de MUI)
- [ ] NO hay `<form>` sin componentes MUI

---

### Estilos

**Verificar que NO se usan estilos inline:**

```jsx
// ✅ CORRECTO
import { Box } from '@mui/material';

function MiComponente() {
  return (
    <Box
      sx={{                                ✅ Usa sx prop
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        p: 2,                              ✅ Usa spacing del tema (p, m, mt, mb, etc.)
        bgcolor: 'primary.main',           ✅ Usa colores del tema
        borderRadius: 1,                   ✅ Usa valores del tema
      }}
    >
      Contenido
    </Box>
  );
}

// ❌ INCORRECTO
function MiComponente() {
  return (
    <div style={{                          ❌ style inline
      display: 'flex',
      justifyContent: 'center',
      padding: '16px',                     ❌ Valores hardcodeados
      backgroundColor: '#1976d2',          ❌ Colores hardcodeados
      borderRadius: '8px',                 ❌ Valores hardcodeados
    }}>
      Contenido
    </div>
  );
}
```

**Checklist:**
- [ ] NO hay `style={{}}` inline
- [ ] Usa `sx={{}}` prop de MUI
- [ ] NO hay colores hardcodeados (ej: `#1976d2`)
- [ ] Usa colores del tema (ej: `primary.main`, `secondary.light`)
- [ ] NO hay espaciado hardcodeado (ej: `padding: '16px'`)
- [ ] Usa spacing del tema (ej: `p: 2`, `m: 3`, `mt: 1`)
- [ ] NO hay tamaños hardcodeados (ej: `fontSize: '24px'`)
- [ ] Usa variantes de Typography (ej: `variant="h4"`)

---

### Responsive Design

**Verificar:**

```jsx
// ✅ CORRECTO
import { Container, Grid, Box } from '@mui/material';

function MiComponente() {
  return (
    <Container maxWidth="lg">              ✅ Container para ancho máximo
      <Grid container spacing={3}>         ✅ Grid para layout
        <Grid item xs={12} sm={6} md={4}>  ✅ Breakpoints responsive
          <Box>Contenido 1</Box>
        </Grid>
        <Grid item xs={12} sm={6} md={4}>
          <Box>Contenido 2</Box>
        </Grid>
        <Grid item xs={12} sm={6} md={4}>
          <Box>Contenido 3</Box>
        </Grid>
      </Grid>
    </Container>
  );
}

// ❌ INCORRECTO
function MiComponente() {
  return (
    <div style={{ width: '1200px' }}>     ❌ Ancho fijo
      <div style={{ display: 'flex' }}>   ❌ Sin responsive
        <div style={{ width: '400px' }}>  ❌ Ancho fijo
          Contenido 1
        </div>
        <div style={{ width: '400px' }}>
          Contenido 2
        </div>
        <div style={{ width: '400px' }}>
          Contenido 3
        </div>
      </div>
    </div>
  );
}
```

**Checklist:**
- [ ] Usa `Container` para ancho máximo
- [ ] Usa `Grid` para layouts
- [ ] Define breakpoints responsive (`xs`, `sm`, `md`, `lg`, `xl`)
- [ ] NO hay anchos fijos hardcodeados
- [ ] Funciona en móvil (< 600px)
- [ ] Funciona en tablet (600px - 960px)
- [ ] Funciona en desktop (> 960px)

---

### Estados de UI

**Verificar que TODOS los componentes manejan:**

```jsx
// ✅ CORRECTO
function ProductoList() {
  const [loading, setLoading] = useState(false);  ✅ Estado de loading
  const [error, setError] = useState(null);       ✅ Estado de error
  const [productos, setProductos] = useState([]); ✅ Estado de datos

  // Loading state
  if (loading) {
    return (
      <Box display="flex" justifyContent="center" p={4}>
        <CircularProgress />               ✅ Muestra loading
      </Box>
    );
  }

  // Error state
  if (error) {
    return (
      <Alert severity="error">             ✅ Muestra error
        {error}
      </Alert>
    );
  }

  // Empty state
  if (productos.length === 0) {
    return (
      <Alert severity="info">              ✅ Muestra estado vacío
        No hay productos disponibles
      </Alert>
    );
  }

  // Success state
  return (
    <Box>
      {productos.map(producto => (
        <ProductoCard key={producto.id} producto={producto} />
      ))}
    </Box>
  );
}

// ❌ INCORRECTO
function ProductoList() {
  const [productos, setProductos] = useState([]);

  return (
    <div>
      {productos.map(producto => (        ❌ No maneja loading, error, empty
        <div key={producto.id}>{producto.nombre}</div>
      ))}
    </div>
  );
}
```

**Checklist:**
- [ ] Todos los componentes tienen estado de **loading**
- [ ] Todos los componentes tienen estado de **error**
- [ ] Todos los componentes tienen estado **vacío** (empty)
- [ ] Usa `CircularProgress` para loading
- [ ] Usa `Alert` para errores
- [ ] Mensajes de error son descriptivos
- [ ] Botones se deshabilitan durante loading

---

### Accesibilidad

**Verificar:**

```jsx
// ✅ CORRECTO
import { Button, TextField, IconButton } from '@mui/material';
import { Delete as DeleteIcon } from '@mui/icons-material';

function MiComponente() {
  return (
    <>
      <TextField
        label="Nombre"                     ✅ Label descriptivo
        required                           ✅ Indica campo obligatorio
        error={!!errors.nombre}            ✅ Indica error
        helperText={errors.nombre?.message}  ✅ Mensaje de error
        aria-label="Nombre del producto"   ✅ ARIA label
      />
      
      <Button
        variant="contained"
        aria-label="Guardar producto"     ✅ ARIA label
      >
        Guardar
      </Button>
      
      <IconButton
        aria-label="Eliminar producto"    ✅ ARIA label para iconos
        onClick={handleDelete}
      >
        <DeleteIcon />
      </IconButton>
    </>
  );
}

// ❌ INCORRECTO
function MiComponente() {
  return (
    <>
      <input type="text" />                ❌ Sin label
      <button>Guardar</button>             ❌ Sin ARIA label
      <button onClick={handleDelete}>      ❌ Icono sin descripción
        <DeleteIcon />
      </button>
    </>
  );
}
```

**Checklist:**
- [ ] Todos los campos tienen `label`
- [ ] Campos obligatorios tienen `required`
- [ ] Errores se muestran con `error` y `helperText`
- [ ] Botones con solo iconos tienen `aria-label`
- [ ] Navegación por teclado funciona
- [ ] Contraste de colores es suficiente (WCAG 2.1)

---

## ✅ PASO 9: Validación de Testing

### Backend - Tests Unitarios

**Verificar que existen tests para:**

```
src/test/java/com/uda/[proyecto]/
├── service/
│   └── [Entidad]ServiceTest.java          ✅ OBLIGATORIO
├── controller/
│   └── [Entidad]ControllerTest.java       ⚠️ RECOMENDADO
└── repository/
    └── [Entidad]RepositoryTest.java       ⚠️ RECOMENDADO
```

**Verificar estructura de test:**

```java
import org.junit.jupiter.api.Test;         ✅ JUnit 5
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

/**
 * Tests unitarios para ProductoService.
 * 
 * @author UDA
 * @version 1.0.0
 */
@ExtendWith(MockitoExtension.class)        ✅ Mockito extension
class ProductoServiceTest {
    
    @Mock                                   ✅ Mock de dependencias
    private ProductoRepository repository;
    
    @Mock
    private ProductoMapper mapper;
    
    @InjectMocks                            ✅ Clase bajo test
    private ProductoServiceImpl service;
    
    private Producto producto;
    private ProductoDTO productoDTO;
    
    @BeforeEach                             ✅ Setup antes de cada test
    void setUp() {
        producto = Producto.builder()
            .id(1L)
            .nombre("Test")
            .build();
        
        productoDTO = ProductoDTO.builder()
            .id(1L)
            .nombre("Test")
            .build();
    }
    
    @Test                                   ✅ Test method
    void testFindById_Success() {
        // Arrange                          ✅ Patrón AAA
        when(repository.findById(1L)).thenReturn(Optional.of(producto));
        when(mapper.toDTO(producto)).thenReturn(productoDTO);
        
        // Act
        ProductoDTO result = service.findById(1L);
        
        // Assert
        assertNotNull(result);              ✅ Assertions
        assertEquals(1L, result.getId());
        verify(repository, times(1)).findById(1L);  ✅ Verify
    }
    
    @Test
    void testFindById_NotFound() {
        // Arrange
        when(repository.findById(999L)).thenReturn(Optional.empty());
        
        // Act & Assert
        assertThrows(ResourceNotFoundException.class, () -> {
            service.findById(999L);
        });
    }
}
```

**Checklist:**
- [ ] Usa JUnit 5 (`org.junit.jupiter.api.*`)
- [ ] Usa Mockito (`@Mock`, `@InjectMocks`)
- [ ] Usa `@ExtendWith(MockitoExtension.class)`
- [ ] Tiene `@BeforeEach` para setup
- [ ] Tests siguen patrón AAA (Arrange, Act, Assert)
- [ ] Usa `assertNotNull`, `assertEquals`, `assertTrue`, etc.
- [ ] Usa `verify()` para verificar llamadas a mocks
- [ ] Nomenclatura: `test[Metodo]_[Escenario]`
- [ ] Tests para casos exitosos
- [ ] Tests para casos de error
- [ ] Cobertura > 70%

---

### Frontend - Tests

**Verificar que existen tests para:**

```
src/__tests__/
├── services/
│   └── [entidad]Service.test.js           ⚠️ RECOMENDADO
└── pages/
    └── [Entidad]List.test.jsx             ⚠️ RECOMENDADO
```

**Verificar estructura de test:**

```javascript
import { describe, it, expect, vi, beforeEach } from 'vitest';  ✅ Vitest
import { render, screen, waitFor } from '@testing-library/react';  ✅ Testing Library
import { BrowserRouter } from 'react-router-dom';
import ProductoList from '../pages/ProductoList';
import productoService from '../services/productoService';

// Mock del servicio
vi.mock('../services/productoService');    ✅ Mock

/**
 * Tests para ProductoList.
 */
describe('ProductoList', () => {           ✅ describe block
  
  beforeEach(() => {                       ✅ beforeEach
    vi.clearAllMocks();
  });

  it('debe renderizar la lista de productos', async () => {  ✅ it block
    // Arrange
    const mockProductos = [
      { id: 1, nombre: 'Producto 1' },
      { id: 2, nombre: 'Producto 2' },
    ];
    productoService.getAll.mockResolvedValue(mockProductos);

    // Act
    render(
      <BrowserRouter>
        <ProductoList />
      </BrowserRouter>
    );

    // Assert
    await waitFor(() => {                  ✅ waitFor para async
      expect(screen.getByText('Producto 1')).toBeInTheDocument();
      expect(screen.getByText('Producto 2')).toBeInTheDocument();
    });
  });

  it('debe mostrar loading mientras carga', () => {
    productoService.getAll.mockImplementation(() => new Promise(() => {}));

    render(
      <BrowserRouter>
        <ProductoList />
      </BrowserRouter>
    );

    expect(screen.getByRole('progressbar')).toBeInTheDocument();
  });

  it('debe mostrar error cuando falla la carga', async () => {
    productoService.getAll.mockRejectedValue(new Error('Error'));

    render(
      <BrowserRouter>
        <ProductoList />
      </BrowserRouter>
    );

    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });
});
```

**Checklist:**
- [ ] Usa Vitest (`vitest`)
- [ ] Usa React Testing Library (`@testing-library/react`)
- [ ] Usa `describe` y `it` blocks
- [ ] Usa `beforeEach` para setup
- [ ] Mockea servicios con `vi.mock()`
- [ ] Usa `render()` para renderizar componentes
- [ ] Usa `screen` para queries
- [ ] Usa `waitFor()` para operaciones asíncronas
- [ ] Tests para renderizado
- [ ] Tests para loading
- [ ] Tests para errores
- [ ] Tests para interacciones del usuario

---

## ✅ PASO 10: Validación de Configuración

### Variables de Entorno

**Verificar que existe `.env.example`:**

```env
# .env.example

# Base de datos
DB_HOST=localhost
DB_PORT=1521
DB_SID=ORCL
DB_USERNAME=uda_user
DB_PASSWORD=

# JWT
JWT_SECRET=
JWT_EXPIRATION=86400000

# Server
SERVER_PORT=8080

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:5173
```

**Verificar que NO existe `.env` en el repositorio:**

```bash
# .gitignore debe contener:
.env                                       ✅ .env ignorado
.env.local
.env.*.local
```

**Checklist:**
- [ ] `.env.example` existe y está documentado
- [ ] `.env` está en `.gitignore`
- [ ] NO hay credenciales en `.env.example`
- [ ] Todas las variables necesarias están documentadas
- [ ] Variables tienen valores de ejemplo (NO producción)

---

### application.yml

**Verificar:**

```yaml
spring:
  application:
    name: ${APP_NAME:nombre-proyecto}      ✅ Usa variables de entorno
  
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:ORCL}  ✅ Variables
    username: ${DB_USERNAME:uda_user}      ✅ Variables
    password: ${DB_PASSWORD:uda_password}  ✅ Variables
    driver-class-name: oracle.jdbc.OracleDriver
  
  jpa:
    database-platform: org.hibernate.dialect.OracleDialect
    hibernate:
      ddl-auto: validate                   ✅ validate en producción
    show-sql: false                        ✅ false en producción

server:
  port: ${SERVER_PORT:8080}                ✅ Variable

jwt:
  secret: ${JWT_SECRET}                    ✅ Variable (sin default)
  expiration: ${JWT_EXPIRATION:86400000}   ✅ Variable

logging:
  level:
    root: INFO                             ✅ INFO en producción
    com.uda: DEBUG
```

**Checklist:**
- [ ] Usa variables de entorno con `${VAR:default}`
- [ ] Credenciales desde variables (NO hardcodeadas)
- [ ] `ddl-auto: validate` (NO `create` o `update`)
- [ ] `show-sql: false`
- [ ] Logging apropiado (INFO/WARN en producción)
- [ ] JWT secret desde variable (sin default)

---

### package.json

**Verificar:**

```json
{
  "name": "proyecto-frontend",             ✅ Nombre descriptivo
  "version": "1.0.0",                      ✅ Versión semántica
  "type": "module",                        ✅ ES modules
  "scripts": {
    "dev": "vite",                         ✅ Script dev
    "build": "vite build",                 ✅ Script build
    "preview": "vite preview",             ✅ Script preview
    "test": "vitest",                      ✅ Script test
    "lint": "eslint ."                     ✅ Script lint
  },
  "dependencies": {
    "react": "^18.3.0",                    ✅ React 18+
    "react-dom": "^18.3.0",
    "react-router-dom": "^6.22.0",         ✅ React Router 6+
    "@mui/material": "^5.15.0",            ✅ MUI 5+
    "@mui/icons-material": "^5.15.0",
    "@emotion/react": "^11.11.0",
    "@emotion/styled": "^11.11.0",
    "axios": "^1.6.0",                     ✅ Axios
    "react-hook-form": "^7.50.0",          ✅ React Hook Form
    "yup": "^1.3.0"                        ✅ Yup
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.0",
    "vite": "^5.1.0",                      ✅ Vite 5+
    "vitest": "^1.3.0",                    ✅ Vitest
    "@testing-library/react": "^14.2.0",   ✅ Testing Library
    "eslint": "^8.56.0"                    ✅ ESLint
  }
}
```

**Checklist:**
- [ ] Nombre descriptivo
- [ ] Versión semántica (1.0.0)
- [ ] `"type": "module"`
- [ ] Scripts: dev, build, preview, test, lint
- [ ] Dependencias obligatorias presentes:
  - [ ] React 18+
  - [ ] React Router 6+
  - [ ] MUI 5+
  - [ ] Axios
  - [ ] React Hook Form
  - [ ] Yup
  - [ ] Vite 5+
  - [ ] Vitest
  - [ ] Testing Library
  - [ ] ESLint

---

### pom.xml

**Verificar:**

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.0</version>                ✅ Spring Boot 3.5.0
    <relativePath/>
</parent>

<groupId>com.uda</groupId>                  ✅ groupId correcto
<artifactId>nombre-proyecto</artifactId>    ✅ artifactId descriptivo
<version>1.0.0</version>                    ✅ Versión semántica
<packaging>war</packaging>                  ✅ WAR packaging

<properties>
    <java.version>21</java.version>         ✅ Java 21
    <mapstruct.version>1.5.5.Final</mapstruct.version>
    <lombok.version>1.18.30</lombok.version>
</properties>

<dependencies>
    <!-- Spring Boot Starters -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>  ✅ Web
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>  ✅ JPA
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>  ✅ Security
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>  ✅ Validation
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <scope>provided</scope>             ✅ Tomcat provided
    </dependency>
    
    <!-- Oracle -->
    <dependency>
        <groupId>com.oracle.database.jdbc</groupId>
        <artifactId>ojdbc11</artifactId>    ✅ Oracle JDBC
    </dependency>
    
    <!-- Lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>     ✅ Lombok
        <scope>provided</scope>
    </dependency>
    
    <!-- MapStruct -->
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>  ✅ MapStruct
    </dependency>
    
    <!-- Testing -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>  ✅ Test
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <finalName>${project.artifactId}</finalName>  ✅ Sin versión en nombre
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>21</source>         ✅ Java 21
                <target>21</target>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </path>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                    </path>
                </annotationProcessorPaths>  ✅ Annotation processors
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Checklist:**
- [ ] Spring Boot 3.5.0
- [ ] Java 21
- [ ] `<packaging>war</packaging>`
- [ ] Dependencias obligatorias presentes:
  - [ ] spring-boot-starter-web
  - [ ] spring-boot-starter-data-jpa
  - [ ] spring-boot-starter-security
  - [ ] spring-boot-starter-validation
  - [ ] spring-boot-starter-tomcat (provided)
  - [ ] ojdbc11
  - [ ] lombok
  - [ ] mapstruct
  - [ ] spring-boot-starter-test
- [ ] Annotation processors configurados (Lombok, MapStruct)
- [ ] `<finalName>` sin versión

---

## ✅ PASO 11: Validación Final

### Compilación

**Backend:**

```bash
cd backend
mvn clean compile                          ✅ Debe compilar sin errores
```

**Verificar:**
- [ ] Compila sin errores
- [ ] NO hay warnings críticos
- [ ] MapStruct genera mappers correctamente

**Frontend:**

```bash
cd frontend
npm install                                ✅ Instala dependencias
npm run build                              ✅ Debe compilar sin errores
```

**Verificar:**
- [ ] Instala sin errores
- [ ] Compila sin errores
- [ ] NO hay warnings críticos
- [ ] Genera bundle en `dist/`

---

### Ejecución

**Backend:**

```bash
mvn spring-boot:run                        ✅ Debe iniciar sin errores
```

**Verificar:**
- [ ] Inicia sin errores
- [ ] Se conecta a la base de datos
- [ ] Endpoints responden correctamente
- [ ] Swagger UI accesible (si está configurado)

**Frontend:**

```bash
npm run dev                                ✅ Debe iniciar sin errores
```

**Verificar:**
- [ ] Inicia sin errores
- [ ] Abre en navegador
- [ ] NO hay errores en consola
- [ ] Se conecta al backend correctamente

---

### Tests

**Backend:**

```bash
mvn test                                   ✅ Todos los tests deben pasar
```

**Verificar:**
- [ ] Todos los tests pasan
- [ ] Cobertura > 70%
- [ ] NO hay tests ignorados sin razón

**Frontend:**

```bash
npm test                                   ✅ Todos los tests deben pasar
```

**Verificar:**
- [ ] Todos los tests pasan
- [ ] NO hay tests ignorados sin razón

---

### Funcionalidad CRUD

**Verificar manualmente:**

1. **Crear:**
   - [ ] Formulario de creación funciona
   - [ ] Validaciones funcionan
   - [ ] Se guarda en base de datos
   - [ ] Redirige a listado
   - [ ] Muestra mensaje de éxito

2. **Leer:**
   - [ ] Listado muestra todos los registros
   - [ ] Búsqueda funciona
   - [ ] Paginación funciona (si existe)
   - [ ] Muestra loading mientras carga
   - [ ] Muestra mensaje si no hay datos

3. **Actualizar:**
   - [ ] Formulario de edición carga datos
   - [ ] Validaciones funcionan
   - [ ] Se actualiza en base de datos
   - [ ] Redirige a listado
   - [ ] Muestra mensaje de éxito

4. **Eliminar:**
   - [ ] Muestra confirmación
   - [ ] Se elimina de base de datos (soft delete)
   - [ ] Actualiza listado
   - [ ] Muestra mensaje de éxito

---

## 📊 Resumen de Validación

### Checklist General

**Estructura:**
- [ ] Estructura de carpetas correcta
- [ ] Todos los archivos obligatorios existen
- [ ] Nomenclatura consistente

**Backend:**
- [ ] Compila sin errores
- [ ] Todos los tests pasan
- [ ] Cobertura > 70%
- [ ] Usa `jakarta.*` (NO `javax.*`)
- [ ] Inyección por constructor
- [ ] Logging con `@Slf4j`
- [ ] Transacciones configuradas
- [ ] Validaciones en DTOs
- [ ] Manejo de excepciones
- [ ] JavaDoc completo

**Frontend:**
- [ ] Compila sin errores
- [ ] Todos los tests pasan
- [ ] Solo componentes MUI (NO HTML nativo)
- [ ] Estilos con `sx` (NO `style` inline)
- [ ] Validaciones con Yup
- [ ] Formularios con React Hook Form
- [ ] Maneja loading, error, empty states
- [ ] Responsive design
- [ ] JSDoc completo

**Base de Datos:**
- [ ] Scripts SQL en MAYÚSCULAS
- [ ] Secuencias configuradas
- [ ] Índices creados
- [ ] Constraints con nombres
- [ ] Comentarios en tablas y columnas

**Documentación:**
- [ ] README.md completo
- [ ] JavaDoc en todas las clases públicas
- [ ] JSDoc en todas las funciones exportadas
- [ ] Variables de entorno documentadas

**Seguridad:**
- [ ] CORS configurado
- [ ] Validación de entrada
- [ ] Passwords encriptados
- [ ] JWT configurado
- [ ] NO hay secretos hardcodeados

**Calidad:**
- [ ] NO hay `System.out.println()`
- [ ] NO hay `@Autowired` en campos
- [ ] NO hay `try-catch` genérico innecesario
- [ ] NO hay código comentado
- [ ] NO hay TODOs

---

## 🎯 Criterios de Aceptación

Para que una aplicación generada sea considerada **COMPLETA y CORRECTA**, debe cumplir:

### ✅ Criterios Obligatorios (100%)

1. **Compila sin errores** (Backend y Frontend)
2. **Ejecuta sin errores** (Backend y Frontend)
3. **CRUD completo funciona** (Crear, Leer, Actualizar, Eliminar)
4. **Estructura correcta** según `ESTRUCTURA_PROYECTO.md`
5. **Usa solo componentes MUI** (NO HTML nativo en Frontend)
6. **Validaciones completas** (Backend con Bean Validation, Frontend con Yup)
7. **Manejo de errores** (GlobalExceptionHandler en Backend, estados en Frontend)
8. **Base de datos Oracle** con scripts SQL correctos
9. **README.md completo** con instrucciones de instalación
10. **JavaDoc y JSDoc** en todas las clases/funciones públicas

### ⚠️ Criterios Recomendados (80%)

1. **Tests unitarios** con cobertura > 70%
2. **Logging apropiado** (NO `System.out.println()`)
3. **Seguridad configurada** (JWT, CORS, validaciones)
4. **Responsive design** (funciona en móvil, tablet, desktop)
5. **Estados de UI** (loading, error, empty)
6. **Documentación adicional** (API.md, DEPLOYMENT.md)
7. **Variables de entorno** documentadas en `.env.example`
8. **Transacciones** configuradas correctamente
9. **Paginación** implementada (si hay listados grandes)
10. **Accesibilidad** (ARIA labels, navegación por teclado)

---

## 🚨 Errores Comunes a Evitar

### Backend

#### ❌ Error 1: Usar `javax.*` en lugar de `jakarta.*`

```java
// ❌ INCORRECTO
import javax.persistence.Entity;
import javax.validation.constraints.NotNull;

// ✅ CORRECTO
import jakarta.persistence.Entity;
import jakarta.validation.constraints.NotNull;
```

**Solución:** Spring Boot 3.x usa Jakarta EE 9+, que cambió el namespace de `javax.*` a `jakarta.*`

---

#### ❌ Error 2: Usar `@Autowired` en campos

```java
// ❌ INCORRECTO
@Service
public class ProductoServiceImpl {
    @Autowired
    private ProductoRepository repository;
}

// ✅ CORRECTO
@Service
@RequiredArgsConstructor
public class ProductoServiceImpl {
    private final ProductoRepository repository;
}
```

**Solución:** Usar inyección por constructor con `@RequiredArgsConstructor` de Lombok

---

#### ❌ Error 3: Exponer entidades en Controllers

```java
// ❌ INCORRECTO
@GetMapping("/{id}")
public ResponseEntity<Producto> findById(@PathVariable Long id) {
    return ResponseEntity.ok(repository.findById(id).get());
}

// ✅ CORRECTO
@GetMapping("/{id}")
public ResponseEntity<ProductoDTO> findById(@PathVariable Long id) {
    return ResponseEntity.ok(service.findById(id));
}
```

**Solución:** Siempre usar DTOs en la capa de presentación

---

#### ❌ Error 4: No validar DTOs

```java
// ❌ INCORRECTO
@PostMapping
public ResponseEntity<ProductoDTO> create(@RequestBody ProductoDTO dto) {
    return ResponseEntity.ok(service.create(dto));
}

// ✅ CORRECTO
@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {
    return ResponseEntity.ok(service.create(dto));
}
```

**Solución:** Siempre usar `@Valid` en `@PostMapping` y `@PutMapping`

---

#### ❌ Error 5: Retornar `null`

```java
// ❌ INCORRECTO
public ProductoDTO findById(Long id) {
    Producto producto = repository.findById(id).orElse(null);
    return producto != null ? mapper.toDTO(producto) : null;
}

// ✅ CORRECTO
public ProductoDTO findById(Long id) {
    return repository.findById(id)
        .map(mapper::toDTO)
        .orElseThrow(() -> new ResourceNotFoundException("Producto no encontrado"));
}
```

**Solución:** Usar `Optional` y lanzar excepciones específicas

---

#### ❌ Error 6: Usar `System.out.println()`

```java
// ❌ INCORRECTO
public void create(ProductoDTO dto) {
    System.out.println("Creando producto: " + dto.getNombre());
    // ...
}

// ✅ CORRECTO
@Slf4j
public class ProductoServiceImpl {
    public void create(ProductoDTO dto) {
        log.info("Creando producto: {}", dto.getNombre());
        // ...
    }
}
```

**Solución:** Usar `@Slf4j` de Lombok y placeholders `{}`

---

#### ❌ Error 7: Hardcodear credenciales

```java
// ❌ INCORRECTO
spring.datasource.url=jdbc:oracle:thin:@localhost:1521:ORCL
spring.datasource.username=uda_user
spring.datasource.password=uda_password

// ✅ CORRECTO
spring.datasource.url=jdbc:oracle:thin:@${DB_HOST}:${DB_PORT}:${DB_SID}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```

**Solución:** Usar variables de entorno

---

#### ❌ Error 8: No usar transacciones

```java
// ❌ INCORRECTO
@Service
public class ProductoServiceImpl {
    public ProductoDTO create(ProductoDTO dto) {
        // ...
    }
}

// ✅ CORRECTO
@Service
@Transactional(readOnly = true)
public class ProductoServiceImpl {
    
    @Transactional
    public ProductoDTO create(ProductoDTO dto) {
        // ...
    }
}
```

**Solución:** Usar `@Transactional` a nivel de clase y método

---

### Frontend

#### ❌ Error 1: Usar HTML nativo

```jsx
// ❌ INCORRECTO
function MiComponente() {
  return (
    <div>
      <h1>Título</h1>
      <button>Guardar</button>
      <input type="text" />
    </div>
  );
}

// ✅ CORRECTO
import { Box, Typography, Button, TextField } from '@mui/material';

function MiComponente() {
  return (
    <Box>
      <Typography variant="h4">Título</Typography>
      <Button variant="contained">Guardar</Button>
      <TextField />
    </Box>
  );
}
```

**Solución:** Usar solo componentes de Material-UI

---

#### ❌ Error 2: Estilos inline

```jsx
// ❌ INCORRECTO
<div style={{ padding: '16px', backgroundColor: '#1976d2' }}>
  Contenido
</div>

// ✅ CORRECTO
<Box sx={{ p: 2, bgcolor: 'primary.main' }}>
  Contenido
</Box>
```

**Solución:** Usar `sx` prop y valores del tema

---

#### ❌ Error 3: No manejar estados de UI

```jsx
// ❌ INCORRECTO
function ProductoList() {
  const [productos, setProductos] = useState([]);
  
  return (
    <div>
      {productos.map(p => <div key={p.id}>{p.nombre}</div>)}
    </div>
  );
}

// ✅ CORRECTO
function ProductoList() {
  const [productos, setProductos] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  if (loading) return <CircularProgress />;
  if (error) return <Alert severity="error">{error}</Alert>;
  if (productos.length === 0) return <Alert severity="info">No hay datos</Alert>;
  
  return (
    <Box>
      {productos.map(p => <ProductoCard key={p.id} producto={p} />)}
    </Box>
  );
}
```

**Solución:** Manejar loading, error y empty states

---

#### ❌ Error 4: No validar formularios

```jsx
// ❌ INCORRECTO
function ProductoForm() {
  const { control } = useForm();
  
  return (
    <form>
      <Controller name="nombre" control={control} render={({ field }) => (
        <TextField {...field} />
      )} />
    </form>
  );
}

// ✅ CORRECTO
const schema = yup.object({
  nombre: yup.string().required('El nombre es obligatorio'),
}).required();

function ProductoForm() {
  const { control } = useForm({
    resolver: yupResolver(schema),
  });
  
  return (
    <form>
      <Controller name="nombre" control={control} render={({ field }) => (
        <TextField {...field} error={!!errors.nombre} helperText={errors.nombre?.message} />
      )} />
    </form>
  );
}
```

**Solución:** Usar Yup para validaciones

---

#### ❌ Error 5: No usar `key` en listas

```jsx
// ❌ INCORRECTO
{productos.map(producto => (
  <ProductoCard producto={producto} />
))}

// ✅ CORRECTO
{productos.map(producto => (
  <ProductoCard key={producto.id} producto={producto} />
))}
```

**Solución:** Siempre usar `key` única en listas

---

#### ❌ Error 6: Mutar estado directamente

```jsx
// ❌ INCORRECTO
const handleAdd = () => {
  productos.push(newProducto);
  setProductos(productos);
};

// ✅ CORRECTO
const handleAdd = () => {
  setProductos([...productos, newProducto]);
};
```

**Solución:** Crear nuevo array/objeto, no mutar el existente

---

#### ❌ Error 7: No limpiar efectos

```jsx
// ❌ INCORRECTO
useEffect(() => {
  const interval = setInterval(() => {
    fetchData();
  }, 5000);
}, []);

// ✅ CORRECTO
useEffect(() => {
  const interval = setInterval(() => {
    fetchData();
  }, 5000);
  
  return () => clearInterval(interval);
}, []);
```

**Solución:** Retornar función de limpieza en `useEffect`

---

### Base de Datos

#### ❌ Error 1: Nombres en minúsculas

```sql
-- ❌ INCORRECTO
CREATE TABLE productos (
    id NUMBER(19),
    nombre VARCHAR2(100)
);

-- ✅ CORRECTO
CREATE TABLE PRODUCTOS (
    ID NUMBER(19),
    NOMBRE VARCHAR2(100)
);
```

**Solución:** Usar MAYÚSCULAS para tablas y columnas

---

#### ❌ Error 2: Sin secuencias

```sql
-- ❌ INCORRECTO
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) PRIMARY KEY,
    NOMBRE VARCHAR2(100)
);

-- ✅ CORRECTO
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1 INCREMENT BY 1;

CREATE TABLE PRODUCTOS (
    ID NUMBER(19) PRIMARY KEY,
    NOMBRE VARCHAR2(100)
);
```

**Solución:** Crear secuencia para cada tabla

---

#### ❌ Error 3: Sin constraints con nombres

```sql
-- ❌ INCORRECTO
ALTER TABLE PRODUCTOS ADD PRIMARY KEY (ID);
ALTER TABLE PRODUCTOS ADD CHECK (PRECIO > 0);

-- ✅ CORRECTO
ALTER TABLE PRODUCTOS ADD CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID);
ALTER TABLE PRODUCTOS ADD CONSTRAINT CK_PRODUCTOS_PRECIO CHECK (PRECIO > 0);
```

**Solución:** Dar nombres a todos los constraints

---

#### ❌ Error 4: IDs hardcodeados

```sql
-- ❌ INCORRECTO
INSERT INTO PRODUCTOS (ID, NOMBRE) VALUES (1, 'Producto 1');
INSERT INTO PRODUCTOS (ID, NOMBRE) VALUES (2, 'Producto 2');

-- ✅ CORRECTO
INSERT INTO PRODUCTOS (ID, NOMBRE) VALUES (PRODUCTOS_SEQ.NEXTVAL, 'Producto 1');
INSERT INTO PRODUCTOS (ID, NOMBRE) VALUES (PRODUCTOS_SEQ.NEXTVAL, 'Producto 2');
```

**Solución:** Usar secuencias para IDs

---

## 🔍 Herramientas de Validación

### Backend

**Compilación:**
```bash
mvn clean compile
```

**Tests:**
```bash
mvn test
```

**Cobertura:**
```bash
mvn test jacoco:report
# Ver: target/site/jacoco/index.html
```

**Análisis estático:**
```bash
mvn checkstyle:check
mvn pmd:check
```

**Dependencias vulnerables:**
```bash
mvn dependency-check:check
```

---

### Frontend

**Compilación:**
```bash
npm run build
```

**Tests:**
```bash
npm test
```

**Cobertura:**
```bash
npm test -- --coverage
```

**Linting:**
```bash
npm run lint
```

**Dependencias vulnerables:**
```bash
npm audit
```

---

## 📋 Plantilla de Reporte de Validación

```markdown
# Reporte de Validación - [Nombre del Proyecto]

**Fecha:** [DD/MM/YYYY]
**Validador:** [Nombre]
**Versión:** [1.0.0]

## ✅ Resumen Ejecutivo

- **Estado General:** [APROBADO / RECHAZADO / REQUIERE CORRECCIONES]
- **Criterios Obligatorios:** [X/10]
- **Criterios Recomendados:** [X/10]
- **Cobertura de Tests:** [XX%]

## 📊 Resultados por Categoría

### 1. Estructura del Proyecto
- [ ] Estructura de carpetas correcta
- [ ] Archivos obligatorios presentes
- [ ] Nomenclatura consistente

**Observaciones:**
- [Detallar observaciones]

### 2. Backend
- [ ] Compila sin errores
- [ ] Tests pasan (cobertura: XX%)
- [ ] Usa `jakarta.*`
- [ ] Inyección por constructor
- [ ] Validaciones en DTOs
- [ ] Manejo de excepciones
- [ ] JavaDoc completo

**Observaciones:**
- [Detallar observaciones]

### 3. Frontend
- [ ] Compila sin errores
- [ ] Tests pasan
- [ ] Solo componentes MUI
- [ ] Estilos con `sx`
- [ ] Validaciones con Yup
- [ ] Maneja estados de UI
- [ ] Responsive design
- [ ] JSDoc completo

**Observaciones:**
- [Detallar observaciones]

### 4. Base de Datos
- [ ] Scripts SQL en MAYÚSCULAS
- [ ] Secuencias configuradas
- [ ] Índices creados
- [ ] Constraints con nombres
- [ ] Comentarios presentes

**Observaciones:**
- [Detallar observaciones]

### 5. Documentación
- [ ] README.md completo
- [ ] JavaDoc en clases públicas
- [ ] JSDoc en funciones exportadas
- [ ] Variables de entorno documentadas

**Observaciones:**
- [Detallar observaciones]

### 6. Seguridad
- [ ] CORS configurado
- [ ] Validación de entrada
- [ ] Passwords encriptados
- [ ] JWT configurado
- [ ] Sin secretos hardcodeados

**Observaciones:**
- [Detallar observaciones]

### 7. Calidad de Código
- [ ] Sin `System.out.println()`
- [ ] Sin `@Autowired` en campos
- [ ] Sin código comentado
- [ ] Sin TODOs
- [ ] Logging apropiado

**Observaciones:**
- [Detallar observaciones]

## 🐛 Errores Encontrados

### Críticos (Bloquean aprobación)
1. [Descripción del error]
   - **Ubicación:** [Archivo:Línea]
   - **Solución:** [Cómo corregir]

### Menores (No bloquean aprobación)
1. [Descripción del error]
   - **Ubicación:** [Archivo:Línea]
   - **Solución:** [Cómo corregir]

## 💡 Recomendaciones

1. [Recomendación 1]
2. [Recomendación 2]
3. [Recomendación 3]

## 📝 Conclusión

[Resumen de la validación y decisión final]

**Decisión:** [APROBADO / RECHAZADO / REQUIERE CORRECCIONES]

---

**Firma del Validador:** [Nombre]
**Fecha:** [DD/MM/YYYY]
```

---

## 🎓 Recursos Adicionales

### Documentación Oficial

- **Spring Boot:** https://spring.io/projects/spring-boot
- **Spring Data JPA:** https://spring.io/projects/spring-data-jpa
- **Spring Security:** https://spring.io/projects/spring-security
- **React:** https://react.dev/
- **Material-UI:** https://mui.com/
- **React Router:** https://reactrouter.com/
- **React Hook Form:** https://react-hook-form.com/
- **Yup:** https://github.com/jquense/yup
- **Vite:** https://vitejs.dev/
- **Vitest:** https://vitest.dev/
- **Oracle Database:** https://docs.oracle.com/en/database/

### Guías y Tutoriales

- **Spring Boot Best Practices:** https://spring.io/guides
- **React Best Practices:** https://react.dev/learn
- **Material-UI Templates:** https://mui.com/material-ui/getting-started/templates/
- **Testing Library:** https://testing-library.com/docs/react-testing-library/intro/

### Herramientas

- **IntelliJ IDEA:** https://www.jetbrains.com/idea/
- **VS Code:** https://code.visualstudio.com/
- **Postman:** https://www.postman.com/
- **DBeaver:** https://dbeaver.io/
- **Git:** https://git-scm.com/

---

## 📞 Soporte

Si encuentras problemas durante la validación o necesitas aclaraciones:

1. **Consulta la documentación:** Revisa `ESPECIFICACION_COMPLETA.md`, `REGLAS_GENERACION.md`, `ESTRUCTURA_PROYECTO.md`
2. **Revisa ejemplos:** Consulta la carpeta `examples/` si existe
3. **Contacta al equipo UDA:** [email de contacto]

---

## 🔄 Historial de Cambios

### Versión 1.0.0 (30/10/2025)
- Versión inicial del checklist de validación
- Incluye validación completa de Backend, Frontend y Base de Datos
- Añadidos errores comunes y soluciones
- Incluida plantilla de reporte de validación

---

## ✅ Checklist Final de Entrega

Antes de considerar la aplicación como **COMPLETA**, verificar:

### Compilación y Ejecución
- [ ] Backend compila: `mvn clean compile`
- [ ] Frontend compila: `npm run build`
- [ ] Backend ejecuta: `mvn spring-boot:run`
- [ ] Frontend ejecuta: `npm run dev`
- [ ] Backend responde en http://localhost:8080
- [ ] Frontend responde en http://localhost:5173

### Tests
- [ ] Tests backend pasan: `mvn test`
- [ ] Tests frontend pasan: `npm test`
- [ ] Cobertura backend > 70%
- [ ] Cobertura frontend > 60%

### Funcionalidad
- [ ] CRUD completo funciona (Crear, Leer, Actualizar, Eliminar)
- [ ] Validaciones funcionan (Backend y Frontend)
- [ ] Manejo de errores funciona
- [ ] Estados de UI funcionan (loading, error, empty)
- [ ] Navegación funciona
- [ ] Responsive design funciona

### Calidad de Código
- [ ] NO hay errores de compilación
- [ ] NO hay warnings críticos
- [ ] NO hay `System.out.println()`
- [ ] NO hay `@Autowired` en campos
- [ ] NO hay HTML nativo en Frontend
- [ ] NO hay estilos inline
- [ ] NO hay código comentado
- [ ] NO hay TODOs

### Documentación
- [ ] README.md completo
- [ ] JavaDoc en todas las clases públicas
- [ ] JSDoc en todas las funciones exportadas
- [ ] Variables de entorno documentadas
- [ ] Scripts SQL comentados

### Seguridad
- [ ] CORS configurado
- [ ] Validaciones en DTOs
- [ ] Passwords encriptados
- [ ] JWT configurado
- [ ] NO hay secretos hardcodeados

### Base de Datos
- [ ] Scripts SQL en MAYÚSCULAS
- [ ] Secuencias creadas
- [ ] Índices creados
- [ ] Constraints con nombres
- [ ] Datos de ejemplo insertados

### Estructura
- [ ] Estructura de carpetas correcta
- [ ] Nomenclatura consistente
- [ ] Archivos obligatorios presentes
- [ ] `.gitignore` configurado
- [ ] `.env.example` documentado

---

## 🎯 Puntuación Final

### Cálculo de Puntuación

**Criterios Obligatorios (70%):**
- Compilación y Ejecución: 20%
- Funcionalidad CRUD: 20%
- Estructura y Nomenclatura: 15%
- Documentación Básica: 15%

**Criterios Recomendados (30%):**
- Tests y Cobertura: 10%
- Calidad de Código: 10%
- Seguridad: 5%
- Documentación Avanzada: 5%

**Puntuación Mínima para Aprobar:** 80/100

---

## 📌 Notas Finales

1. **Este checklist es exhaustivo:** No todos los puntos son obligatorios, pero cuantos más se cumplan, mejor será la calidad de la aplicación.

2. **Prioriza los criterios obligatorios:** Asegúrate de que la aplicación compila, ejecuta y el CRUD funciona antes de preocuparte por los detalles.

3. **La consistencia es clave:** Es mejor tener una aplicación simple pero consistente que una compleja pero inconsistente.

4. **Documenta todo:** Una buena documentación es tan importante como el código.

5. **Prueba manualmente:** Aunque los tests pasen, siempre prueba la aplicación manualmente para asegurarte de que funciona como se espera.

6. **Revisa el código:** Antes de entregar, revisa el código completo al menos una vez.

7. **Usa las herramientas:** Aprovecha las herramientas de validación automática (linters, tests, análisis estático).

8. **Aprende de los errores:** Si encuentras errores comunes, documéntalos para evitarlos en el futuro.

9. **Mantén la calidad:** La calidad del código es más importante que la cantidad de funcionalidades.

10. **Pide feedback:** Si es posible, pide a alguien más que revise tu código.

---

**¡Éxito en la validación!** 🚀

---

**Versión:** 1.0.0  
**Última actualización:** 30/10/2025  
**Autor:** Universidad del Azuay (UDA)  
**Repositorio:** https://github.com/xaabi6/udaTemplatesIA
```

---

## 📚 Apéndices

### Apéndice A: Comandos Útiles

**Backend (Maven):**
```bash
# Compilar
mvn clean compile

# Ejecutar
mvn spring-boot:run

# Tests
mvn test

# Tests con cobertura
mvn clean test jacoco:report

# Generar WAR
mvn clean package

# Limpiar
mvn clean

# Ver dependencias
mvn dependency:tree

# Actualizar dependencias
mvn versions:display-dependency-updates
```

**Frontend (npm):**
```bash
# Instalar dependencias
npm install

# Ejecutar en desarrollo
npm run dev

# Compilar para producción
npm run build

# Preview de producción
npm run preview

# Tests
npm test

# Tests con cobertura
npm test -- --coverage

# Linting
npm run lint

# Fix linting
npm run lint -- --fix

# Auditoría de seguridad
npm audit

# Fix vulnerabilidades
npm audit fix
```

**Git:**
```bash
# Clonar repositorio
git clone [url]

# Ver estado
git status

# Agregar cambios
git add .

# Commit
git commit -m "mensaje"

# Push
git push origin [branch]

# Pull
git pull origin [branch]

# Crear rama
git checkout -b [nombre-rama]

# Cambiar de rama
git checkout [nombre-rama]

# Ver ramas
git branch

# Mergear rama
git merge [nombre-rama]
```
