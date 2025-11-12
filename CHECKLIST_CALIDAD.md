# Checklist de Calidad - Aplicación UDA

---

## 📋 Propósito

Este documento proporciona una lista de verificación exhaustiva que la IA debe completar antes de entregar una aplicación UDA generada. Cada ítem debe ser verificado y marcado como completado.

**💡 Nota:** Para una guía paso a paso con ejemplos y sistema de puntuación, consulta [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)

---

## ⚠️ VERIFICACIÓN DE ELEMENTOS CRÍTICOS

### 🔴 PASO 0: ELEMENTOS CRÍTICOS (OBLIGATORIO - 55 PUNTOS)

**ANTES de revisar cualquier otra cosa, verificar estos 8 elementos:**

Esta verificación es **OBLIGATORIA** y debe completarse **ANTES** de continuar con el resto del checklist.

**Si falta alguno de estos elementos, la aplicación NO funcionará correctamente.**

---

#### 🔴 PASO 1. JacksonConfig.java (10 puntos)

**Ubicación:** `src/main/java/com/uda/[proyecto]/config/JacksonConfig.java`

**Verificar:**
- [ ] El archivo existe en la ubicación correcta
- [ ] Tiene anotación `@Configuration`
- [ ] Tiene método `objectMapper()` con anotaciones `@Bean` y `@Primary`
- [ ] Registra JavaTimeModule: `mapper.registerModule(new JavaTimeModule())`
- [ ] Desactiva timestamps: `mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)`
- [ ] Tiene JavaDoc explicativo
- [ ] Importa correctamente:
  - `com.fasterxml.jackson.databind.ObjectMapper`
  - `com.fasterxml.jackson.databind.SerializationFeature`
  - `com.fasterxml.jackson.datatype.jsr310.JavaTimeModule`

**Código mínimo esperado:**
```java
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

**Prueba de validación:**
```java
// Crear endpoint de prueba
@GetMapping("/test-date")
public Map<String, Object> testDate() {
    Map<String, Object> response = new HashMap<>();
    response.put("now", LocalDateTime.now());
    return response;
}
```

**Respuesta esperada:**
```json
{
  "now": "2024-01-15T10:30:45.123"  // ✅ String ISO-8601
}
```

**Respuesta INCORRECTA (sin JacksonConfig):**
```json
{
  "now": [2024, 1, 15, 10, 30, 45, 123000000]  // ❌ Array
}
```

**Puntuación:** ___/10

**⚠️ SI ESTE ELEMENTO FALTA O ESTÁ INCOMPLETO:**
- ❌ **DETENER evaluación inmediatamente**
- ❌ **Calificación máxima posible:** 45/100 (INSUFICIENTE)
- ❌ **Acción requerida:** Generar JacksonConfig.java completo antes de continuar

---

#### 🔴 PASO 2. application.yml con H2 (10 puntos)

**Ubicación:** `src/main/resources/application.yml`

**Este es el archivo BASE y usa H2 por defecto para desarrollo.**

**Verificar:**
- [ ] El archivo existe
- [ ] Perfil activo por defecto es 'dev': `profiles.active: ${SPRING_PROFILES_ACTIVE:dev}`
- [ ] Configura H2 en memoria: `url: jdbc:h2:mem:testdb`
- [ ] Driver H2: `driver-class-name: org.h2.Driver`
- [ ] Username: `username: sa`
- [ ] Password vacío: `password:` (sin valor)
- [ ] H2 Console habilitado: `h2.console.enabled: true`
- [ ] Path de consola: `h2.console.path: /h2-console`
- [ ] Dialect H2: `database-platform: org.hibernate.dialect.H2Dialect`
- [ ] DDL auto: `ddl-auto: create-drop`
- [ ] Show SQL activado: `show-sql: true`
- [ ] Format SQL activado: `format_sql: true`

**Configuración mínima esperada:**
```yaml
spring:
  # Perfil activo por defecto
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}

  application:
    name: nombre-proyecto

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
    properties:
      hibernate:
        format_sql: true

server:
  port: ${SERVER_PORT:8080}
  servlet:
    context-path: /${spring.application.name}
```

**Dependencia en pom.xml:**
- [ ] Existe `<dependency>` con `<artifactId>h2</artifactId>`
- [ ] Tiene `<scope>runtime</scope>`

```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Prueba de validación:**
1. Ejecutar: `mvn spring-boot:run`
2. Acceder a: `http://localhost:8080/[nombre-proyecto]/h2-console`
3. Conectar con:
   - JDBC URL: `jdbc:h2:mem:testdb`
   - Username: `sa`
   - Password: (vacío)
4. Verificar que se puede acceder a la consola

**Puntuación:** ___/10

**⚠️ SI ESTE ELEMENTO FALTA O ESTÁ INCOMPLETO:**
- ❌ **DETENER evaluación**
- ❌ **Impacto:** Desarrollo sin BD local imposible
- ❌ **Acción requerida:** Generar application-dev.yml completo

---

#### 🔴 PASO 3. application-prod.yml con Oracle (10 puntos)

**Ubicación:** `src/main/resources/application-prod.yml`

**Verificar:**
- [ ] El archivo existe
- [ ] URL Oracle con variables: `url: jdbc:oracle:thin:@${DB_HOST}:${DB_PORT}:${DB_SID}`
- [ ] Driver Oracle: `driver-class-name: oracle.jdbc.OracleDriver`
- [ ] Username con variable: `username: ${DB_USERNAME}`
- [ ] Password con variable: `password: ${DB_PASSWORD}`
- [ ] HikariCP configurado:
  - [ ] `maximum-pool-size: 20` (o variable)
  - [ ] `minimum-idle: 5` (o variable)
  - [ ] `connection-timeout: 30000`
  - [ ] `idle-timeout: 600000`
  - [ ] `max-lifetime: 1800000`
- [ ] Dialect Oracle: `database-platform: org.hibernate.dialect.Oracle12cDialect`
- [ ] DDL auto: `ddl-auto: validate`
- [ ] Show SQL desactivado: `show-sql: false`
- [ ] Optimizaciones de Hibernate:
  - [ ] `jdbc.batch_size: 20`
  - [ ] `order_inserts: true`
  - [ ] `order_updates: true`

**Configuración mínima esperada:**
```yaml
spring:
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:ORCL}
    username: ${DB_USERNAME:uda_user}
    password: ${DB_PASSWORD:uda_password}
    driver-class-name: oracle.jdbc.OracleDriver
    hikari:
      maximum-pool-size: ${DB_POOL_SIZE:20}
      minimum-idle: ${DB_POOL_MIN_IDLE:5}
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
  
  jpa:
    database-platform: org.hibernate.dialect.Oracle12cDialect
    hibernate:
      ddl-auto: validate
    show-sql: false
    properties:
      hibernate:
        jdbc:
          batch_size: 20
        order_inserts: true
        order_updates: true
```

**Archivo .env.prod.example:**
- [ ] Existe en la raíz del proyecto
- [ ] Documenta todas las variables necesarias:

```bash
# Base de datos Oracle
DB_HOST=oracle-prod.example.com
DB_PORT=1521
DB_SID=PROD
DB_USERNAME=uda_prod_user
DB_PASSWORD=CHANGE_THIS_PASSWORD
DB_POOL_SIZE=20
DB_POOL_MIN_IDLE=5
```

**Puntuación:** ___/10

**⚠️ SI ESTE ELEMENTO FALTA O ESTÁ INCOMPLETO:**
- ❌ **DETENER evaluación**
- ❌ **Impacto:** No funciona en producción
- ❌ **Acción requerida:** Generar application-prod.yml completo

---

**📋 Resumen de Configuración de Perfiles:**

| Archivo | Perfil | Base de Datos | Cuándo se usa |
|---------|--------|---------------|---------------|
| `application.yml` | dev (por defecto) | H2 en memoria | Desarrollo local |
| `application-prod.yml` | prod | Oracle | Producción/Staging |

**Activación de perfiles:**
```bash
# Desarrollo (usa application.yml con H2)
mvn spring-boot:run

# Producción (usa application-prod.yml con Oracle)
mvn spring-boot:run -Dspring-boot.run.profiles=prod
# O configurar variable de entorno:
export SPRING_PROFILES_ACTIVE=prod
```

---

#### 🔴 PASO 4. Application.java extiende SpringBootServletInitializer (5 puntos)

**Ubicación:** `src/main/java/com/uda/[proyecto]/Application.java`

**Verificar:**
- [ ] La clase extiende `SpringBootServletInitializer`
- [ ] Tiene método `configure()` sobrescrito correctamente
- [ ] Tiene método `main()` para ejecución standalone
- [ ] Tiene anotación `@SpringBootApplication`
- [ ] Tiene JavaDoc explicativo
- [ ] Importa correctamente:
  - `org.springframework.boot.builder.SpringApplicationBuilder`
  - `org.springframework.boot.web.servlet.support.SpringBootServletInitializer`

**Código esperado:**
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

**Puntuación:** ___/5

**⚠️ SI NO EXTIENDE SpringBootServletInitializer:**
- ❌ **DETENER evaluación**
- ❌ **Impacto:** No se puede desplegar como WAR en Tomcat
- ❌ **Acción requerida:** Modificar Application.java

---

#### 🔴 PASO 5. pom.xml con packaging WAR (5 puntos)

**Ubicación:** `pom.xml`

**Verificar:**
- [ ] Tiene `<packaging>war</packaging>`
- [ ] Tomcat como provided:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```
- [ ] Build sin versión en nombre:
```xml
<build>
    <finalName>${project.artifactId}</finalName>
</build>
```
- [ ] Dependencia H2 para desarrollo:
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Prueba de validación:**
```bash
mvn clean package
ls -lh target/*.war  # Debe existir un archivo .war
```

**Puntuación:** ___/5

**⚠️ SI NO TIENE PACKAGING WAR:**
- ❌ **DETENER evaluación**
- ❌ **Impacto:** Genera JAR en lugar de WAR
- ❌ **Acción requerida:** Modificar pom.xml

---

#### 🟡 PASO 6. GlobalExceptionHandler (5 puntos)

**Ubicación:** `src/main/java/com/uda/[proyecto]/exception/GlobalExceptionHandler.java`

**Verificar:**
- [ ] El archivo existe
- [ ] Tiene anotación `@RestControllerAdvice`
- [ ] Tiene anotación `@Slf4j` (o logger manual)
- [ ] Maneja `ResourceNotFoundException` → retorna 404
- [ ] Maneja `MethodArgumentNotValidException` → retorna 400 con errores de validación
- [ ] Maneja `Exception` genérica → retorna 500
- [ ] NO expone stack traces en producción
- [ ] Retorna `ErrorResponse` estructurado con:
  - `timestamp`
  - `status`
  - `error`
  - `message`
  - `validationErrors` (para errores de validación)
- [ ] Loguea errores apropiadamente

**ErrorResponse.java:**
- [ ] Existe en el paquete `exception`
- [ ] Tiene todos los campos necesarios
- [ ] Usa Lombok o tiene getters/setters

**Código mínimo esperado:**
```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(ResourceNotFoundException ex) {
        log.error("Recurso no encontrado: {}", ex.getMessage());
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.NOT_FOUND.value())
            .error("Not Found")
            .message(ex.getMessage())
            .build();
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach(error -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.BAD_REQUEST.value())
            .error("Validation Error")
            .message("Error en la validación de datos")
            .validationErrors(errors)
            .build();
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        log.error("Error interno del servidor", ex);
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.INTERNAL_SERVER_ERROR.value())
            .error("Internal Server Error")
            .message("Ha ocurrido un error interno en el servidor")
            .build();
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

**Puntuación:** ___/5

**⚠️ SI FALTA O ESTÁ INCOMPLETO:**
- ⚠️ **Advertencia:** Manejo de errores deficiente
- ⚠️ **Impacto:** Stack traces expuestos, mala experiencia de usuario
- ⚠️ **Acción requerida:** Generar GlobalExceptionHandler completo

---

#### 🟡 PASO 7. Validaciones en DTOs (5 puntos)

**Ubicación:** `src/main/java/com/uda/[proyecto]/dto/`

**Verificar en CADA DTO:**
- [ ] Campos obligatorios tienen `@NotNull` o `@NotBlank`
- [ ] Strings tienen `@Size(min=X, max=Y)` con límites apropiados
- [ ] Números tienen `@Min`, `@Max`, `@DecimalMin`, `@DecimalMax` según corresponda
- [ ] Emails tienen `@Email`
- [ ] Patrones específicos tienen `@Pattern`
- [ ] Todas las validaciones tienen `message` descriptivo en español
- [ ] Los mensajes son claros y ayudan al usuario

**Ejemplo mínimo aceptable:**
```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ProductoDTO {
    
    private Long id;
    
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100, message = "El nombre debe tener entre 3 y 100 caracteres")
    private String nombre;
    
    @Size(max = 500, message = "La descripción no puede exceder 500 caracteres")
    private String descripcion;
    
    @NotNull(message = "El precio es obligatorio")
    @DecimalMin(value = "0.01", message = "El precio debe ser mayor a 0")
    @Digits(integer = 10, fraction = 2, message = "Formato de precio inválido")
    private BigDecimal precio;
    
    @Min(value = 0, message = "El stock no puede ser negativo")
    private Integer stock;
}
```

**En Controllers:**
- [ ] Todos los `@PostMapping` usan `@Valid`:
```java
@PostMapping
public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {
    // ...
}
```
- [ ] Todos los `@PutMapping` usan `@Valid`:
```java
@PutMapping("/{id}")
public ResponseEntity<ProductoDTO> update(@PathVariable Long id, @Valid @RequestBody ProductoDTO dto) {
    // ...
}
```

**Puntuación:** ___/5

**⚠️ SI FALTAN VALIDACIONES:**
- ⚠️ **Advertencia:** Datos inválidos pueden llegar a la base de datos
- ⚠️ **Impacto:** Errores en runtime, datos corruptos, problemas de seguridad
- ⚠️ **Acción requerida:** Agregar validaciones a todos los DTOs

---

#### 🟡 PASO 8. Validaciones Yup en Frontend (5 puntos)

**Ubicación:** `src/utils/validationSchemas.js`

**Verificar:**
- [ ] El archivo existe
- [ ] Importa Yup: `import * as yup from 'yup'`
- [ ] Define al menos un schema por cada formulario de la aplicación
- [ ] Cada schema tiene validaciones apropiadas:
  - [ ] `.required()` en campos obligatorios
  - [ ] `.min()` y `.max()` en strings
  - [ ] `.positive()` en números que deben ser positivos
  - [ ] `.integer()` en números enteros
  - [ ] `.email()` en campos de email
  - [ ] `.oneOf()` para confirmación de passwords
- [ ] Todos los mensajes están en español
- [ ] Los mensajes son descriptivos y ayudan al usuario

**Ejemplo mínimo aceptable:**
```javascript
import * as yup from 'yup';

export const productoSchema = yup.object({
  nombre: yup
    .string()
    .required('El nombre es obligatorio')
    .min(3, 'El nombre debe tener al menos 3 caracteres')
    .max(100, 'El nombre no puede exceder 100 caracteres'),
  
  descripcion: yup
    .string()
    .max(500, 'La descripción no puede exceder 500 caracteres'),
  
  precio: yup
    .number()
    .required('El precio es obligatorio')
    .positive('El precio debe ser mayor a 0')
    .typeError('Ingrese un precio válido'),
  
  stock: yup
    .number()
    .required('El stock es obligatorio')
    .min(0, 'El stock no puede ser negativo')
    .integer('El stock debe ser un número entero')
    .typeError('Ingrese un stock válido'),
}).required();

export default {
  productoSchema,
};
```

**En formularios:**
- [ ] Usa `react-hook-form` con `yupResolver`:
```javascript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import { productoSchema } from '../utils/validationSchemas';

function ProductoForm() {
  const {
    control,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(productoSchema),  // ⚠️ OBLIGATORIO
  });
  
  // ...
}
```

**Dependencias en package.json:**
- [ ] `yup` está instalado
- [ ] `@hookform/resolvers` está instalado

**Puntuación:** ___/5

**⚠️ SI FALTAN VALIDACIONES YUP:**
- ⚠️ **Advertencia:** Validación solo HTML5 (insuficiente)
- ⚠️ **Impacto:** Mala experiencia de usuario, mensajes genéricos
- ⚠️ **Acción requerida:** Generar validationSchemas.js completo

---

### 📊 RESUMEN DE VERIFICACIÓN CRÍTICA

**Puntuación de elementos críticos:**

| # | Elemento | Verificado | Puntos | Estado |
|---|----------|------------|--------|--------|
| 1 | JacksonConfig.java | [ ] | ___/10 | 🔴 CRÍTICO |
| 2 | application.yml | [ ] | ___/10 | 🔴 CRÍTICO |
| 3 | application-prod.yml | [ ] | ___/10 | 🔴 CRÍTICO |
| 4 | SpringBootServletInitializer | [ ] | ___/5 | 🔴 CRÍTICO |
| 5 | packaging WAR | [ ] | ___/5 | 🔴 CRÍTICO |
| 6 | GlobalExceptionHandler | [ ] | ___/5 | 🟡 ALTO |
| 7 | Validaciones DTOs | [ ] | ___/5 | 🟡 ALTO |
| 8 | Validaciones Yup | [ ] | ___/5 | 🟡 MEDIO |
| **TOTAL** | | **___/8** | **___/55** | |

**Criterio de aceptación:**

- ✅ **55/55 puntos (8/8 elementos):** EXCELENTE - Continuar con checklist completo
- ⚠️ **45-54 puntos (6-7/8 elementos):** ACEPTABLE - Corregir faltantes antes de continuar
- ❌ **< 45 puntos (< 6/8 elementos):** INSUFICIENTE - Detener y corregir inmediatamente

**SI LA PUNTUACIÓN ES < 45/55:**
1. ❌ **DETENER** la evaluación inmediatamente
2. ❌ **NO** continuar con el resto del checklist
3. ❌ **Corregir** los elementos faltantes o incompletos
4. ❌ **Volver** a verificar desde el inicio de esta sección

---

### ⚠️ ADVERTENCIA IMPORTANTE

**Los elementos marcados como 🔴 CRÍTICO (elementos 1-5) son OBLIGATORIOS.**

**Si falta alguno de estos 5 elementos:**
- La aplicación NO compilará, NO ejecutará, o NO funcionará correctamente
- NO se puede desplegar en Tomcat
- NO cumple con las especificaciones UDA
- Calificación automática: **INSUFICIENTE**

**Los elementos marcados como 🟡 (elementos 6-8) son MUY IMPORTANTES.**

**Si faltan estos elementos:**
- La aplicación funcionará pero con deficiencias graves
- Mala experiencia de usuario
- Problemas de seguridad
- Difícil mantenimiento

---

## ✅ Checklist General

### Estructura del Proyecto

- [ ] La estructura de carpetas coincide exactamente con `ESTRUCTURA_PROYECTO.md`
- [ ] Todos los archivos obligatorios están presentes
- [ ] No hay archivos o carpetas adicionales no especificados
- [ ] Los nombres de archivos y carpetas siguen las convenciones establecidas
- [ ] Existe un `README.md` completo en la raíz del proyecto
- [ ] Existe `.gitignore` configurado correctamente
- [ ] Existe `docker-compose.yml` funcional
- [ ] Existe `.env.example` con todas las variables documentadas

---

## 🔧 Backend - Checklist

### Configuración Maven

- [ ] `pom.xml` existe y está bien formado
- [ ] Versión de Spring Boot es **3.5.0**
- [ ] Versión de Java es **21**
- [ ] `<packaging>war</packaging>` está configurado
- [ ] Todas las dependencias obligatorias están incluidas:
  - [ ] `spring-boot-starter-web`
  - [ ] `spring-boot-starter-data-jpa`
  - [ ] `spring-boot-starter-security`
  - [ ] `spring-boot-starter-validation`
  - [ ] `spring-boot-starter-tomcat` (scope: provided)
  - [ ] `ojdbc11` (Oracle JDBC)
  - [ ] `lombok`
  - [ ] `mapstruct`
  - [ ] `spring-boot-starter-test`
  - [ ] `springdoc-openapi-starter-webmvc-ui`
- [ ] Plugin de Maven Compiler está configurado para Java 21
- [ ] Annotation processors (Lombok, MapStruct) están configurados
- [ ] `<finalName>` está configurado sin versión

### Clase Principal

- [ ] `Application.java` existe en el paquete raíz
- [ ] Extiende `SpringBootServletInitializer`
- [ ] Tiene anotación `@SpringBootApplication`
- [ ] Método `configure()` está implementado
- [ ] Método `main()` está implementado
- [ ] Tiene JavaDoc completo

### Configuración

- [ ] `application.yml` existe y está completo
- [ ] `application.yml` existe con configuración de desarrollo
- [ ] `application-prod.yml` existe con configuración de producción
- [ ] Configuración de datasource usa variables de entorno
- [ ] Configuración de JPA está correcta (dialect: Oracle)
- [ ] `ddl-auto` está en `validate` para producción
- [ ] Configuración de logging está presente
- [ ] Configuración de JWT está presente
- [ ] Puerto del servidor está configurado
- [ ] Context path está configurado

#### SecurityConfig

- [ ] `SecurityConfig.java` existe
- [ ] Tiene anotación `@Configuration` y `@EnableWebSecurity`
- [ ] Configura `SecurityFilterChain`
- [ ] CORS está configurado correctamente
- [ ] CSRF está deshabilitado (con justificación)
- [ ] Sesiones están en modo STATELESS
- [ ] Endpoints públicos están definidos (`/api/v1/auth/**`, `/actuator/health`)
- [ ] Endpoints protegidos requieren autenticación
- [ ] `PasswordEncoder` (BCrypt) está configurado como Bean
- [ ] `AuthenticationManager` está configurado como Bean

#### WebConfig

- [ ] `WebConfig.java` existe
- [ ] Tiene anotación `@Configuration` y `@EnableWebMvc`
- [ ] Implementa `WebMvcConfigurer`
- [ ] Configura resource handlers si es necesario

#### JacksonConfig

- [ ] `JacksonConfig.java` existe
- [ ] Configura serialización de fechas

#### OpenAPIConfig

- [ ] `OpenAPIConfig.java` existe
- [ ] Configura información de la API (título, versión, descripción)
- [ ] Configura esquema de seguridad JWT
- [ ] Tiene contacto y licencia configurados

### Entidades

#### BaseEntity

- [ ] `BaseEntity.java` existe
- [ ] Tiene anotación `@MappedSuperclass`
- [ ] Tiene `@EntityListeners(AuditingEntityListener.class)`
- [ ] Tiene campos de auditoría:
  - [ ] `createdAt` con `@CreatedDate`
  - [ ] `updatedAt` con `@LastModifiedDate`
  - [ ] `active` (Boolean)
- [ ] Usa Lombok (`@Getter`, `@Setter`)

#### Usuario

- [ ] `Usuario.java` existe
- [ ] Implementa `UserDetails`
- [ ] Tiene anotación `@Entity` y `@Table`
- [ ] Tiene `@Id` con estrategia de generación por secuencia
- [ ] Campos obligatorios:
  - [ ] `username` (unique, not null)
  - [ ] `password` (not null)
  - [ ] `email` (unique, not null)
  - [ ] `enabled`
  - [ ] `accountNonExpired`
  - [ ] `accountNonLocked`
  - [ ] `credentialsNonExpired`
- [ ] Relación `@ManyToMany` con `Rol`
- [ ] Implementa métodos de `UserDetails` correctamente
- [ ] Usa Lombok

#### Rol

- [ ] `Rol.java` existe
- [ ] Tiene anotación `@Entity` y `@Table`
- [ ] Tiene `@Id` con estrategia de generación
- [ ] Campo `nombre` (unique, not null)
- [ ] Relación `@ManyToMany` con `Usuario`
- [ ] Usa Lombok

#### Entidades de Dominio

Para cada entidad de dominio:
- [ ] Extiende `BaseEntity`
- [ ] Tiene anotación `@Entity` y `@Table` con nombre en MAYÚSCULAS
- [ ] Tiene `@Id` con `@GeneratedValue` y `@SequenceGenerator`
- [ ] Nombre de secuencia sigue formato `[TABLA]_SEQ`
- [ ] Todos los campos tienen anotaciones JPA apropiadas
- [ ] Validaciones Bean Validation en campos necesarios
- [ ] Usa Lombok (`@Getter`, `@Setter`, `@NoArgsConstructor`, `@AllArgsConstructor`, `@Builder`)
- [ ] Tiene JavaDoc completo

### DTOs

Para cada DTO:
- [ ] Nombre sigue formato `[Entidad]DTO.java`
- [ ] Tiene anotación `@Data`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`
- [ ] Todos los campos tienen validaciones Bean Validation apropiadas:
  - [ ] `@NotNull`, `@NotBlank` donde corresponda
  - [ ] `@Size` con límites apropiados
  - [ ] `@Email` para emails
  - [ ] `@DecimalMin`, `@DecimalMax` para números
  - [ ] `@Pattern` para formatos específicos
- [ ] Mensajes de validación son descriptivos
- [ ] No incluye campos sensibles (passwords sin encriptar)
- [ ] Tiene JavaDoc completo

#### DTOs Especiales

- [ ] `LoginRequestDTO` existe con `username` y `password`
- [ ] `LoginResponseDTO` existe con `token`, `type`, `id`, `username`, `email`, `roles`
- [ ] `RegisterRequestDTO` existe con validaciones completas
- [ ] `PageResponse<T>` existe con campos de paginación
- [ ] `ErrorResponse` existe con `timestamp`, `status`, `error`, `message`, `validationErrors`

### Mappers

Para cada Mapper:
- [ ] Nombre sigue formato `[Entidad]Mapper.java`
- [ ] Es una interfaz con `@Mapper(componentModel = "spring")`
- [ ] Tiene método `toDTO(Entity)` → `DTO`
- [ ] Tiene método `toEntity(DTO)` → `Entity`
- [ ] Tiene método `toDTOList(List<Entity>)` → `List<DTO>`
- [ ] Tiene método `updateEntityFromDTO(DTO, @MappingTarget Entity)`
- [ ] Usa `nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE`
- [ ] Tiene JavaDoc completo

### Repositories

Para cada Repository:
- [ ] Nombre sigue formato `[Entidad]Repository.java`
- [ ] Es una interfaz que extiende `JpaRepository<Entity, Long>`
- [ ] Tiene anotación `@Repository`
- [ ] Métodos personalizados siguen nomenclatura de Spring Data:
  - [ ] `findByActiveTrue()`
  - [ ] `findByIdAndActiveTrue(Long id)`
  - [ ] Queries con `@Query` están bien formadas
- [ ] Extiende `JpaSpecificationExecutor` si usa Specifications
- [ ] Tiene JavaDoc completo

### Services

#### Interfaces

Para cada Service:
- [ ] Nombre sigue formato `[Entidad]Service.java`
- [ ] Es una interfaz (no clase)
- [ ] Define métodos de negocio:
  - [ ] `findAll()` o `findAll(Pageable)`
  - [ ] `findById(Long id)`
  - [ ] `create(DTO)`
  - [ ] `update(Long id, DTO)`
  - [ ] `delete(Long id)`
- [ ] Métodos retornan DTOs (no Entities)
- [ ] Tiene JavaDoc completo con `@param`, `@return`, `@throws`

#### Implementaciones

Para cada ServiceImpl:
- [ ] Nombre sigue formato `[Entidad]ServiceImpl.java`
- [ ] Implementa la interfaz correspondiente
- [ ] Tiene anotaciones:
  - [ ] `@Service`
  - [ ] `@Transactional`
  - [ ] `@RequiredArgsConstructor`
  - [ ] `@Slf4j`
- [ ] Inyecta Repository y Mapper por constructor
- [ ] Métodos de lectura tienen `@Transactional(readOnly = true)`
- [ ] Métodos de escritura tienen `@Transactional`
- [ ] Lanza `ResourceNotFoundException` cuando no encuentra recursos
- [ ] Usa logger para debug e info
- [ ] Convierte Entities a DTOs antes de retornar
- [ ] Valida lógica de negocio
- [ ] Tiene JavaDoc completo

#### AuthService

- [ ] `AuthService.java` existe (interfaz)
- [ ] `AuthServiceImpl.java` existe (implementación)
- [ ] Método `login(LoginRequestDTO)` implementado
- [ ] Método `register(RegisterRequestDTO)` implementado
- [ ] Método `validateToken(String)` implementado
- [ ] Usa `AuthenticationManager` para autenticar
- [ ] Genera tokens JWT
- [ ] Encripta contraseñas con BCrypt
- [ ] Valida que username y email no existan al registrar
- [ ] Asigna rol por defecto al registrar

### Controllers

Para cada Controller:
- [ ] Nombre sigue formato `[Entidad]Controller.java`
- [ ] Tiene anotaciones:
  - [ ] `@RestController`
  - [ ] `@RequestMapping("/api/v1/[entidades]")`
  - [ ] `@RequiredArgsConstructor`
  - [ ] `@Slf4j`
  - [ ] `@CrossOrigin(origins = "*")`
  - [ ] `@Tag` (OpenAPI)
- [ ] Inyecta Service por constructor
- [ ] Endpoints CRUD completos:
  - [ ] `GET /` → `findAll()`
  - [ ] `GET /{id}` → `findById(@PathVariable Long id)`
  - [ ] `POST /` → `create(@Valid @RequestBody DTO)`
  - [ ] `PUT /{id}` → `update(@PathVariable Long id, @Valid @RequestBody DTO)`
  - [ ] `DELETE /{id}` → `delete(@PathVariable Long id)`
- [ ] Usa `@Valid` para validar DTOs
- [ ] Retorna `ResponseEntity` con códigos HTTP apropiados:
  - [ ] 200 OK para GET, PUT
  - [ ] 201 CREATED para POST
  - [ ] 204 NO_CONTENT para DELETE
- [ ] Usa `@PreAuthorize` para autorización si es necesario
- [ ] Tiene anotaciones OpenAPI (`@Operation`, `@ApiResponses`)
- [ ] Loguea requests importantes
- [ ] Tiene JavaDoc completo

#### AuthController

- [ ] `AuthController.java` existe
- [ ] Endpoint `POST /api/v1/auth/login` implementado
- [ ] Endpoint `POST /api/v1/auth/register` implementado
- [ ] Endpoint `GET /api/v1/auth/validate` implementado
- [ ] Endpoints son públicos (no requieren autenticación)
- [ ] Valida DTOs con `@Valid`
- [ ] Retorna códigos HTTP apropiados

### Excepciones

- [ ] `GlobalExceptionHandler.java` existe
- [ ] Tiene anotación `@RestControllerAdvice`
- [ ] Maneja excepciones:
  - [ ] `ResourceNotFoundException` → 404
  - [ ] `MethodArgumentNotValidException` → 400
  - [ ] `BusinessException` → 400
  - [ ] `Exception` (genérica) → 500
- [ ] Retorna `ErrorResponse` estructurado
- [ ] Loguea errores apropiadamente
- [ ] No expone stack traces en producción
- [ ] Incluye timestamp en respuestas de error

#### Excepciones Personalizadas

- [ ] `ResourceNotFoundException.java` existe
- [ ] `BusinessException.java` existe
- [ ] `ValidationException.java` existe
- [ ] Todas extienden `RuntimeException`
- [ ] Tienen constructores apropiados
- [ ] Tienen JavaDoc

### Seguridad

#### JWT

- [ ] `JwtUtil.java` existe
- [ ] Tiene anotación `@Component`
- [ ] Métodos implementados:
  - [ ] `generateToken(UserDetails)`
  - [ ] `extractUsername(String token)`
  - [ ] `validateToken(String token, UserDetails)`
  - [ ] `extractExpiration(String token)`
- [ ] Usa clave secreta desde configuración
- [ ] Configura tiempo de expiración desde configuración
- [ ] Usa algoritmo HS256

#### Filtros

- [ ] `JwtRequestFilter.java` existe
- [ ] Extiende `OncePerRequestFilter`
- [ ] Tiene anotación `@Component`
- [ ] Extrae token del header `Authorization`
- [ ] Valida formato `Bearer {token}`
- [ ] Valida token con `JwtUtil`
- [ ] Establece autenticación en `SecurityContext`
- [ ] Maneja excepciones apropiadamente

#### UserDetailsService

- [ ] `UserDetailsServiceImpl.java` existe
- [ ] Implementa `UserDetailsService`
- [ ] Tiene anotación `@Service`
- [ ] Método `loadUserByUsername()` implementado
- [ ] Carga usuario desde `UsuarioRepository`
- [ ] Lanza `UsernameNotFoundException` si no encuentra
- [ ] Verifica que usuario esté habilitado

### Specifications (Opcional)

Si se usan Specifications:
- [ ] Clase `[Entidad]Specification.java` existe
- [ ] Método estático `withFilters(FilterDTO)` implementado
- [ ] Construye predicados dinámicamente
- [ ] Maneja valores null apropiadamente
- [ ] Usa criterios case-insensitive para búsquedas de texto

### Aspects (Opcional)

Si se usan Aspects:
- [ ] `LoggingAspect.java` existe
- [ ] Tiene anotaciones `@Aspect` y `@Component`
- [ ] Define pointcuts apropiados
- [ ] Loguea entrada/salida de métodos
- [ ] Loguea excepciones
- [ ] Mide tiempos de ejecución

### Tests Backend

#### Tests Unitarios

Para cada Service:
- [ ] Clase `[Entidad]ServiceTest.java` existe
- [ ] Tiene anotación `@ExtendWith(MockitoExtension.class)`
- [ ] Usa `@Mock` para dependencias
- [ ] Usa `@InjectMocks` para clase bajo test
- [ ] Tests para todos los métodos públicos:
  - [ ] `testFindById_Success()`
  - [ ] `testFindById_NotFound()`
  - [ ] `testCreate_Success()`
  - [ ] `testUpdate_Success()`
  - [ ] `testUpdate_NotFound()`
  - [ ] `testDelete_Success()`
  - [ ] `testDelete_NotFound()`
- [ ] Usa patrón AAA (Arrange, Act, Assert)
- [ ] Verifica llamadas a mocks con `verify()`
- [ ] Usa assertions apropiadas

#### Tests de Integración

Para cada Controller:
- [ ] Clase `[Entidad]ControllerTest.java` existe
- [ ] Tiene anotaciones `@SpringBootTest` y `@AutoConfigureMockMvc`
- [ ] Inyecta `MockMvc`
- [ ] Usa `@MockBean` para Service
- [ ] Tests para todos los endpoints:
  - [ ] `testGetAll()`
  - [ ] `testGetById_Success()`
  - [ ] `testGetById_NotFound()`
  - [ ] `testCreate_Success()`
  - [ ] `testCreate_ValidationError()`
  - [ ] `testUpdate_Success()`
  - [ ] `testDelete_Success()`
- [ ] Usa `@WithMockUser` para autenticación
- [ ] Verifica códigos de estado HTTP
- [ ] Verifica contenido de respuestas JSON

#### Tests de Repository

Para cada Repository:
- [ ] Clase `[Entidad]RepositoryTest.java` existe
- [ ] Tiene anotación `@DataJpaTest`
- [ ] Inyecta `TestEntityManager`
- [ ] Tests para métodos personalizados
- [ ] Verifica persistencia correcta
- [ ] Verifica consultas personalizadas

### Configuración de Logging

- [ ] `logback-spring.xml` existe (opcional pero recomendado)
- [ ] Configuración de appenders (console, file)
- [ ] Configuración de niveles por paquete
- [ ] Patrón de log incluye timestamp, nivel, clase, mensaje
- [ ] Logs rotan por tamaño o fecha
- [ ] Logs antiguos se comprimen o eliminan

### Scripts SQL

- [ ] Carpeta `src/main/resources/db/migration/` existe
- [ ] Script `V1__initial_schema.sql` existe
- [ ] Script crea tablas en MAYÚSCULAS
- [ ] Script crea secuencias
- [ ] Script crea índices
- [ ] Script crea constraints con nombres
- [ ] Script incluye comentarios en tablas y columnas
- [ ] Script `V2__insert_roles.sql` existe
- [ ] Inserta roles iniciales (ROLE_USER, ROLE_ADMIN)
- [ ] Crea usuario admin por defecto (opcional)

---

## 🎨 Frontend - Checklist

### Configuración

- [ ] `package.json` existe y está completo
- [ ] Versiones de dependencias son las especificadas:
  - [ ] React 18+
  - [ ] Material-UI 5+
  - [ ] React Router 6+
  - [ ] Axios 1+
  - [ ] React Hook Form 7+
  - [ ] Yup 1+
- [ ] Scripts npm están definidos:
  - [ ] `dev`
  - [ ] `build`
  - [ ] `preview`
  - [ ] `test`
  - [ ] `lint`
- [ ] `vite.config.js` existe y configura:
  - [ ] Plugin de React
  - [ ] Puerto del servidor (5173)
  - [ ] Proxy para API
- [ ] `vitest.config.js` existe y configura:
  - [ ] Entorno jsdom
  - [ ] Setup file
  - [ ] Coverage
- [ ] `.eslintrc.js` existe
- [ ] `.prettierrc` existe
- [ ] `.gitignore` está configurado

### Estructura Base

- [ ] `index.html` existe
- [ ] `main.jsx` existe y renderiza `<App />`
- [ ] `App.jsx` existe y configura:
  - [ ] `BrowserRouter`
  - [ ] `ThemeProvider`
  - [ ] `SnackbarProvider` o `NotificationProvider`
  - [ ] `AuthProvider`
  - [ ] `Routes` con todas las rutas

### UI y Design System

- [ ] Material-UI está instalado (`@mui/material`, `@mui/icons-material`)
- [ ] Tema UDA está en `frontend/src/theme/`
- [ ] Estructura del tema es correcta:
  - [ ] `theme/index.js` existe
  - [ ] `theme/palette.js` existe con colores UDA
  - [ ] `theme/typography.js` existe
  - [ ] `theme/components.js` existe con overrides
  - [ ] `theme/shadows.js` existe
- [ ] `ThemeProvider` envuelve la aplicación en `main.jsx`
- [ ] `CssBaseline` está incluido
- [ ] Todos los componentes usan MUI (no HTML nativo)
- [ ] No hay estilos inline (`style={{}}`)
- [ ] No hay colores hardcodeados
- [ ] Se usa `sx` prop para estilos personalizados
- [ ] Se usa `theme.spacing()` para espaciado
- [ ] Se usa `theme.palette.*` para colores
- [ ] Se usa `Typography` para todo el texto
- [ ] Responsive design con breakpoints de MUI
- [ ] Iconos de `@mui/icons-material`
- [ ] No hay wrappers innecesarios sobre componentes MUI
- [ ] No se usa `!important` en estilos

**📖 Referencia**: [UI_STANDARDS.md](../UI_STANDARDS.md)

### Servicios

#### API Base

- [ ] `api.js` existe en `src/services/`
- [ ] Crea instancia de Axios con `baseURL`
- [ ] Configura timeout
- [ ] Configura headers por defecto
- [ ] Interceptor de request agrega token JWT
- [ ] Interceptor de response maneja errores:
  - [ ] 401 → redirige a login
  - [ ] 403 → muestra mensaje de acceso denegado
  - [ ] 404 → muestra mensaje de no encontrado
  - [ ] 500 → muestra mensaje de error del servidor

#### AuthService

- [ ] `authService.js` existe
- [ ] Métodos implementados:
  - [ ] `login(credentials)`
  - [ ] `register(userData)`
  - [ ] `validateToken(token)`
  - [ ] `getToken()`
  - [ ] `getUser()`
- [ ] Usa instancia de `api`
- [ ] Tiene JSDoc completo

#### Servicios de Dominio

Para cada servicio:
- [ ] Nombre sigue formato `[entidad]Service.js`
- [ ] Métodos CRUD implementados:
  - [ ] `getAll(params)`
  - [ ] `getById(id)`
  - [ ] `create(data)`
  - [ ] `update(id, data)`
  - [ ] `delete(id)`
  - [ ] `search(query)` (opcional)
- [ ] Usa instancia de `api`
- [ ] Retorna promesas
- [ ] Tiene JSDoc completo

### Contextos

#### AuthContext

- [ ] `AuthContext.jsx` existe
- [ ] Crea contexto con `createContext()`
- [ ] Exporta `AuthProvider`
- [ ] Exporta hook `useAuth()`
- [ ] Estado incluye:
  - [ ] `user`
  - [ ] `loading`
  - [ ] `isAuthenticated`
- [ ] Funciones incluyen:
  - [ ] `login(credentials)`
  - [ ] `register(userData)`
  - [ ] `logout()`
  - [ ] `hasRole(role)`
  - [ ] `hasAnyRole(roles)`
- [ ] Verifica token al cargar
- [ ] Guarda/elimina token en localStorage
- [ ] Redirige apropiadamente

#### NotificationContext

- [ ] `NotificationContext.jsx` existe
- [ ] Exporta `NotificationProvider`
- [ ] Exporta hook `useNotification()`
- [ ] Funciones incluyen:
  - [ ] `showSuccess(message)`
  - [ ] `showError(message)`
  - [ ] `showWarning(message)`
  - [ ] `showInfo(message)`
- [ ] Usa `Snackbar` de MUI
- [ ] Configura posición (top-right)
- [ ] Configura auto-hide

### Hooks Personalizados

#### useAuth

- [ ] Exportado desde `AuthContext` o archivo separado
- [ ] Lanza error si se usa fuera de `AuthProvider`
- [ ] Retorna todas las funciones y estado de autenticación

#### useNotification

- [ ] Exportado desde `NotificationContext` o archivo separado
- [ ] Lanza error si se usa fuera de `NotificationProvider`
- [ ] Retorna funciones de notificación

#### usePagination

- [ ] `usePagination.js` existe
- [ ] Parámetros: `fetchFunction`, `initialPageSize`
- [ ] Estado incluye:
  - [ ] `data`
  - [ ] `loading`
  - [ ] `page`
  - [ ] `pageSize`
  - [ ] `totalElements`
  - [ ] `totalPages`
  - [ ] `sortField`
  - [ ] `sortDirection`
- [ ] Funciones incluyen:
  - [ ] `loadData(filters)`
  - [ ] `handlePageChange(newPage)`
  - [ ] `handlePageSizeChange(newSize)`
  - [ ] `handleSortChange(field)`
  - [ ] `reset()`
- [ ] Tiene JSDoc completo

#### useErrorHandler

- [ ] `useErrorHandler.js` existe
- [ ] Funciones incluyen:
  - [ ] `handleError(error, defaultMessage)`
  - [ ] `handleValidationErrors(errors, setError)`
  - [ ] `withErrorHandling(asyncFn, errorMessage)`
- [ ] Integra con sistema de notificaciones
- [ ] Extrae mensajes de diferentes formatos de error

#### Hooks de Dominio

Para cada hook de dominio (ej: `useProductos`):
- [ ] Nombre sigue formato `use[Entidad].js`
- [ ] Estado incluye:
  - [ ] `[entidades]`
  - [ ] `loading`
  - [ ] `error`
- [ ] Funciones incluyen:
  - [ ] `fetch[Entidades]()`
  - [ ] `create[Entidad](data)`
  - [ ] `update[Entidad](id, data)`
  - [ ] `delete[Entidad](id)`
- [ ] Usa servicio correspondiente
- [ ] Maneja errores con `useErrorHandler`
- [ ] Muestra notificaciones de éxito/error

### Utilidades

#### constants.js

- [ ] `constants.js` existe
- [ ] Define constantes en UPPER_SNAKE_CASE
- [ ] Incluye:
  - [ ] `API_BASE_URL`
  - [ ] Códigos de estado HTTP
  - [ ] Mensajes comunes
  - [ ] Configuraciones

#### validators.js

- [ ] `validators.js` existe
- [ ] Funciones de validación exportadas
- [ ] Validaciones comunes (email, teléfono, etc.)

#### validationSchemas.js

- [ ] `validationSchemas.js` existe
- [ ] Esquemas Yup exportados:
  - [ ] `loginSchema`
  - [ ] `registerSchema`
  - [ ] Esquemas para cada formulario de dominio
- [ ] Mensajes de error descriptivos
- [ ] Validaciones completas

#### errorHandler.js

- [ ] `errorHandler.js` existe
- [ ] Función `getErrorMessage(error)` implementada
- [ ] Función `logError(error, context)` implementada
- [ ] Maneja diferentes tipos de errores

#### sanitizer.js

- [ ] `sanitizer.js` existe
- [ ] Funciones implementadas:
  - [ ] `sanitizeHtml(html)`
  - [ ] `sanitizeText(text)`
  - [ ] `sanitizeUrl(url)`
  - [ ] `sanitizeEmail(email)`
  - [ ] `sanitizeObject(obj)`
- [ ] Usa DOMPurify para HTML

#### logger.js

- [ ] `logger.js` existe
- [ ] Funciones implementadas:
  - [ ] `debug(message, data)`
  - [ ] `info(message, data)`
  - [ ] `warn(message, data)`
  - [ ] `error(message, error, data)`
- [ ] Diferencia entre desarrollo y producción
- [ ] Envía a servicio externo en producción (opcional)

### Componentes Comunes

#### Header

- [ ] `Header.jsx` existe en `components/common/`
- [ ] Usa `AppBar` y `Toolbar` de MUI
- [ ] Muestra logo/título de la aplicación
- [ ] Muestra menú de navegación
- [ ] Muestra información del usuario
- [ ] Botón de logout
- [ ] Responsive (menú hamburguesa en móvil)

#### Footer

- [ ] `Footer.jsx` existe en `components/common/`
- [ ] Muestra información de copyright
- [ ] Enlaces útiles (opcional)
- [ ] Responsive

#### Loading

- [ ] `Loading.jsx` existe en `components/common/`
- [ ] Usa `CircularProgress` de MUI
- [ ] Centrado en pantalla
- [ ] Acepta prop `message` (opcional)

#### ErrorMessage

- [ ] `ErrorMessage.jsx` existe en `components/common/`
- [ ] Usa `Alert` de MUI
- [ ] Acepta props `message` y `severity`
- [ ] Muestra icono apropiado

#### ConfirmDialog

- [ ] `ConfirmDialog.jsx` existe en `components/common/`
- [ ] Usa `Dialog` de MUI
- [ ] Props:
  - [ ] `open`
  - [ ] `title`
  - [ ] `message`
  - [ ] `onConfirm`
  - [ ] `onCancel`
  - [ ] `confirmText` (opcional)
  - [ ] `cancelText` (opcional)
  - [ ] `confirmColor` (opcional)
- [ ] Tiene PropTypes

#### ErrorBoundary

- [ ] `ErrorBoundary.jsx` existe en `components/common/`
- [ ] Es un componente de clase
- [ ] Implementa `componentDidCatch()`
- [ ] Muestra UI de fallback
- [ ] Loguea errores
- [ ] Muestra stack trace en desarrollo
- [ ] Botón para reintentar

#### ProtectedRoute

- [ ] `ProtectedRoute.jsx` existe en `components/common/`
- [ ] Usa `useAuth()`
- [ ] Verifica autenticación
- [ ] Verifica roles si se especifican
- [ ] Redirige a `/login` si no autenticado
- [ ] Redirige a `/unauthorized` si no tiene roles
- [ ] Muestra loading mientras verifica
- [ ] Tiene PropTypes

### Layouts

#### MainLayout

- [ ] `MainLayout.jsx` existe en `layouts/`
- [ ] Usa `Outlet` de React Router
- [ ] Incluye `Header`
- [ ] Incluye `Footer`
- [ ] Sidebar (opcional)
- [ ] Responsive
- [ ] Maneja estado de sidebar (abierto/cerrado)

### Páginas

#### Home

- [ ] `Home.jsx` existe en `pages/`
- [ ] Página de bienvenida
- [ ] Enlaces a secciones principales
- [ ] Responsive

#### Login

- [ ] `Login.jsx` existe en `pages/`
- [ ] Usa `react-hook-form`
- [ ] Usa esquema de validación Yup
- [ ] Campos:
  - [ ] Username
  - [ ] Password (con toggle de visibilidad)
- [ ] Botón de submit
- [ ] Link a página de registro
- [ ] Muestra errores de validación
- [ ] Muestra errores de autenticación
- [ ] Deshabilita botón mientras carga
- [ ] Redirige a dashboard después de login exitoso
- [ ] Responsive

#### Register

- [ ] `Register.jsx` existe en `pages/`
- [ ] Usa `react-hook-form`
- [ ] Usa esquema de validación Yup
- [ ] Campos:
  - [ ] Username
  - [ ] Email
  - [ ] Password (con toggle de visibilidad)
  - [ ] Confirm Password
  - [ ] Nombre
  - [ ] Apellido
- [ ] Validación de contraseñas coincidentes
- [ ] Botón de submit
- [ ] Link a página de login
- [ ] Muestra errores de validación
- [ ] Muestra errores de registro
- [ ] Deshabilita botón mientras carga
- [ ] Redirige a dashboard después de registro exitoso
- [ ] Responsive

#### Dashboard

- [ ] `Dashboard.jsx` existe en `pages/`
- [ ] Muestra información del usuario
- [ ] Cards con estadísticas (opcional)
- [ ] Enlaces rápidos a secciones
- [ ] Protegida con `ProtectedRoute`
- [ ] Responsive

#### Unauthorized

- [ ] `Unauthorized.jsx` existe en `pages/`
- [ ] Muestra mensaje de acceso denegado
- [ ] Icono apropiado
- [ ] Botón para volver al dashboard
- [ ] Responsive

#### NotFound

- [ ] `NotFound.jsx` existe en `pages/`
- [ ] Muestra mensaje de página no encontrada
- [ ] Icono 404
- [ ] Botón para volver al inicio
- [ ] Responsive

#### Páginas de Listado

Para cada página de listado (ej: `ProductoList.jsx`):
- [ ] Nombre sigue formato `[Entidad]List.jsx`
- [ ] Usa hook personalizado (ej: `useProductos`)
- [ ] Muestra tabla o grid de items
- [ ] Incluye filtros de búsqueda
- [ ] Incluye paginación
- [ ] Botón para crear nuevo
- [ ] Botones de editar y eliminar por item
- [ ] Diálogo de confirmación para eliminar
- [ ] Muestra loading mientras carga
- [ ] Muestra mensaje si no hay datos
- [ ] Maneja errores apropiadamente
- [ ] Responsive (tabla → cards en móvil)
- [ ] Protegida con `ProtectedRoute`

#### Páginas de Formulario

Para cada página de formulario (ej: `ProductoForm.jsx`):
- [ ] Nombre sigue formato `[Entidad]Form.jsx`
- [ ] Usa `react-hook-form`
- [ ] Usa esquema de validación Yup
- [ ] Detecta modo (crear/editar) por parámetro de ruta
- [ ] Carga datos si es edición
- [ ] Todos los campos necesarios
- [ ] Validaciones en tiempo real
- [ ] Muestra errores de validación
- [ ] Botón de guardar
- [ ] Botón de cancelar
- [ ] Deshabilita botones mientras guarda
- [ ] Muestra loading mientras carga (edición)
- [ ] Redirige a listado después de guardar
- [ ] Maneja errores del servidor
- [ ] Responsive
- [ ] Protegida con `ProtectedRoute`

### Componentes de Dominio

Para cada componente de dominio:

#### [Entidad]Table

- [ ] Nombre sigue formato `[Entidad]Table.jsx`
- [ ] Usa `Table` de MUI
- [ ] Props:
  - [ ] `data` (array de items)
  - [ ] `loading`
  - [ ] `onEdit`
  - [ ] `onDelete`
  - [ ] `sortField` (opcional)
  - [ ] `sortDirection` (opcional)
  - [ ] `onSort` (opcional)
- [ ] Columnas apropiadas
- [ ] Botones de acción por fila
- [ ] Sorting en headers (opcional)
- [ ] Muestra loading
- [ ] Muestra mensaje si no hay datos
- [ ] Tiene PropTypes

#### [Entidad]Card

- [ ] Nombre sigue formato `[Entidad]Card.jsx`
- [ ] Usa `Card` de MUI
- [ ] Props:
  - [ ] `[entidad]` (objeto)
  - [ ] `onEdit`
  - [ ] `onDelete`
- [ ] Muestra información relevante
- [ ] Botones de acción
- [ ] Responsive
- [ ] Usa `memo` para optimización
- [ ] Tiene PropTypes

#### [Entidad]Dialog

- [ ] Nombre sigue formato `[Entidad]Dialog.jsx`
- [ ] Usa `Dialog` de MUI
- [ ] Props:
  - [ ] `open`
  - [ ] `[entidad]` (para edición, null para crear)
  - [ ] `onClose`
  - [ ] `onSave`
- [ ] Usa `react-hook-form`
- [ ] Usa esquema de validación Yup
- [ ] Todos los campos necesarios
- [ ] Validaciones
- [ ] Botones de guardar y cancelar
- [ ] Carga datos si es edición
- [ ] Resetea formulario al cerrar
- [ ] Tiene PropTypes

### Tests Frontend

#### Setup

- [ ] `setup.js` existe en `src/__tests__/`
- [ ] Configura matchers de `@testing-library/jest-dom`
- [ ] Configura cleanup después de cada test
- [ ] Mock de `localStorage`
- [ ] Mock de `window.matchMedia`

#### Tests de Páginas

Para cada página:
- [ ] Archivo de test existe (ej: `Login.test.jsx`)
- [ ] Tests incluyen:
  - [ ] Renderizado inicial
  - [ ] Validaciones de formulario
  - [ ] Submit exitoso
  - [ ] Manejo de errores
  - [ ] Navegación
- [ ] Usa `render` de `@testing-library/react`
- [ ] Usa `screen` para queries
- [ ] Usa `fireEvent` o `userEvent` para interacciones
- [ ] Usa `waitFor` para operaciones asíncronas
- [ ] Mockea servicios

#### Tests de Componentes

Para cada componente:
- [ ] Archivo de test existe
- [ ] Tests incluyen:
  - [ ] Renderizado con diferentes props
  - [ ] Interacciones del usuario
  - [ ] Callbacks se llaman correctamente
- [ ] Usa PropTypes para validar props

#### Tests de Servicios

Para cada servicio:
- [ ] Archivo de test existe
- [ ] Tests incluyen:
  - [ ] Llamadas exitosas
  - [ ] Manejo de errores
  - [ ] Parámetros correctos
- [ ] Mockea instancia de `api`

#### Tests de Hooks

Para cada hook personalizado:
- [ ] Archivo de test existe
- [ ] Usa `renderHook` de `@testing-library/react`
- [ ] Tests incluyen:
  - [ ] Estado inicial
  - [ ] Cambios de estado
  - [ ] Efectos secundarios
  - [ ] Manejo de errores

### Configuración de Linting

- [ ] `.eslintrc.js` configura:
  - [ ] Parser de React
  - [ ] Plugins (react, react-hooks)
  - [ ] Reglas apropiadas
  - [ ] Entorno (browser, es2021)
- [ ] `.prettierrc` configura:
  - [ ] `singleQuote: true`
  - [ ] `trailingComma: 'es5'`
  - [ ] `tabWidth: 2`
  - [ ] `semi: true`

---

## 🗄️ Base de Datos - Checklist

### Scripts de Esquema

#### Tablas

- [ ] Carpeta `database/schema/` existe
- [ ] Script `01_create_tables.sql` existe
- [ ] Todas las tablas en MAYÚSCULAS
- [ ] Todas las columnas en MAYÚSCULAS
- [ ] Cada tabla tiene:
  - [ ] `ID NUMBER(19) NOT NULL`
  - [ ] `CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL`
  - [ ] `UPDATED_AT TIMESTAMP`
  - [ ] `ACTIVE NUMBER(1) DEFAULT 1 NOT NULL`
- [ ] Tipos de datos apropiados:
  - [ ] `NUMBER(19)` para IDs
  - [ ] `VARCHAR2(n)` para strings con longitud
  - [ ] `NUMBER(10,2)` para decimales
  - [ ] `TIMESTAMP` para fechas
  - [ ] `NUMBER(1)` para booleanos
- [ ] Comentarios en tablas y columnas

#### Secuencias

- [ ] Script `02_create_sequences.sql` existe
- [ ] Una secuencia por tabla
- [ ] Formato: `[TABLA]_SEQ`
- [ ] `START WITH 1`
- [ ] `INCREMENT BY 1`
- [ ] `NOCACHE`
- [ ] `NOCYCLE`

#### Índices

- [ ] Script `03_create_indexes.sql` existe
- [ ] Índices en columnas de búsqueda frecuente
- [ ] Índices en Foreign Keys
- [ ] Formato: `IDX_[TABLA]_[COLUMNA]`
- [ ] Índices compuestos donde sea apropiado

#### Constraints

- [ ] Script `04_create_constraints.sql` existe
- [ ] Primary Keys: `PK_[TABLA]`
- [ ] Foreign Keys: `FK_[TABLA]_[REFERENCIA]`
- [ ] Unique: `UK_[TABLA]_[COLUMNA]`
- [ ] Check: `CK_[TABLA]_[COLUMNA]`
- [ ] Todos los constraints tienen nombres

### Scripts de Datos

#### Datos Iniciales

- [ ] Carpeta `database/data/` existe
- [ ] Script `01_insert_roles.sql` existe
- [ ] Inserta roles:
  - [ ] `ROLE_USER`
  - [ ] `ROLE_ADMIN`
  - [ ] `ROLE_MODERATOR` (opcional)
- [ ] Usa secuencias para IDs
- [ ] Incluye `COMMIT` al final

#### Datos de Prueba

- [ ] Script `02_insert_test_data.sql` existe (opcional)
- [ ] Datos de ejemplo para desarrollo
- [ ] Usa secuencias para IDs
- [ ] Incluye `COMMIT` al final

### Validación de Esquema

- [ ] Todas las tablas tienen Primary Key
- [ ] Todas las Foreign Keys tienen índice
- [ ] Todas las relaciones tienen constraints
- [ ] No hay columnas sin tipo de dato
- [ ] No hay nombres en minúsculas
- [ ] Todos los scripts son ejecutables sin errores
- [ ] Scripts son idempotentes (pueden ejecutarse múltiples veces)

---

## 🐳 Docker - Checklist

### Dockerfile Backend

- [ ] `docker/Dockerfile.backend` existe
- [ ] Usa multi-stage build
- [ ] Stage 1: Build con Maven
- [ ] Stage 2: Runtime con JRE
- [ ] Copia JAR/WAR desde stage de build
- [ ] Expone puerto 8080
- [ ] Define variables de entorno
- [ ] Configura healthcheck
- [ ] Usuario no-root

### Dockerfile Frontend

- [ ] `docker/Dockerfile.frontend` existe
- [ ] Usa multi-stage build
- [ ] Stage 1: Build con Node
- [ ] Stage 2: Runtime con Nginx
- [ ] Copia build desde stage anterior
- [ ] Copia configuración de Nginx
- [ ] Expone puerto 80
- [ ] Configura healthcheck

### Nginx Config

- [ ] `docker/nginx.conf` existe
- [ ] Configura servidor en puerto 80
- [ ] Configura root a `/usr/share/nginx/html`
- [ ] Maneja rutas de React Router
- [ ] Configura compresión gzip
- [ ] Configura cache para assets estáticos
- [ ] Configura headers de seguridad
- [ ] Proxy para API (opcional)

### Docker Compose

- [ ] `docker-compose.yml` existe en raíz
- [ ] Define servicios:
  - [ ] `postgres` o `oracle` (base de datos)
  - [ ] `backend`
  - [ ] `frontend`
- [ ] Configuración de base de datos:
  - [ ] Variables de entorno
  - [ ] Volumen para persistencia
  - [ ] Healthcheck
- [ ] Configuración de backend:
  - [ ] Depende de base de datos
  - [ ] Variables de entorno
  - [ ] Puertos mapeados
  - [ ] Healthcheck
- [ ] Configuración de frontend:
  - [ ] Depende de backend
  - [ ] Puertos mapeados
- [ ] Red compartida
- [ ] Volúmenes definidos

### Variables de Entorno

- [ ] `.env.example` existe
- [ ] Documenta todas las variables necesarias:
  - [ ] Base de datos (host, port, name, user, password)
  - [ ] JWT (secret, expiration)
  - [ ] Server (port, context-path)
  - [ ] CORS (allowed-origins)
  - [ ] Frontend (API URL)
- [ ] Valores de ejemplo (no producción)
- [ ] Comentarios explicativos

---

## 📚 Documentación - Checklist

### README Principal

- [ ] `README.md` existe en raíz
- [ ] Incluye:
  - [ ] Título y descripción del proyecto
  - [ ] Tabla de contenidos
  - [ ] Características principales
  - [ ] Stack tecnológico
  - [ ] Requisitos previos
  - [ ] Instrucciones de instalación (backend y frontend)
  - [ ] Instrucciones de configuración
  - [ ] Instrucciones de ejecución
  - [ ] Ejemplos de uso
  - [ ] Documentación de API (link a Swagger)
  - [ ] Instrucciones de testing
  - [ ] Instrucciones de despliegue
  - [ ] Guía de contribución
  - [ ] Licencia
  - [ ] Contacto/Autores
- [ ] Formato Markdown correcto
- [ ] Enlaces funcionan
- [ ] Ejemplos de código son correctos

### README Backend

- [ ] `backend/README.md` existe
- [ ] Incluye:
  - [ ] Descripción del backend
  - [ ] Estructura de carpetas
  - [ ] Configuración específica
  - [ ] Comandos Maven útiles
  - [ ] Información de testing
  - [ ] Notas de desarrollo

### README Frontend

- [ ] `frontend/README.md` existe
- [ ] Incluye:
  - [ ] Descripción del frontend
  - [ ] Estructura de carpetas
  - [ ] Configuración específica
  - [ ] Scripts npm disponibles
  - [ ] Información de testing
  - [ ] Notas de desarrollo

### Documentación Adicional

- [ ] `docs/API.md` existe (opcional)
- [ ] `docs/DEPLOYMENT.md` existe (opcional)
- [ ] `docs/CONTRIBUTING.md` existe (opcional)
- [ ] `docs/CHANGELOG.md` existe (opcional)

---

## 🔒 Seguridad - Checklist

### Backend

- [ ] Contraseñas encriptadas con BCrypt
- [ ] JWT implementado correctamente
- [ ] Tokens tienen expiración
- [ ] CORS configurado (no `*` en producción)
- [ ] CSRF deshabilitado solo para APIs REST stateless
- [ ] Validación de entrada en todos los endpoints
- [ ] Sanitización de datos
- [ ] No se exponen stack traces en producción
- [ ] No hay credenciales hardcodeadas
- [ ] Variables sensibles en variables de entorno
- [ ] Logging no incluye información sensible
- [ ] Headers de seguridad configurados
- [ ] Rate limiting implementado (opcional)
- [ ] SQL injection prevenido (uso de JPA)
- [ ] XSS prevenido (validación y sanitización)

### Frontend

- [ ] Tokens almacenados de forma segura
- [ ] Rutas protegidas con `ProtectedRoute`
- [ ] Validación de entrada en formularios
- [ ] Sanitización de HTML con DOMPurify
- [ ] No se expone información sensible en código
- [ ] HTTPS en producción (configuración de servidor)
- [ ] Content Security Policy configurado
- [ ] XSS prevenido (sanitización)
- [ ] CSRF tokens si es necesario
- [ ] Validación de URLs antes de usar

### Base de Datos

- [ ] Usuario de base de datos con permisos mínimos
- [ ] Contraseñas fuertes
- [ ] Conexión encriptada (SSL/TLS en producción)
- [ ] Backups regulares configurados
- [ ] No hay datos sensibles sin encriptar
- [ ] Auditoría de cambios (timestamps)

---

## ⚡ Performance - Checklist

### Backend

- [ ] Paginación implementada en listados
- [ ] Índices en columnas de búsqueda
- [ ] Lazy loading en relaciones JPA
- [ ] Queries optimizadas (no N+1)
- [ ] Cache implementado donde sea apropiado
- [ ] Connection pooling configurado
- [ ] Compresión de respuestas habilitada
- [ ] Logging asíncrono (opcional)

### Frontend

- [ ] Code splitting implementado
- [ ] Lazy loading de componentes
- [ ] Imágenes optimizadas
- [ ] Bundle size optimizado
- [ ] React.memo en componentes apropiados
- [ ] useMemo y useCallback donde sea necesario
- [ ] Debouncing en búsquedas
- [ ] Paginación en listados grandes
- [ ] Cache de requests (opcional)

### Base de Datos

- [ ] Índices en columnas frecuentemente consultadas
- [ ] Índices en Foreign Keys
- [ ] Queries optimizadas
- [ ] No hay SELECT *
- [ ] Uso apropiado de JOIN vs subqueries

---

## 🧪 Testing - Checklist

### Backend

- [ ] Tests unitarios para Services
- [ ] Tests de integración para Controllers
- [ ] Tests de Repository
- [ ] Cobertura > 70%
- [ ] Tests pasan sin errores
- [ ] Tests son independientes
- [ ] Tests usan datos de prueba (no producción)
- [ ] Mocks apropiados
- [ ] Assertions claras

### Frontend

- [ ] Tests de componentes
- [ ] Tests de páginas
- [ ] Tests de servicios
- [ ] Tests de hooks
- [ ] Cobertura > 60%
- [ ] Tests pasan sin errores
- [ ] Tests son independientes
- [ ] Mocks apropiados
- [ ] Assertions claras

---

## 🚀 Despliegue - Checklist

### Preparación

- [ ] Variables de entorno documentadas
- [ ] Configuración de producción separada
- [ ] Scripts de despliegue incluidos
- [ ] Instrucciones de despliegue documentadas
- [ ] Healthchecks configurados
- [ ] Logs configurados apropiadamente

### Docker

- [ ] Dockerfiles funcionan correctamente
- [ ] Docker Compose funciona correctamente
- [ ] Imágenes optimizadas (tamaño)
- [ ] Multi-stage builds
- [ ] Healthchecks configurados
- [ ] Volúmenes para persistencia

### Tomcat (Backend)

- [ ] WAR se genera correctamente
- [ ] Configuración para Tomcat incluida
- [ ] Context path configurado
- [ ] Datasource configurado
- [ ] Variables de entorno documentadas

---

## ✅ Checklist Final

### Antes de Entregar

#### ⚠️ Elementos Críticos (OBLIGATORIO)
- [ ] ✅ **Verificación crítica completada:** ___/55 puntos
- [ ] ✅ JacksonConfig.java existe y funciona (fechas como strings ISO)
- [ ] ✅ application.yml con H2 funciona (perfil dev por defecto)
- [ ] ✅ application-prod.yml con Oracle está completo
- [ ] ✅ Application.java extiende SpringBootServletInitializer
- [ ] ✅ pom.xml genera WAR correctamente
- [ ] ✅ GlobalExceptionHandler maneja errores correctamente
- [ ] ✅ DTOs tienen validaciones Bean Validation
- [ ] ✅ validationSchemas.js existe con Yup

**SI ALGUNO FALTA: DETENER Y CORREGIR ANTES DE CONTINUAR**

#### Elementos Importantes

- [ ] Todos los archivos obligatorios existen
- [ ] No hay errores de compilación
- [ ] No hay errores de linting
- [ ] Todos los tests pasan
- [ ] No hay TODOs en el código
- [ ] No hay código comentado
- [ ] No hay console.log en producción
- [ ] No hay credenciales hardcodeadas
- [ ] README está completo
- [ ] Documentación está actualizada
- [ ] .gitignore está configurado
- [ ] Variables de entorno están documentadas
- [ ] Proyecto compila y ejecuta correctamente
- [ ] Backend responde en endpoints esperados
- [ ] Frontend se conecta correctamente al backend
- [ ] Autenticación funciona
- [ ] CRUD completo funciona
- [ ] Validaciones funcionan
- [ ] Manejo de errores funciona
- [ ] Paginación funciona (si aplica)
- [ ] Búsqueda funciona (si aplica)

#### Validación de Calidad

- [ ] Código sigue convenciones de nomenclatura
- [ ] Código está bien documentado
- [ ] Código es legible y mantenible
- [ ] No hay duplicación de código
- [ ] Principios SOLID aplicados
- [ ] Separación de responsabilidades clara
- [ ] Manejo de errores robusto
- [ ] Seguridad implementada correctamente
- [ ] Performance es aceptable
- [ ] Tests cubren casos principales
- [ ] Documentación es clara y completa

---

## 📊 Métricas de Calidad

### Elementos Críticos

| Métrica | Objetivo | Verificación |
|---------|----------|--------------|
| Elementos críticos completos | 8/8 (100%) | Verificar sección "Elementos Críticos" |
| Puntuación mínima | 55/55 puntos | Sumar puntuación de cada elemento |
| JacksonConfig funcional | Sí | Probar endpoint con LocalDateTime |
| H2 funcional en dev | Sí | Acceder a /h2-console |
| WAR se genera | Sí | `mvn clean package` → archivo .war existe |

**⚠️ Si alguna métrica no se cumple, la aplicación NO es apta para entrega.**

### Backend

| Métrica | Objetivo | Verificación |
|---------|----------|--------------|
| Cobertura de tests | > 70% | `mvn test jacoco:report` |
| Complejidad ciclomática | < 10 por método | Análisis estático |
| Duplicación de código | < 5% | SonarQube (opcional) |
| Vulnerabilidades | 0 críticas | `mvn dependency-check:check` |
| Warnings de compilación | 0 | `mvn clean compile` |

### Frontend

| Métrica | Objetivo | Verificación |
|---------|----------|--------------|
| Cobertura de tests | > 60% | `npm test -- --coverage` |
| Bundle size | < 500KB (gzipped) | `npm run build` |
| Lighthouse Score | > 90 | Chrome DevTools |
| ESLint warnings | 0 | `npm run lint` |
| Vulnerabilidades | 0 críticas | `npm audit` |

---

## 🔍 Revisión por Pares

Si es posible, realizar revisión de código verificando:

- [ ] Lógica de negocio es correcta
- [ ] Código es eficiente
- [ ] Código es seguro
- [ ] Código es testeable
- [ ] Código sigue estándares del proyecto
- [ ] Documentación es adecuada
- [ ] Tests son suficientes
- [ ] No hay code smells evidentes

---

## 📝 Notas Finales

Este checklist debe ser completado **antes** de considerar la aplicación como terminada. Cada ítem marcado representa una verificación exitosa de calidad.

**La IA debe usar este checklist como guía final de validación antes de entregar el código generado.**

---

## 🔗 Documentos Relacionados

- **[VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)**: Guía paso a paso para validar el código generado
- **[ESPECIFICACION_COMPLETA.md](ESPECIFICACION_COMPLETA.md)**: Detalles técnicos completos
- **[REGLAS_GENERACION.md](REGLAS_GENERACION.md)**: Reglas que la IA debe seguir
- **[ESTRUCTURA_PROYECTO.md](ESTRUCTURA_PROYECTO.md)**: Estructura exacta de carpetas y archivos
