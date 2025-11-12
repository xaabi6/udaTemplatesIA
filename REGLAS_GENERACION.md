# Reglas de Generación para IA

---

## 📋 Propósito

Este documento define las **reglas estrictas** que cualquier IA debe seguir al generar una aplicación UDA. Estas reglas garantizan consistencia, calidad y adherencia a los estándares definidos.

---

## ⚠️ ELEMENTOS CRÍTICOS OBLIGATORIOS

### 🔴 ATENCIÓN: ESTOS 8 ELEMENTOS SON OBLIGATORIOS

**Si falta alguno de estos elementos, la aplicación NO funcionará correctamente.**

| # | Elemento | Ubicación | ¿Por qué es crítico? |
|---|----------|-----------|---------------------|
| 1 | **JacksonConfig.java** | `config/` | Sin esto, las fechas se serializan como arrays `[2024,1,15]` en lugar de strings ISO |
| 2 | **application-dev.yml** | `resources/` | Sin H2, no se puede desarrollar sin Oracle instalado |
| 3 | **application-prod.yml** | `resources/` | Sin Oracle configurado, no funciona en producción |
| 4 | **SpringBootServletInitializer** | `Application.java` | Sin esto, no se puede desplegar como WAR en Tomcat |
| 5 | **packaging WAR** | `pom.xml` | Sin esto, genera JAR en lugar de WAR |
| 6 | **GlobalExceptionHandler** | `exception/` | Sin esto, los errores no se manejan correctamente |
| 7 | **Validaciones en DTOs** | `dto/` | Sin esto, datos inválidos llegan a la BD |
| 8 | **Validaciones Yup** | `frontend/utils/` | Sin esto, validación solo HTML5 (insuficiente) |

### 📋 CHECKLIST PRE-GENERACIÓN

**Antes de empezar a generar código, confirmar:**

```
[ ] ✅ Voy a generar JacksonConfig.java con JavaTimeModule
[ ] ✅ Voy a generar application-dev.yml con H2
[ ] ✅ Voy a generar application-prod.yml con Oracle completo
[ ] ✅ Application.java va a extender SpringBootServletInitializer
[ ] ✅ pom.xml va a tener <packaging>war</packaging>
[ ] ✅ Voy a generar GlobalExceptionHandler completo
[ ] ✅ Todos los DTOs van a tener validaciones @NotNull, @NotBlank, etc.
[ ] ✅ Voy a generar validationSchemas.js con Yup
```

**Si no estás seguro de alguno, DETENTE y revisa este documento.**

---

### 🔴 DETALLE DE ELEMENTOS CRÍTICOS

#### 1. JacksonConfig.java

**Ubicación:** `src/main/java/com/uda/[proyecto]/config/JacksonConfig.java`

**Problema que resuelve:**
Sin esta configuración, cuando el backend envía un `LocalDateTime`, Jackson lo serializa como:
```json
{
  "createdAt": [2024, 1, 15, 10, 30, 45, 123000000]
}
```

En lugar de:
```json
{
  "createdAt": "2024-01-15T10:30:45.123"
}
```

**Código obligatorio:**

```java
package com.uda.[nombre-proyecto].config;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

@Configuration
public class JacksonConfig {
    
    @Bean
    @Primary
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        return mapper;
    }
}
```

**Validación:**
Crear endpoint de prueba y verificar que las fechas son strings ISO-8601.

---

#### 2. application.yml con H2

**Ubicación:** `src/main/resources/application.yml`

**Este es el archivo BASE de configuración y usa H2 para desarrollo.**

**Problema que resuelve:**
Sin H2, los desarrolladores necesitan tener Oracle instalado localmente, lo cual es:
- Lento de instalar
- Consume muchos recursos
- Dificulta el desarrollo rápido

**Configuración mínima obligatoria:**

```yaml
spring:
  # Perfil activo por defecto: desarrollo con H2
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}
  
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password: 
  
  h2:
    console:
      enabled: true
      path: /h2-console
  
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: create-drop
    show-sql: true
```

**Dependencia necesaria en pom.xml:**
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

---

#### 3. application-prod.yml con Oracle

**Ubicación:** `src/main/resources/application-prod.yml`

**Problema que resuelve:**
Sin esta configuración, la aplicación no puede conectarse a Oracle en producción.

**Configuración mínima obligatoria:**

```yaml
spring:
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:ORCL}
    username: ${DB_USERNAME:uda_user}
    password: ${DB_PASSWORD:uda_password}
    driver-class-name: oracle.jdbc.OracleDriver
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
  
  jpa:
    database-platform: org.hibernate.dialect.Oracle12cDialect
    hibernate:
      ddl-auto: validate
    show-sql: false
```

---

#### 4. Application.java extiende SpringBootServletInitializer

**Ubicación:** `src/main/java/com/uda/[proyecto]/Application.java`

**Problema que resuelve:**
Sin extender `SpringBootServletInitializer`, la aplicación no se puede desplegar como WAR en Tomcat.

**Código obligatorio:**

```java
@SpringBootApplication
public class Application extends SpringBootServletInitializer {
    
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
    
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

#### 5. pom.xml con packaging WAR

**Ubicación:** `pom.xml`

**Problema que resuelve:**
Sin `<packaging>war</packaging>`, Maven genera un JAR en lugar de WAR, y no se puede desplegar en Tomcat.

**Elementos obligatorios:**

```xml
<packaging>war</packaging>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>

<build>
    <finalName>${project.artifactId}</finalName>
</build>
```

---

#### 6. GlobalExceptionHandler

**Ubicación:** `src/main/java/com/uda/[proyecto]/exception/GlobalExceptionHandler.java`

**Problema que resuelve:**
Sin este handler, cuando ocurre un error:
- El stack trace completo se envía al cliente (riesgo de seguridad)
- Los mensajes de error no son consistentes
- Difícil debugging en producción

**Código mínimo obligatorio:**

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(ResourceNotFoundException ex) {
        // Manejo de 404
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {
        // Manejo de errores de validación
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        // Manejo de errores genéricos (sin exponer stack trace)
    }
}
```

---

#### 7. Validaciones en DTOs

**Problema que resuelve:**
Sin validaciones, datos inválidos llegan a la base de datos:
- Strings vacíos donde no deberían
- Números negativos donde deben ser positivos
- Emails inválidos
- Etc.

**Ejemplo obligatorio:**

```java
@Data
public class ProductoDTO {
    
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100)
    private String nombre;
    
    @NotNull(message = "El precio es obligatorio")
    @DecimalMin(value = "0.01")
    private BigDecimal precio;
    
    @Min(value = 0)
    private Integer stock;
}
```

**En el Controller:**
```java
@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {
    // @Valid es OBLIGATORIO
}
```

---

#### 8. Validaciones Yup en Frontend

**Ubicación:** `src/utils/validationSchemas.js`

**Problema que resuelve:**
Sin Yup, la validación solo se hace con HTML5, que es:
- Fácil de bypassear
- Mensajes genéricos
- No valida lógica compleja

**Código obligatorio:**

```javascript
import * as yup from 'yup';

export const productoSchema = yup.object({
  nombre: yup
    .string()
    .required('El nombre es obligatorio')
    .min(3, 'Mínimo 3 caracteres')
    .max(100, 'Máximo 100 caracteres'),
  
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
```

**Uso en formulario:**
```javascript
const { control, handleSubmit } = useForm({
  resolver: yupResolver(productoSchema),  // OBLIGATORIO
});
```

---

## 🎯 Principios Fundamentales

### 1. Adherencia Estricta
- ✅ **SIEMPRE** seguir la estructura definida en `ESTRUCTURA_PROYECTO.md`
- ✅ **SIEMPRE** usar las tecnologías especificadas en `ESPECIFICACION_COMPLETA.md`
- ❌ **NUNCA** desviarse de los estándares sin justificación explícita

### 2. Completitud
- ✅ Generar **TODOS** los archivos necesarios
- ✅ Incluir **TODOS** los imports y dependencias
- ✅ Proporcionar código **funcional y compilable**
- ❌ **NO** generar código incompleto o con TODOs

### 3. Calidad
- ✅ Código limpio y bien documentado
- ✅ Seguir principios SOLID
- ✅ Incluir manejo de errores
- ✅ Validar todas las entradas

---

## 📦 Reglas de Stack Tecnológico

### ⚠️ IMPORTANTE: Uso de Jakarta EE (NO javax)

**Spring Boot 3.x usa Jakarta EE 9+, NO Java EE (javax)**

#### ✅ CORRECTO - Usar jakarta.*

```java
// Imports correctos para Spring Boot 3.x
import jakarta.persistence.*;
import jakarta.validation.constraints.*;
import jakarta.servlet.*;
import jakarta.transaction.Transactional;
```

#### ❌ INCORRECTO - NO usar javax.*

```java
// ❌ NUNCA usar estos imports en Spring Boot 3.x
import javax.persistence.*;        // ❌ INCORRECTO
import javax.validation.constraints.*;  // ❌ INCORRECTO
import javax.servlet.*;            // ❌ INCORRECTO
import javax.transaction.Transactional; // ❌ INCORRECTO
```

#### 📋 Tabla de Conversión javax → jakarta

| ❌ javax (INCORRECTO) | ✅ jakarta (CORRECTO) |
|----------------------|----------------------|
| `javax.persistence.*` | `jakarta.persistence.*` |
| `javax.validation.*` | `jakarta.validation.*` |
| `javax.servlet.*` | `jakarta.servlet.*` |
| `javax.transaction.*` | `jakarta.transaction.*` |
| `javax.annotation.*` | `jakarta.annotation.*` |
| `javax.inject.*` | `jakarta.inject.*` |
| `javax.ws.rs.*` | `jakarta.ws.rs.*` |

#### 🎯 Regla de Oro

**SIEMPRE usar `jakarta.*` en Spring Boot 3.x**

Si ves `javax.*` en algún ejemplo o código generado, **cámbialo inmediatamente a `jakarta.*`**

#### Ejemplo Completo Correcto

```java
package com.uda.proyecto.entity;

// ✅ CORRECTO - Jakarta EE
import jakarta.persistence.*;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;
import lombok.*;

import java.time.LocalDateTime;

@Entity
@Table(name = "PRODUCTOS")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Producto {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "producto_seq")
    @SequenceGenerator(name = "producto_seq", sequenceName = "PRODUCTO_SEQ", allocationSize = 1)
    @Column(name = "ID")
    private Long id;
    
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100)
    @Column(name = "NOMBRE", nullable = false, length = 100)
    private String nombre;
    
    @Column(name = "CREATED_AT")
    private LocalDateTime createdAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
}
```

---

### Backend

#### Versiones Exactas (OBLIGATORIO)

```xml
<!-- pom.xml -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.0</version>
</parent>

<properties>
    <java.version>21</java.version>
    <mapstruct.version>1.6.3</mapstruct.version>
    <lombok.version>1.18.34</lombok.version>
</properties>
```

#### Dependencias Obligatorias

✅ **SIEMPRE incluir**:
- `spring-boot-starter-web`
- `spring-boot-starter-data-jpa`
- `spring-boot-starter-security`
- `spring-boot-starter-validation`
- `spring-boot-starter-tomcat` (scope: provided)
- `ojdbc11` (Oracle JDBC Driver)
- `lombok`
- `mapstruct`
- `spring-boot-starter-test`
- `springdoc-openapi-starter-webmvc-ui` (Swagger)

❌ **NUNCA usar**:
- Versiones diferentes de Spring Boot
- Bases de datos diferentes a Oracle
- Otros ORMs que no sean Hibernate/JPA
- Frameworks de mapeo diferentes a MapStruct

### Frontend

#### Versiones Exactas (OBLIGATORIO)

```json
{
  "dependencies": {
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    "react-router-dom": "^6.22.0",
    "@mui/material": "^5.15.0",
    "@mui/icons-material": "^5.15.0",
    "@emotion/react": "^11.11.0",
    "@emotion/styled": "^11.11.0",
    "axios": "^1.6.0",
    "react-hook-form": "^7.50.0",
    "yup": "^1.3.0"
  }
}
```

#### Dependencias Obligatorias

✅ **SIEMPRE incluir**:
- React 18+
- Material-UI (MUI) 5+
- React Router 6+
- Axios
- React Hook Form
- Yup (validación)
- Vite (build tool)
- Vitest (testing)

❌ **NUNCA usar**:
- Otros frameworks UI (Bootstrap, Ant Design, etc.)
- Otros routers (React Router v5 o anteriores)
- Fetch API en lugar de Axios
- Create React App (usar Vite)

**📖 Ver estándares de UI**: [ESTANDARES_UI.md](ESTANDARES_UI.md)

---

## 🎨 Reglas de UI y Design System

### Material-UI es Obligatorio

✅ **SIEMPRE**:
1. Usar componentes de MUI exclusivamente
2. Aplicar el tema UDA desde `frontend/src/theme/`
3. Usar `ThemeProvider` envolviendo toda la app
4. Usar `CssBaseline` de MUI
5. Usar `sx` prop para estilos personalizados
6. Usar `theme.spacing()` para espaciado
7. Usar `theme.palette.*` para colores
8. Usar `Typography` para todo el texto
9. Usar breakpoints para responsive design
10. Usar iconos de `@mui/icons-material`

❌ **NUNCA**:
1. Crear componentes UI desde cero
2. Usar estilos inline: `style={{ color: 'red' }}`
3. Hardcodear colores: `color: '#1976d2'`
4. Hardcodear espaciado: `padding: '16px'`
5. Mezclar HTML nativo con MUI
6. Usar `!important` en estilos
7. Crear wrappers innecesarios sobre MUI
8. Usar otros frameworks UI

### Estructura del Tema

```javascript
// ✅ CORRECTO - Estructura obligatoria
frontend/src/theme/
├── index.js           # Tema principal
├── palette.js         # Colores UDA
├── typography.js      # Tipografía
├── components.js      # Overrides MUI
└── shadows.js         # Sombras
```

**📖 Documentación completa**: [ESTANDARES_UI.md](ESTANDARES_UI.md)

### Ejemplos de Uso Correcto

```jsx
// ✅ CORRECTO
import { Box, Typography, Button } from '@mui/material';

<Box sx={{ p: 3, bgcolor: 'primary.main' }}>
  <Typography variant="h4" gutterBottom>
    Título
  </Typography>
  <Button variant="contained">
    Acción
  </Button>
</Box>

// ❌ INCORRECTO
<div style={{ padding: '24px', backgroundColor: '#1976d2' }}>
  <h1 style={{ fontSize: '24px' }}>Título</h1>
  <button>Acción</button>
</div>
```

**Ver más ejemplos**: [ESTANDARES_UI.md](ESTANDARES_UI.md)

---

## 🏗️ Reglas de Arquitectura

### Backend

#### 1. Estructura de Capas (OBLIGATORIO)

```
Controller → Service → Repository → Database
     ↓          ↓
    DTO      Entity
```

✅ **SIEMPRE**:
- Controller llama a Service
- Service llama a Repository
- Repository accede a la base de datos
- Usar DTOs en Controllers
- Usar Entities en Repositories
- Mapear entre Entity y DTO con MapStruct

❌ **NUNCA**:
- Controller accede directamente a Repository
- Exponer Entities en la API REST
- Lógica de negocio en Controllers
- Acceso a datos en Services (usar Repository)

#### 2. Nomenclatura de Clases (OBLIGATORIO)

```java
// ✅ CORRECTO
ProductoController.java
ProductoService.java
ProductoServiceImpl.java
ProductoRepository.java
Producto.java (Entity)
ProductoDTO.java
ProductoMapper.java

// ❌ INCORRECTO
ProductController.java
ProductSvc.java
ProductRepoImpl.java
ProductEntity.java
ProductDataTransferObject.java
```

#### 3. Estructura de Métodos (OBLIGATORIO)

**Controller**:
```java
// ✅ CORRECTO
@GetMapping
public ResponseEntity<List<ProductoDTO>> findAll() { }

@GetMapping("/{id}")
public ResponseEntity<ProductoDTO> findById(@PathVariable Long id) { }

@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) { }

@PutMapping("/{id}")
public ResponseEntity<ProductoDTO> update(@PathVariable Long id, @Valid @RequestBody ProductoDTO dto) { }

@DeleteMapping("/{id}")
public ResponseEntity<Void> delete(@PathVariable Long id) { }
```

**Service**:
```java
// ✅ CORRECTO
List<ProductoDTO> findAll();
ProductoDTO findById(Long id);
ProductoDTO create(ProductoDTO dto);
ProductoDTO update(Long id, ProductoDTO dto);
void delete(Long id);
```

**Repository**:
```java
// ✅ CORRECTO - Extender JpaRepository
public interface ProductoRepository extends JpaRepository<Producto, Long> {
    List<Producto> findByActiveTrue();
    Optional<Producto> findByIdAndActiveTrue(Long id);
}

// ❌ INCORRECTO - No implementar manualmente
public class ProductoRepositoryImpl { }
```

#### 4. Anotaciones Obligatorias

**Entity**:
```java
@Entity
@Table(name = "PRODUCTOS")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Producto extends BaseEntity { }
```

**DTO**:
```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ProductoDTO {
    @NotBlank(message = "El nombre es obligatorio")
    private String nombre;
}
```

**Service**:
```java
@Service
@Transactional
@RequiredArgsConstructor
@Slf4j
public class ProductoServiceImpl implements ProductoService { }
```

**Controller**:
```java
@RestController
@RequestMapping("/api/v1/productos")
@RequiredArgsConstructor
@Slf4j
@CrossOrigin(origins = "*")
@Tag(name = "Productos", description = "API para gestión de productos")
public class ProductoController { }
```

### Frontend

#### 1. Estructura de Componentes (OBLIGATORIO)

```jsx
// ✅ CORRECTO - Orden de elementos
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { Box, Button } from '@mui/material';
import PropTypes from 'prop-types';

function ProductoList() {
  // 1. Hooks de estado
  const [productos, setProductos] = useState([]);
  
  // 2. Hooks de navegación/contexto
  const navigate = useNavigate();
  
  // 3. useEffect
  useEffect(() => {
    loadProductos();
  }, []);
  
  // 4. Funciones de manejo
  const handleCreate = () => { };
  
  // 5. Funciones auxiliares
  const loadProductos = async () => { };
  
  // 6. Renderizado condicional
  if (loading) return <Loading />;
  
  // 7. Renderizado principal
  return <Box>...</Box>;
}

// 8. PropTypes
ProductoList.propTypes = {
  // ...
};

export default ProductoList;
```

#### 2. Nomenclatura de Archivos (OBLIGATORIO)

```
✅ CORRECTO:
ProductoList.jsx          (Componente)
ProductoForm.jsx          (Componente)
productoService.js        (Servicio)
useProductos.js           (Hook)
constants.js              (Utilidades)

❌ INCORRECTO:
producto-list.jsx
ProductoService.js        (debe ser camelCase)
UseProductos.js           (hooks en camelCase)
Constants.js              (utils en camelCase)
```

#### 3. Imports Organizados (OBLIGATORIO)

```jsx
// ✅ CORRECTO - Orden de imports
// 1. React y hooks
import { useState, useEffect } from 'react';

// 2. Librerías de terceros
import { useNavigate } from 'react-router-dom';
import { Box, Button } from '@mui/material';
import PropTypes from 'prop-types';

// 3. Componentes locales
import Header from '../components/Header';

// 4. Hooks personalizados
import { useAuth } from '../hooks/useAuth';

// 5. Servicios
import productoService from '../services/productoService';

// 6. Utilidades
import { formatDate } from '../utils/formatters';

// 7. Estilos (si aplica)
import './ProductoList.css';
```

---

## 🔒 Reglas de Seguridad

### Backend

#### 1. Autenticación y Autorización (OBLIGATORIO)

✅ **SIEMPRE**:
- Implementar JWT para autenticación
- Usar `@PreAuthorize` para autorización
- Encriptar contraseñas con BCrypt
- Validar tokens en cada request
- Implementar refresh tokens

```java
// ✅ CORRECTO
@PostMapping
@PreAuthorize("hasAnyRole('USER', 'ADMIN')")
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) { }

// ❌ INCORRECTO - Sin autorización
@PostMapping
public ResponseEntity<ProductoDTO> create(@RequestBody ProductoDTO dto) { }
```

#### 2. Validación de Datos (OBLIGATORIO)

✅ **SIEMPRE**:
- Usar `@Valid` en Controllers
- Validar en DTOs con Bean Validation
- Validar en Services (lógica de negocio)
- Sanitizar entradas del usuario

```java
// ✅ CORRECTO
@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {
    // Validación adicional en service
    return ResponseEntity.ok(service.create(dto));
}

// DTO con validaciones
@Data
public class ProductoDTO {
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100)
    private String nombre;
    
    @NotNull
    @DecimalMin("0.01")
    private BigDecimal precio;
}
```

#### 3. Manejo de Errores (OBLIGATORIO)

✅ **SIEMPRE**:
- Usar `GlobalExceptionHandler`
- No exponer stack traces en producción
- Loguear errores con contexto
- Retornar mensajes amigables

```java
// ✅ CORRECTO
@ExceptionHandler(ResourceNotFoundException.class)
public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
    log.error("Recurso no encontrado: {}", ex.getMessage());
    ErrorResponse error = ErrorResponse.builder()
        .message(ex.getMessage())
        .status(HttpStatus.NOT_FOUND.value())
        .build();
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
}

// ❌ INCORRECTO - Exponer stack trace
@ExceptionHandler(Exception.class)
public ResponseEntity<String> handleError(Exception ex) {
    return ResponseEntity.ok(ex.getStackTrace().toString());
}
```

### Frontend

#### 1. Protección de Rutas (OBLIGATORIO)

✅ **SIEMPRE**:
- Usar `ProtectedRoute` para rutas privadas
- Verificar autenticación antes de renderizar
- Redirigir a login si no está autenticado

```jsx
// ✅ CORRECTO
<Route
  path="/productos"
  element={
    <ProtectedRoute>
      <ProductoList />
    </ProtectedRoute>
  }
/>

// ❌ INCORRECTO - Sin protección
<Route path="/productos" element={<ProductoList />} />
```

#### 2. Sanitización de Datos (OBLIGATORIO)

✅ **SIEMPRE**:
- Sanitizar HTML con DOMPurify
- Validar URLs antes de usar
- Escapar caracteres especiales

```jsx
// ✅ CORRECTO
import { sanitizeHtml } from '../utils/sanitizer';

function ProductoCard({ producto }) {
  return (
    <div dangerouslySetInnerHTML={{ __html: sanitizeHtml(producto.descripcion) }} />
  );
}

// ❌ INCORRECTO - XSS vulnerable
function ProductoCard({ producto }) {
  return <div dangerouslySetInnerHTML={{ __html: producto.descripcion }} />;
}
```

---

## 📊 Reglas de Base de Datos

### 1. Nomenclatura (OBLIGATORIO)

✅ **SIEMPRE usar MAYÚSCULAS**:
```sql
-- ✅ CORRECTO
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) NOT NULL,
    NOMBRE VARCHAR2(100) NOT NULL,
    PRECIO NUMBER(10,2) NOT NULL,
    CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID)
);

CREATE SEQUENCE PRODUCTOS_SEQ;

-- ❌ INCORRECTO
create table productos (
    id number(19) not null,
    nombre varchar2(100) not null
);
```

### 2. Constraints (OBLIGATORIO)

✅ **SIEMPRE incluir**:
- Primary Key: `PK_[TABLA]`
- Foreign Key: `FK_[TABLA]_[REFERENCIA]`
- Unique: `UK_[TABLA]_[COLUMNA]`
- Check: `CK_[TABLA]_[COLUMNA]`

```sql
-- ✅ CORRECTO
CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID),
CONSTRAINT FK_PRODUCTOS_CATEGORIA FOREIGN KEY (CATEGORIA_ID) REFERENCES CATEGORIAS(ID),
CONSTRAINT UK_PRODUCTOS_SKU UNIQUE (SKU),
CONSTRAINT CK_PRODUCTOS_PRECIO CHECK (PRECIO > 0)

-- ❌ INCORRECTO - Sin nombres
PRIMARY KEY (ID),
FOREIGN KEY (CATEGORIA_ID) REFERENCES CATEGORIAS(ID)
```

### 3. Índices (OBLIGATORIO)

✅ **SIEMPRE crear índices** en:
- Columnas de búsqueda frecuente
- Foreign Keys
- Columnas en WHERE, ORDER BY, JOIN

```sql
-- ✅ CORRECTO
CREATE INDEX IDX_PRODUCTOS_NOMBRE ON PRODUCTOS(NOMBRE);
CREATE INDEX IDX_PRODUCTOS_CATEGORIA ON PRODUCTOS(CATEGORIA_ID);
CREATE INDEX IDX_PRODUCTOS_ACTIVO ON PRODUCTOS(ACTIVO);
```

### 4. Secuencias (OBLIGATORIO)

✅ **SIEMPRE**:
- Una secuencia por tabla
- Formato: `[TABLA]_SEQ`
- `START WITH 1`, `INCREMENT BY 1`

```sql
-- ✅ CORRECTO
CREATE SEQUENCE PRODUCTOS_SEQ
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;
```

---

## 📝 Reglas de Documentación

### 1. JavaDoc (OBLIGATORIO)

✅ **SIEMPRE documentar**:
- Clases públicas
- Métodos públicos
- Parámetros complejos

```java
/**
 * Servicio para gestión de productos.
 * Proporciona operaciones CRUD y lógica de negocio.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Service
public class ProductoServiceImpl implements ProductoService {
    
    /**
     * Busca un producto por su ID.
     * 
     * @param id ID del producto a buscar
     * @return DTO del producto encontrado
     * @throws ResourceNotFoundException si el producto no existe
     */
    @Override
    public ProductoDTO findById(Long id) {
        // ...
    }
}
```

### 2. JSDoc (OBLIGATORIO)

✅ **SIEMPRE documentar**:
- Funciones exportadas
- Hooks personalizados
- Servicios

```javascript
/**
 * Hook personalizado para gestión de productos.
 * 
 * @param {Object} options - Opciones de configuración
 * @param {boolean} options.autoLoad - Cargar automáticamente al montar
 * @returns {Object} Estado y funciones para productos
 * 
 * @example
 * const { productos, loading, createProducto } = useProductos();
 */
export const useProductos = (options = {}) => {
  // ...
};
```

### 3. README (OBLIGATORIO)

✅ **SIEMPRE incluir**:
- Descripción del proyecto
- Requisitos previos
- Instrucciones de instalación
- Instrucciones de ejecución
- Ejemplos de uso
- Información de contacto

---

## 🧪 Reglas de Testing

### Backend

#### 1. Cobertura Mínima (OBLIGATORIO)

✅ **SIEMPRE**:
- Tests unitarios para Services: **> 80%**
- Tests de integración para Controllers: **> 70%**
- Tests de Repository: **> 60%**

#### 2. Estructura de Tests (OBLIGATORIO)

```java
// ✅ CORRECTO - Patrón AAA (Arrange, Act, Assert)
@Test
void testFindById_Success() {
    // Arrange
    when(repository.findById(1L)).thenReturn(Optional.of(producto));
    when(mapper.toDTO(producto)).thenReturn(productoDTO);
    
    // Act
    ProductoDTO result = service.findById(1L);
    
    // Assert
    assertNotNull(result);
    assertEquals(productoDTO.getId(), result.getId());
    verify(repository, times(1)).findById(1L);
}
```

#### 3. Nomenclatura de Tests (OBLIGATORIO)

```java
// ✅ CORRECTO
testFindById_Success()
testFindById_NotFound()
testCreate_Success()
testCreate_ValidationError()
testUpdate_Success()
testDelete_Success()

// ❌ INCORRECTO
test1()
testProducto()
findByIdTest()
```

### Frontend

#### 1. Tests de Componentes (OBLIGATORIO)

✅ **SIEMPRE probar**:
- Renderizado inicial
- Interacciones del usuario
- Estados de loading y error
- Llamadas a servicios

```javascript
// ✅ CORRECTO
describe('ProductoList', () => {
  it('debe renderizar la lista de productos', async () => {
    // Arrange
    const mockProductos = [{ id: 1, nombre: 'Test' }];
    productoService.getAll.mockResolvedValue(mockProductos);
    
    // Act
    render(<ProductoList />);
    
    // Assert
    await waitFor(() => {
      expect(screen.getByText('Test')).toBeInTheDocument();
    });
  });
});
```

---

## 🚫 Prohibiciones Absolutas

### Backend

❌ **NUNCA**:
1. **Omitir JacksonConfig.java** - CRÍTICO para serialización de fechas
2. **Omitir application-dev.yml con H2** - CRÍTICO para desarrollo
3. **Omitir application-prod.yml con Oracle** - CRÍTICO para producción
4. **No extender SpringBootServletInitializer** - CRÍTICO para Tomcat
5. **Usar packaging JAR en lugar de WAR** - CRÍTICO para Tomcat
6. Usar `System.out.println()` (usar logger)
7. Hardcodear credenciales en el código
8. Exponer Entities en Controllers
9. Usar `@Autowired` en campos (usar constructor injection)
10. Ignorar excepciones con `catch` vacío
11. Retornar `null` (usar `Optional`)
12. Usar SQL nativo sin justificación
13. Deshabilitar CSRF sin razón válida
14. Loguear información sensible (passwords, tokens)
15. Usar `SELECT *` en queries

### Frontend

❌ **NUNCA**:
1. Usar `var` (usar `const` o `let`)
2. Mutar estado directamente
3. Usar `dangerouslySetInnerHTML` sin sanitizar
4. Hardcodear URLs de API
5. Ignorar errores en `catch`
6. Usar `any` en TypeScript
7. Crear componentes de más de 300 líneas
8. Usar `console.log` en producción
9. Almacenar tokens en localStorage sin encriptar
10. Hacer requests sin manejo de errores

### Base de Datos

❌ **NUNCA**:
1. Crear tablas sin Primary Key
2. Usar nombres en minúsculas
3. Omitir Foreign Keys
4. Crear columnas sin tipo de dato explícito
5. Usar `VARCHAR2` sin longitud
6. Omitir índices en Foreign Keys
7. Crear tablas sin comentarios
8. Usar `DELETE` sin `WHERE`
9. Hardcodear IDs en scripts
10. Omitir secuencias para IDs

---

## 📚 Ejemplos Clave

### Backend - Patrones Correctos

```java
// ✅ Inyección por constructor
@Service
@RequiredArgsConstructor
public class ProductoServiceImpl {
    private final ProductoRepository repository;
    private final ProductoMapper mapper;
}

// ✅ Manejo de Optional
public ProductoDTO findById(Long id) {
    return repository.findById(id)
        .map(mapper::toDTO)
        .orElseThrow(() -> new ResourceNotFoundException("No encontrado"));
}

// ✅ Validaciones en DTOs
@Data
public class ProductoDTO {
    @NotBlank(message = "Nombre obligatorio")
    @Size(min = 3, max = 100)
    private String nombre;
}
```

### Frontend - Patrones Correctos

```jsx
// ✅ Hooks ordenados
function ProductoList() {
  const [productos, setProductos] = useState([]);
  const navigate = useNavigate();
  
  useEffect(() => {
    loadProductos();
  }, []);
  
  return <div>...</div>;
}

// ✅ Validación con Yup
const schema = yup.object({
  nombre: yup.string().required('Obligatorio'),
});

const { control } = useForm({
  resolver: yupResolver(schema),
});
```

### Base de Datos - Patrones Correctos

```sql
-- ✅ Nomenclatura Oracle
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) PRIMARY KEY,
    NOMBRE VARCHAR2(100) NOT NULL
);

CREATE SEQUENCE PRODUCTOS_SEQ;

-- ✅ Constraints con nombres
ALTER TABLE PRODUCTOS
ADD CONSTRAINT FK_PRODUCTOS_CATEGORIA
FOREIGN KEY (CATEGORIA_ID) REFERENCES CATEGORIAS(ID);
```

---

## 🔄 Orden Detallado de Generación de Archivos

La IA debe generar los archivos **exactamente en este orden**:

### Fase 1: Configuración Base del Backend (5 archivos)

1. `backend/pom.xml` ⚠️ **VERIFICAR:** packaging=war, tomcat=provided, dependencia H2
2. `backend/src/main/resources/application.yml` ⚠️ **CRÍTICO:** con H2 completo
3. `backend/src/main/resources/application-prod.yml` ⚠️ **CRÍTICO:** con Oracle completo
4. `backend/src/main/java/com/uda/[proyecto]/Application.java` ⚠️ **VERIFICAR:** extends SpringBootServletInitializer
5. `backend/.gitignore`

### Fase 2: Configuración de Seguridad y Web (2 archivos)

6. `backend/src/main/java/com/uda/[proyecto]/config/JacksonConfig.java` ⚠️ **CRÍTICO:** SIEMPRE GENERAR PRIMERO
7. `backend/src/main/java/com/uda/[proyecto]/config/SecurityConfig.java`
8. `backend/src/main/java/com/uda/[proyecto]/config/WebConfig.java`

### Fase 3: Excepciones (3 archivos)

9. `backend/src/main/java/com/uda/[proyecto]/exception/ResourceNotFoundException.java`
10. `backend/src/main/java/com/uda/[proyecto]/exception/ErrorResponse.java`
11. `backend/src/main/java/com/uda/[proyecto]/exception/GlobalExceptionHandler.java` ⚠️ **CRÍTICO:** este operador y el resto de excepciones personalizadas

### Fase 4: Entidades (por cada entidad, 6 archivos)

12. `backend/src/main/java/com/uda/[proyecto]/entity/Producto.java`
13. `backend/src/main/java/com/uda/[proyecto]/dto/ProductoDTO.java` ⚠️ **VERIFICAR:** Todas tienen validaciones
14. `backend/src/main/java/com/uda/[proyecto]/mapper/ProductoMapper.java`
15. `backend/src/main/java/com/uda/[proyecto]/repository/ProductoRepository.java`
16. `backend/src/main/java/com/uda/[proyecto]/service/ProductoService.java`
17. `backend/src/main/java/com/uda/[proyecto]/service/impl/ProductoServiceImpl.java`

### Fase 5: Controladores (1 archivo por entidad)

18. `backend/src/main/java/com/uda/[proyecto]/controller/ProductoController.java` ⚠️ **VERIFICAR:** Usan @Valid en DTOs

### Fase 6: Base de Datos (1 archivo)

19. `database/schema.sql` (con tablas, secuencias, índices y datos de ejemplo)

### Fase 7: Configuración Base del Frontend (5 archivos)

20. `frontend/package.json`
21. `frontend/vite.config.js`
22. `frontend/index.html`
23. `frontend/.gitignore`
24. `frontend/.env.example`

### Fase 8: Configuración de Estilos y API (2 archivos)

25. `frontend/src/styles/theme.js`
26. `frontend/src/services/api.js`

### Fase 9: Servicios del Frontend (1 archivo por entidad)

27. `frontend/src/services/productoService.js`

### Fase 10: Componentes Principales (2 archivos)

28. `frontend/src/main.jsx`
29. `frontend/src/App.jsx`

### Fase 11: Páginas (3 archivos por entidad + 1 home)

30. `frontend/src/pages/Home.jsx`
31. `frontend/src/pages/ProductoList.jsx`
32. `frontend/src/pages/ProductoForm.jsx`

### Fase 12: Documentación (1 archivo)

33. `README.md`

---

## ✅ Checklist de Validación Post-Generación

Después de generar todos los archivos, la IA debe verificar varios apartados, especialmente los críticos:

### ⚠️ VERIFICACIÓN CRÍTICA (OBLIGATORIA)

**DETENER Y VERIFICAR ESTOS 8 ELEMENTOS ANTES DE CONTINUAR:**

#### Backend Crítico (5 elementos)
```
[ ] ✅ JacksonConfig.java existe en config/ y está completo
      - Tiene @Configuration
      - Tiene @Bean @Primary ObjectMapper
      - Registra JavaTimeModule
      - Desactiva WRITE_DATES_AS_TIMESTAMPS

[ ] ✅ application-dev.yml existe con H2 configurado
      - URL: jdbc:h2:mem:testdb
      - H2 Console habilitado
      - ddl-auto: create-drop
      - show-sql: true
      - Dependencia H2 en pom.xml

[ ] ✅ application-prod.yml existe con Oracle configurado
      - URL Oracle con variables de entorno
      - HikariCP configurado
      - ddl-auto: validate
      - show-sql: false

[ ] ✅ Application.java extiende SpringBootServletInitializer
      - extends SpringBootServletInitializer
      - Método configure() sobrescrito
      - Método main() presente

[ ] ✅ pom.xml tiene packaging WAR
      - <packaging>war</packaging>
      - Tomcat con scope provided
      - <finalName> sin versión
```

#### Manejo de Errores (1 elemento)
```
[ ] ✅ GlobalExceptionHandler existe y está completo
      - @RestControllerAdvice
      - Maneja ResourceNotFoundException (404)
      - Maneja MethodArgumentNotValidException (400)
      - Maneja Exception genérica (500)
      - NO expone stack traces
```

#### Validaciones (2 elementos)
```
[ ] ✅ Todos los DTOs tienen validaciones Bean Validation
      - @NotNull, @NotBlank en campos obligatorios
      - @Size, @Min, @Max donde corresponda
      - Mensajes descriptivos
      - Controllers usan @Valid

[ ] ✅ validationSchemas.js existe con Yup (si hay formularios)
      - Importa yup
      - Al menos un schema por formulario
      - Mensajes en español
      - Formularios usan yupResolver
```

**RESULTADO DE VERIFICACIÓN CRÍTICA:**

- ✅ **8/8 elementos:** EXCELENTE - Continuar con checklist completo
- ⚠️ **6-7/8 elementos:** ACEPTABLE - Corregir faltantes antes de continuar
- ❌ **< 6/8 elementos:** INSUFICIENTE - DETENER y corregir inmediatamente

**SI LA PUNTUACIÓN ES < 6/8:**
1. ❌ DETENER la generación
2. ❌ NO entregar el código
3. ❌ Corregir los elementos faltantes
4. ❌ Volver a verificar desde el inicio

Después, sería el turno de verificar los siguientes apartados:

### Backend
- [ ] `mvn clean compile` ejecuta sin errores
- [ ] Todas las clases tienen imports correctos
- [ ] Todas las anotaciones están presentes
- [ ] No hay warnings de compilación
- [ ] Los nombres de paquetes son consistentes

### Frontend
- [ ] `npm install` ejecuta sin errores
- [ ] Todos los imports son correctos
- [ ] No hay errores de ESLint
- [ ] `npm run build` genera el bundle correctamente

### Base de Datos
- [ ] El script SQL es ejecutable sin errores
- [ ] Todas las tablas tienen PRIMARY KEY
- [ ] Todas las secuencias existen
- [ ] Los nombres están en MAYÚSCULAS

### Integración
- [ ] Los nombres de endpoints coinciden entre frontend y backend
- [ ] Los nombres de campos en DTOs coinciden con los del frontend
- [ ] Las URLs de API son consistentes

---

## ⚠️ Errores Comunes y Cómo Evitarlos

### Error #1: Fechas como Arrays

**Síntoma:**
```json
{
  "createdAt": [2024, 1, 15, 10, 30, 45, 123000000]
}
```

**Causa:** Falta JacksonConfig.java

**Solución:** Generar JacksonConfig.java con JavaTimeModule

---

### Error #2: No compila en desarrollo

**Síntoma:**
```
Error: Could not find or load main class oracle.jdbc.OracleDriver
```

**Causa:** Falta application.yml con H2

**Solución:** Generar application.yml con H2 configurado

---

### Error #3: No se puede desplegar en Tomcat

**Síntoma:**
```
Error: No SpringBootServletInitializer found
```

**Causa:** Application.java no extiende SpringBootServletInitializer

**Solución:** Hacer que Application.java extienda SpringBootServletInitializer

---

### Error #4: Genera JAR en lugar de WAR

**Síntoma:**
```bash
ls target/
# nombre-proyecto.jar  ❌
```

**Causa:** pom.xml no tiene `<packaging>war</packaging>`

**Solución:** Agregar `<packaging>war</packaging>` en pom.xml

---

### Error #5: Stack traces expuestos al cliente

**Síntoma:**
```json
{
  "error": "java.lang.NullPointerException at com.uda..."
}
```

**Causa:** Falta GlobalExceptionHandler

**Solución:** Generar GlobalExceptionHandler completo

---

### Error #6: Datos inválidos en base de datos

**Síntoma:**
```
ERROR: ORA-01400: cannot insert NULL into ("PRODUCTOS"."NOMBRE")
```

**Causa:** DTOs sin validaciones Bean Validation

**Solución:** Agregar `@NotNull`, `@NotBlank`, etc. en todos los DTOs

---

### Error #7: Validación solo HTML5

**Síntoma:**
Mensajes genéricos del navegador: "Please fill out this field"

**Causa:** Falta validationSchemas.js con Yup

**Solución:** Generar validationSchemas.js con Yup para todos los formularios

---

### Error #8: H2 Console no accesible

**Síntoma:**
```
404 Not Found: /h2-console
```

**Causa:** H2 Console no habilitado en application.yml

**Solución:** Agregar configuración de H2 Console en application.yml

---

## 📚 Referencias

- **ESTRUCTURA_PROYECTO.md**: Estructura exacta de carpetas y archivos
- **ESPECIFICACION_COMPLETA.md**: Detalles técnicos completos
- **CHECKLIST_CALIDAD.md**: Lista de verificación de calidad
- **docs/**: Documentación detallada por área

---

## ⚠️ Nota Importante

### 🔴 Elementos Críticos

**Los 8 elementos críticos listados al inicio de este documento son OBLIGATORIOS.**

**Si falta alguno:**
- ❌ La aplicación NO funcionará correctamente
- ❌ NO cumple con las especificaciones UDA
- ❌ NO es apta para producción
- ❌ Requiere corrección INMEDIATA

**Prioridad de verificación:**
1. **PRIMERO:** Verificar los 8 elementos críticos
2. **SEGUNDO:** Verificar el resto del checklist

Estas reglas son **OBLIGATORIAS** y **NO NEGOCIABLES**. La IA debe seguirlas al pie de la letra para garantizar:

1. **Consistencia**: Todas las aplicaciones generadas son idénticas en estructura
2. **Calidad**: El código cumple con estándares profesionales
3. **Mantenibilidad**: El código es fácil de entender y modificar
4. **Seguridad**: Se implementan las mejores prácticas de seguridad
5. **Funcionalidad**: La aplicación es completamente funcional

**Cualquier desviación de estas reglas debe ser justificada explícitamente y documentada.**
