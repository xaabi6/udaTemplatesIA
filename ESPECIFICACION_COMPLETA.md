# Especificación Completa - Aplicación UDA

---

## ⚠️ ELEMENTOS CRÍTICOS OBLIGATORIOS

**ANTES de empezar a leer este documento, debes saber que hay 8 elementos CRÍTICOS que son OBLIGATORIOS.**

**Si falta alguno de estos elementos, la aplicación NO funcionará:**

1. 🔴 **JacksonConfig.java** - Sin esto, las fechas se serializan mal
2. 🔴 **application.yml con H2** - Sin esto, no se puede desarrollar sin Oracle
3. 🔴 **application-prod.yml con Oracle** - Sin esto, no funciona en producción
4. 🔴 **SpringBootServletInitializer** - Sin esto, no se puede desplegar en Tomcat
5. 🔴 **packaging WAR** - Sin esto, genera JAR en lugar de WAR
6. 🟡 **GlobalExceptionHandler** - Sin esto, errores mal manejados
7. 🟡 **Validaciones en DTOs** - Sin esto, datos inválidos llegan a BD
8. 🟡 **Validaciones Yup** - Sin esto, validación insuficiente en frontend

**📖 Detalles completos en:** `REGLAS_GENERACION.md` sección "Elementos Críticos"

---

## 📋 Índice

1. [Stack Tecnológico](#stack-tecnológico)
2. [Estructura del Proyecto](#estructura-del-proyecto)
3. [Archivos .gitignore](#archivos-gitignore)
4. [Backend - Configuración Esencial](#backend---configuración-esencial)
5. [Frontend - Configuración Esencial](#frontend---configuración-esencial)
6. [Base de Datos](#base-de-datos)
7. [Seguridad Básica](#seguridad-básica)
8. [Despliegue](#despliegue)
9. [Checklist de Generación](#checklist-de-generación)

---

## Stack Tecnológico

### Backend
- **Java 21 LTS** + **Spring Boot 3.5.x**
- **Oracle Database** + Spring Data JPA
- **Spring Security** + JWT
- **Lombok** + **MapStruct**
- **Apache Tomcat** (despliegue WAR)

### Frontend
- **React 18+** + **Vite**
- **Material UI (MUI)**
- **React Router** + **Axios**
- **React Hook Form** + **Yup**

---

### Tabla de Compatibilidad

| Componente | Versión Mínima | Versión Recomendada | Versión Máxima Probada |
|------------|----------------|---------------------|------------------------|
| Java | 17 | 21 | 25 |
| Spring Boot | 3.1.0 | 3.5.0 | 3.5.x |
| Maven | 3.8.0 | 3.9.x | 3.9.x |
| Node.js | 18.0.0 | 20.x | 22.x |
| npm | 9.0.0 | 10.x | 10.x |
| Oracle Database | 12c | 19c | 21c |
| H2 Database | 2.1.0 | 2.2.x | 2.2.x |
| React | 18.0.0 | 18.3.x | 18.3.x |
| Material-UI | 5.0.0 | 5.15.x | 5.15.x |
| Vite | 4.0.0 | 5.1.x | 5.1.x |
| Apache Tomcat | 10.0.0 | 10.1.x | 10.1.x |

**⚠️ IMPORTANTE:** Usar las versiones recomendadas para garantizar compatibilidad.

---

## Estructura del Proyecto

```
proyecto-uda/
├── backend/
│   ├── src/main/java/com/uda/[proyecto]/
│   │   ├── Application.java
│   │   ├── config/
│   │   │   ├── SecurityConfig.java
│   │   │   └── WebConfig.java
│   │   ├── controller/
│   │   ├── service/
│   │   │   └── impl/
│   │   ├── repository/
│   │   ├── entity/
│   │   ├── dto/
│   │   ├── mapper/
│   │   └── exception/
│   │       ├── GlobalExceptionHandler.java
│   │       └── ResourceNotFoundException.java
│   ├── src/main/resources/
│   │   ├── application.yml
│   │   └── application-prod.yml
│   └── pom.xml
│
├── frontend/
│   ├── src/
│   │   ├── main.jsx
│   │   ├── App.jsx
│   │   ├── pages/
│   │   ├── components/
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── hooks/
│   │   ├── contexts/
│   │   └── styles/
│   │       └── theme.js
│   ├── package.json
│   └── vite.config.js
│
└── README.md
```

---

## Archivos .gitignore

### Backend - .gitignore

```gitignore
# Compiled class files
*.class

# Log files
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# Virtual machine crash logs
hs_err_pid*
replay_pid*

# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties
.mvn/wrapper/maven-wrapper.jar

# Gradle
.gradle
build/
!gradle/wrapper/gradle-wrapper.jar
!**/src/main/**/build/
!**/src/test/**/build/

# IntelliJ IDEA
.idea/
*.iws
*.iml
*.ipr
out/
!**/src/main/**/out/
!**/src/test/**/out/

# Eclipse
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache
bin/
!**/src/main/**/bin/
!**/src/test/**/bin/

# NetBeans
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/

# VS Code
.vscode/

# Mac
.DS_Store

# Windows
Thumbs.db
ehthumbs.db
Desktop.ini

# Application specific
application-local.yml
application-local.properties
*.env
.env.local
```

### Frontend - .gitignore

```gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# Dependencies
node_modules/
.pnp
.pnp.js

# Testing
/coverage
*.lcov
.nyc_output

# Production
dist/
dist-ssr/
build/
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Vite
vite.config.js.timestamp-*
vite.config.ts.timestamp-*

# TypeScript
*.tsbuildinfo

# Optional npm cache directory
.npm

# Optional eslint cache
.eslintcache

# Optional stylelint cache
.stylelintcache

# Microbundle cache
.rpt2_cache/
.rts2_cache_cjs/
.rts2_cache_es/
.rts2_cache_umd/

# Optional REPL history
.node_repl_history

# Output of 'npm pack'
*.tgz

# Yarn
.yarn-integrity
.yarn/cache
.yarn/unplugged
.yarn/build-state.yml
.yarn/install-state.gz
.pnp.*

# Stores VSCode versions used for testing VSCode extensions
.vscode-test

# yarn v2
.yarn/cache
.yarn/unplugged
.yarn/build-state.yml
.yarn/install-state.gz
.pnp.*
```

### Raíz del Proyecto - .gitignore

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
.env.*.local

# Build outputs
target/
dist/
build/
node_modules/

# Temporary files
*.tmp
*.temp
*.swp
*.swo
*~

# Database
*.db
*.sqlite
*.sqlite3

# Backups
*.bak
*.backup
```

---

## Backend - Configuración Esencial

### ⚠️ ELEMENTO CRÍTICO: JacksonConfig.java

**Este archivo es OBLIGATORIO y debe generarse SIEMPRE.**

**Ubicación:** `src/main/java/com/uda/[proyecto]/config/JacksonConfig.java`

**Problema que resuelve:**
Sin esta configuración, las fechas (`LocalDateTime`, `LocalDate`) se serializan como arrays en lugar de strings ISO-8601.

**Código completo:**

```java
package com.uda.[nombre-proyecto].config;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

/**
 * Configuración de Jackson para serialización correcta de fechas.
 * 
 * Sin esta configuración, LocalDateTime se serializa como array:
 * [2024, 1, 15, 10, 30, 45, 123000000]
 * 
 * Con esta configuración, se serializa como string ISO-8601:
 * "2024-01-15T10:30:45.123"
 * 
 * @author UDA
 * @version 1.0.0
 */
@Configuration
public class JacksonConfig {
    
    /**
     * Configura ObjectMapper con soporte para Java 8 Date/Time API.
     * 
     * @return ObjectMapper configurado
     */
    @Bean
    @Primary
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        
        // Registrar módulo para Java 8 Date/Time
        mapper.registerModule(new JavaTimeModule());
        
        // Desactivar serialización de fechas como timestamps
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        
        return mapper;
    }
}
```

**⚠️ IMPORTANTE:** Este archivo debe generarse ANTES de cualquier entidad que use `LocalDateTime` o `LocalDate`.

### 1. pom.xml (Dependencias Clave)

**⚠️ ELEMENTOS CRÍTICOS en pom.xml:**

1. `<packaging>war</packaging>` - OBLIGATORIO para Tomcat
2. Tomcat con `scope=provided` - OBLIGATORIO
3. Dependencia H2 - OBLIGATORIA para desarrollo
4. `<finalName>` sin versión - RECOMENDADO

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.5.0</version>
        <relativePath/>
    </parent>
    
    <groupId>com.uda</groupId>
    <artifactId>nombre-proyecto</artifactId>
    <version>1.0.0</version>
    <!-- ⚠️ CRÍTICO: WAR para despliegue en Tomcat -->
    <packaging>war</packaging>
    
    <properties>
        <java.version>21</java.version>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <mapstruct.version>1.6.3</mapstruct.version>
        <lombok.version>1.18.34</lombok.version>
    </properties>
    
    <dependencies>
        <!-- Spring Boot Starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <!-- ⚠️ CRÍTICO: Tomcat como provided (no incluir en WAR) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        
        <!-- Oracle Database -->
        <dependency>
            <groupId>com.oracle.database.jdbc</groupId>
            <artifactId>ojdbc11</artifactId>
        </dependency>

        <!-- ⚠️ CRÍTICO: H2 para desarrollo sin Oracle -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        
        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>
        
        <!-- MapStruct -->
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>${mapstruct.version}</version>
        </dependency>
        
        <!-- JWT -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>0.11.5</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <version>0.11.5</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <version>0.11.5</version>
            <scope>runtime</scope>
        </dependency>
        
        <!-- Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <!-- ⚠️ RECOMENDADO: Nombre sin versión para facilitar despliegue -->
        <finalName>${project.artifactId}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>21</source>
                    <target>21</target>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                            <version>${lombok.version}</version>
                        </path>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${mapstruct.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 2. ⚠️ CRÍTICO: Application.java

**Esta clase DEBE extender SpringBootServletInitializer para despliegue en Tomcat.**

**Ubicación:** `src/main/java/com/uda/[proyecto]/Application.java`

```java
package com.uda.[nombre-proyecto];

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

/**
 * Clase principal de la aplicación UDA.
 * 
 * IMPORTANTE: Extiende SpringBootServletInitializer para permitir
 * despliegue como WAR en Apache Tomcat.
 * 
 * Sin esta extensión, la aplicación solo funcionaría como JAR standalone
 * y NO se podría desplegar en Tomcat.
 * 
 * @author UDA
 * @version 1.0.0
 */
@SpringBootApplication
public class Application extends SpringBootServletInitializer {
    
    /**
     * Configura la aplicación para despliegue en contenedor servlet (Tomcat).
     * 
     * Este método es llamado por el contenedor servlet al iniciar la aplicación.
     * 
     * @param application Builder de la aplicación Spring
     * @return Builder configurado
     */
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
    
    /**
     * Punto de entrada para ejecución standalone (desarrollo).
     * 
     * @param args Argumentos de línea de comandos
     */
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**⚠️ VERIFICAR:**
- [ ] La clase extiende `SpringBootServletInitializer`
- [ ] Método `configure()` está sobrescrito
- [ ] Método `main()` está presente
- [ ] Imports correctos

**Sin esto, NO se puede desplegar en Tomcat.**

### 3. Configuración de Perfiles (application.yml)

**La aplicación DEBE tener 3 archivos de configuración:**

1. `application.yml` - Configuración base con H2 (desarrollo)
2. `application-prod.yml` - Producción con Oracle

**Nota:** El archivo base (`application.yml`) usa H2 por defecto, lo que permite desarrollo sin necesidad de Oracle instalado.

---

#### 3.1. ⚠️ CRÍTICO: application.yml (Desarrollo con H2)

**Ubicación:** `src/main/resources/application.yml`

**Este archivo es OBLIGATORIO para desarrollo sin Oracle.**

```yaml
spring:
  application:
    name: nombre-proyecto
  # Perfil activo (se sobrescribe con variable de entorno)
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}
  # Configuración de H2 (base de datos en memoria)
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password: 
  
  # Consola H2 para debugging
  h2:
    console:
      enabled: true
      settings:
        trace: false
        web-allow-others: false
      path: /h2-console
      settings:
        web-allow-others: false
  
  # Configuración JPA para H2
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: create-drop  # Recrea BD en cada inicio
    show-sql: true
    properties:
      hibernate:
        format_sql: true
        use_sql_comments: true

server:
  port: ${SERVER_PORT:8080}
  servlet:
    context-path: /${spring.application.name}
  error:
    include-message: always
    include-binding-errors: always

logging:
  level:
    root: DEBUG
    com.uda: TRACE
    org.hibernate.SQL: DEBUG
    org.hibernate.type.descriptor.sql.BasicBinder: TRACE
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
```

**Dependencia necesaria en pom.xml:**
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Acceso a H2 Console:**
- URL: `http://localhost:8080/[nombre-proyecto]/h2-console`
- JDBC URL: `jdbc:h2:mem:testdb`
- Username: `sa`
- Password: (vacío)

**Ejecución:**
```bash
# Desarrollo (usa H2 por defecto)
mvn spring-boot:run

# O explícitamente
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

---

#### 3.2. ⚠️ CRÍTICO: application-prod.yml (Producción con Oracle)

**Ubicación:** `src/main/resources/application-prod.yml`

**Este archivo es OBLIGATORIO para producción.**

```yaml
spring:
  application:
    name: nombre-proyecto
  # Perfil activo (se sobrescribe con variable de entorno)
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}
  # Configuración de Oracle
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:ORCL}
    username: ${DB_USERNAME:uda_user}
    password: ${DB_PASSWORD:uda_password}
    driver-class-name: oracle.jdbc.OracleDriver
    
    # Pool de conexiones HikariCP
    hikari:
      maximum-pool-size: ${DB_POOL_SIZE:20}
      minimum-idle: ${DB_POOL_MIN_IDLE:5}
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
      leak-detection-threshold: 60000
  
  # Configuración JPA para Oracle
  jpa:
    database-platform: org.hibernate.dialect.Oracle12cDialect
    hibernate:
      ddl-auto: validate  # NO modificar BD en producción
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
    show-sql: false
    properties:
      hibernate:
        format_sql: false
        jdbc:
          batch_size: 20
        order_inserts: true
        order_updates: true
        query:
          in_clause_parameter_padding: true

server:
  port: ${SERVER_PORT:8080}
  servlet:
    context-path: /${spring.application.name}
  error:
    include-message: always
    include-binding-errors: always

logging:
  level:
    root: WARN
    com.uda: INFO
    org.hibernate: WARN
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
  file:
    name: logs/application.log
    max-size: 10MB
    max-history: 30
```

**Archivo .env.prod.example (documentar variables):**

```bash
# Base de datos Oracle
DB_HOST=oracle-prod.example.com
DB_PORT=1521
DB_SID=PROD
DB_USERNAME=uda_prod_user
DB_PASSWORD=CHANGE_THIS_PASSWORD

# Pool de conexiones
DB_POOL_SIZE=20
DB_POOL_MIN_IDLE=5

# Servidor
SERVER_PORT=8080
SPRING_PROFILES_ACTIVE=prod

# JWT (si se usa)
JWT_SECRET=CHANGE_THIS_SECRET_KEY_MIN_256_BITS
JWT_EXPIRATION=86400000
```

**⚠️ IMPORTANTE:** 
- En desarrollo: `mvn spring-boot:run` (usa application.yml con H2)
- En producción: Configurar variable `SPRING_PROFILES_ACTIVE=prod` (usa application-prod.yml con Oracle)

### 4. Entidad

```java
package com.uda.nombreproyecto.entity;

import jakarta.persistence.*;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import lombok.*;

import java.math.BigDecimal;
import java.time.LocalDateTime;

/**
 * Entidad Producto.
 * Representa la tabla PRODUCTOS en Oracle.
 */
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
    
    @NotBlank
    @Column(name = "NOMBRE", nullable = false, length = 100)
    private String nombre;
    
    @Column(name = "DESCRIPCION", length = 500)
    private String descripcion;
    
    @NotNull
    @Column(name = "PRECIO", precision = 10, scale = 2)
    private BigDecimal precio;
    
    @Column(name = "STOCK")
    private Integer stock;
    
    @Column(name = "CATEGORIA", length = 50)
    private String categoria;
    
    @Column(name = "ACTIVO")
    private Boolean activo = true;
    
    @Column(name = "CREATED_AT")
    private LocalDateTime createdAt;
    
    @Column(name = "UPDATED_AT")
    private LocalDateTime updatedAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        if (activo == null) {
            activo = true;
        }
    }
    
    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```

### 5. DTO (Data Transfer Object)

#### ⚠️ ELEMENTO CRÍTICO: Validaciones en DTOs

**Todos los DTOs DEBEN tener validaciones Bean Validation.**

**Problema que resuelve:**
Sin validaciones, datos inválidos llegan a la base de datos:
- Strings vacíos donde no deberían
- Números negativos donde deben ser positivos
- Emails inválidos
- Etc.

**Validaciones obligatorias:**
- `@NotNull` / `@NotBlank` en campos obligatorios
- `@Size` en strings con límites
- `@Min` / `@Max` en números con rangos
- `@Email` en emails
- `@Pattern` para formatos específicos
- Mensajes descriptivos en español

**Ejemplo completo:**

```java
package com.uda.nombreproyecto.dto;

import jakarta.validation.constraints.*;
import lombok.*;

import java.math.BigDecimal;
import java.time.LocalDateTime;

/**
 * DTO para Producto.
 * Objeto de transferencia de datos para la API REST.
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
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
    
    @Size(max = 50, message = "La categoría no puede exceder 50 caracteres")
    private String categoria;
    
    private Boolean activo;
    
    private LocalDateTime createdAt;
    
    private LocalDateTime updatedAt;
}
```

#### Validaciones de Bean Validation - Referencia Completa

**Validaciones de String:**
```java
@NotNull(message = "No puede ser null")
@NotBlank(message = "No puede estar vacío")
@NotEmpty(message = "No puede estar vacío")
@Size(min = 3, max = 100, message = "Debe tener entre 3 y 100 caracteres")
@Pattern(regexp = "^[A-Z0-9-]+$", message = "Solo mayúsculas, números y guiones")
@Email(message = "Email inválido")
```

**Validaciones de Números:**
```java
@NotNull(message = "El número es obligatorio")
@Min(value = 0, message = "Debe ser mayor o igual a 0")
@Max(value = 100, message = "Debe ser menor o igual a 100")
@DecimalMin(value = "0.01", message = "Debe ser mayor a 0")
@DecimalMax(value = "999.99", message = "Debe ser menor a 999.99")
@Positive(message = "Debe ser positivo")
@PositiveOrZero(message = "Debe ser positivo o cero")
@Negative(message = "Debe ser negativo")
@NegativeOrZero(message = "Debe ser negativo o cero")
@Digits(integer = 10, fraction = 2, message = "Formato numérico inválido")
```

**Validaciones de Fecha (java.time):**
```java
@NotNull(message = "La fecha es obligatoria")
@Past(message = "La fecha debe ser pasada")
@PastOrPresent(message = "La fecha debe ser pasada o presente")
@Future(message = "La fecha debe ser futura")
@FutureOrPresent(message = "La fecha debe ser futura o presente")
```

**Validaciones de Colecciones:**
```java
@NotEmpty(message = "La lista no puede estar vacía")
@Size(min = 1, max = 10, message = "Debe tener entre 1 y 10 elementos")
```

**Validaciones Booleanas:**
```java
@NotNull(message = "El valor booleano es obligatorio")
@AssertTrue(message = "Debe ser verdadero")
@AssertFalse(message = "Debe ser falso")
```

**Ejemplo completo de DTO con todas las validaciones:**

```java
package com.uda.nombreproyecto.dto;

import jakarta.validation.constraints.*;
import lombok.*;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;

/**
 * DTO de ejemplo con todas las validaciones.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class EjemploCompletoDTO {
    
    // ID (no requiere validación en creación)
    private Long id;
    
    // String - obligatorio con longitud
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100, message = "El nombre debe tener entre 3 y 100 caracteres")
    private String nombre;
    
    // String - opcional con longitud máxima
    @Size(max = 500, message = "La descripción no puede exceder 500 caracteres")
    private String descripcion;
    
    // Email
    @NotBlank(message = "El email es obligatorio")
    @Email(message = "El email debe ser válido")
    private String email;
    
    // String con patrón (ej: código alfanumérico)
    @Pattern(regexp = "^[A-Z0-9-]+$", message = "El código solo puede contener letras mayúsculas, números y guiones")
    private String codigo;
    
    // BigDecimal - precio
    @NotNull(message = "El precio es obligatorio")
    @DecimalMin(value = "0.01", message = "El precio debe ser mayor a 0")
    @Digits(integer = 10, fraction = 2, message = "Formato de precio inválido")
    private BigDecimal precio;
    
    // Integer - stock
    @NotNull(message = "El stock es obligatorio")
    @Min(value = 0, message = "El stock no puede ser negativo")
    private Integer stock;
    
    // Integer - cantidad con rango
    @Min(value = 1, message = "La cantidad mínima es 1")
    @Max(value = 100, message = "La cantidad máxima es 100")
    private Integer cantidad;
    
    // LocalDate - fecha pasada
    @NotNull(message = "La fecha de nacimiento es obligatoria")
    @Past(message = "La fecha de nacimiento debe ser pasada")
    private LocalDate fechaNacimiento;
    
    // LocalDate - fecha pasada o presente
    @PastOrPresent(message = "La fecha de publicación no puede ser futura")
    private LocalDate fechaPublicacion;
    
    // LocalDate - fecha futura
    @Future(message = "La fecha de vencimiento debe ser futura")
    private LocalDate fechaVencimiento;
    
    // LocalDate - fecha futura o presente
    @FutureOrPresent(message = "La fecha de entrega debe ser futura o presente")
    private LocalDate fechaEntrega;
    
    // Boolean
    @NotNull(message = "El estado activo es obligatorio")
    private Boolean activo;
    
    // Campos de auditoría (sin validación)
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}
```

---

### 6. Mapper

```java
package com.uda.nombreproyecto.mapper;

import com.uda.nombreproyecto.dto.ProductoDTO;
import com.uda.nombreproyecto.entity.Producto;
import org.mapstruct.*;

import java.util.List;

/**
 * Mapper para conversión entre Producto y ProductoDTO.
 * Utiliza MapStruct para generación automática de código.
 */
@Mapper(
    componentModel = "spring",
    nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE
)
public interface ProductoMapper {
    
    /**
     * Convierte una entidad Producto a ProductoDTO.
     */
    ProductoDTO toDTO(Producto entity);
    
    /**
     * Convierte un ProductoDTO a entidad Producto.
     */
    Producto toEntity(ProductoDTO dto);
    
    /**
     * Convierte una lista de entidades a lista de DTOs.
     */
    List<ProductoDTO> toDTOList(List<Producto> entities);
    
    /**
     * Actualiza una entidad existente con datos del DTO.
     * Ignora valores null del DTO.
     */
    @BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
    void updateEntityFromDTO(ProductoDTO dto, @MappingTarget Producto entity);
}
```

### 7. Repository

```java
package com.uda.nombreproyecto.repository;

import com.uda.nombreproyecto.entity.Producto;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

/**
 * Repositorio para la entidad Producto.
 * Proporciona acceso a datos mediante Spring Data JPA.
 */
@Repository
public interface ProductoRepository extends JpaRepository<Producto, Long> {
    
    /**
     * Busca todos los productos activos.
     */
    List<Producto> findByActivoTrue();
    
    /**
     * Busca un producto por ID solo si está activo.
     */
    Optional<Producto> findByIdAndActivoTrue(Long id);
    
    /**
     * Busca productos por nombre (case insensitive).
     */
    @Query("SELECT p FROM Producto p WHERE LOWER(p.nombre) LIKE LOWER(CONCAT('%', :nombre, '%')) AND p.activo = true")
    List<Producto> findByNombreContainingIgnoreCase(@Param("nombre") String nombre);
    
    /**
     * Busca productos por categoría.
     */
    List<Producto> findByCategoriaAndActivoTrue(String categoria);
}
```

### 8. Service Interface

```java
package com.uda.nombreproyecto.service;

import com.uda.nombreproyecto.dto.ProductoDTO;

import java.util.List;

/**
 * Interfaz de servicio para Producto.
 * Define las operaciones de negocio disponibles.
 */
public interface ProductoService {
    
    /**
     * Obtiene todos los productos activos.
     */
    List<ProductoDTO> findAll();
    
    /**
     * Obtiene un producto por su ID.
     */
    ProductoDTO findById(Long id);
    
    /**
     * Crea un nuevo producto.
     */
    ProductoDTO create(ProductoDTO dto);
    
    /**
     * Actualiza un producto existente.
     */
    ProductoDTO update(Long id, ProductoDTO dto);
    
    /**
     * Elimina un producto (soft delete).
     */
    void delete(Long id);
    
    /**
     * Busca productos por nombre.
     */
    List<ProductoDTO> searchByNombre(String nombre);
}
```

### 9. Service Implementation

```java
package com.uda.nombreproyecto.service.impl;

import com.uda.nombreproyecto.dto.ProductoDTO;
import com.uda.nombreproyecto.entity.Producto;
import com.uda.nombreproyecto.exception.ResourceNotFoundException;
import com.uda.nombreproyecto.mapper.ProductoMapper;
import com.uda.nombreproyecto.repository.ProductoRepository;
import com.uda.nombreproyecto.service.ProductoService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.stream.Collectors;

/**
 * Implementación del servicio de Producto.
 * Contiene la lógica de negocio.
 */
@Service
@RequiredArgsConstructor
@Slf4j
@Transactional(readOnly = true)
public class ProductoServiceImpl implements ProductoService {
    
    private final ProductoRepository repository;
    private final ProductoMapper mapper;
    
    @Override
    public List<ProductoDTO> findAll() {
        log.debug("Obteniendo todos los productos activos");
        return repository.findByActivoTrue().stream()
            .map(mapper::toDTO)
            .collect(Collectors.toList());
    }
    
    @Override
    public ProductoDTO findById(Long id) {
        log.debug("Buscando producto con ID: {}", id);
        return repository.findByIdAndActivoTrue(id)
            .map(mapper::toDTO)
            .orElseThrow(() -> new ResourceNotFoundException("Producto no encontrado con ID: " + id));
    }
    
    @Override
    @Transactional
    public ProductoDTO create(ProductoDTO dto) {
        log.info("Creando nuevo producto: {}", dto.getNombre());
        Producto entity = mapper.toEntity(dto);
        entity.setActivo(true);
        Producto saved = repository.save(entity);
        log.info("Producto creado con ID: {}", saved.getId());
        return mapper.toDTO(saved);
    }
    
    @Override
    @Transactional
    public ProductoDTO update(Long id, ProductoDTO dto) {
        log.info("Actualizando producto con ID: {}", id);
        Producto entity = repository.findByIdAndActivoTrue(id)
            .orElseThrow(() -> new ResourceNotFoundException("Producto no encontrado con ID: " + id));
        
        mapper.updateEntityFromDTO(dto, entity);
        Producto updated = repository.save(entity);
        log.info("Producto actualizado con ID: {}", id);
        return mapper.toDTO(updated);
    }
    
    @Override
    @Transactional
    public void delete(Long id) {
        log.info("Eliminando producto con ID: {}", id);
        Producto entity = repository.findByIdAndActivoTrue(id)
            .orElseThrow(() -> new ResourceNotFoundException("Producto no encontrado con ID: " + id));
        
        entity.setActivo(false);
        repository.save(entity);
        log.info("Producto eliminado (soft delete) con ID: {}", id);
    }
    
    @Override
    public List<ProductoDTO> searchByNombre(String nombre) {
        log.debug("Buscando productos por nombre: {}", nombre);
        return repository.findByNombreContainingIgnoreCase(nombre).stream()
            .map(mapper::toDTO)
            .collect(Collectors.toList());
    }
}
```

### 10. Controller

```java
package com.uda.nombreproyecto.controller;

import com.uda.nombreproyecto.dto.ProductoDTO;
import com.uda.nombreproyecto.service.ProductoService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * Controlador REST para Producto.
 * Expone los endpoints de la API.
 */
@RestController
@RequestMapping("/api/v1/productos")
@RequiredArgsConstructor
@Slf4j
@CrossOrigin(origins = "*")
public class ProductoController {
    
    private final ProductoService service;
    
    /**
     * Obtiene todos los productos.
     */
    @GetMapping
    public ResponseEntity<List<ProductoDTO>> findAll() {
        log.info("GET /api/v1/productos - Obteniendo todos los productos");
        return ResponseEntity.ok(service.findAll());
    }
    
    /**
     * Obtiene un producto por ID.
     */
    @GetMapping("/{id}")
    public ResponseEntity<ProductoDTO> findById(@PathVariable Long id) {
        log.info("GET /api/v1/productos/{}", id);
        return ResponseEntity.ok(service.findById(id));
    }
    
    /**
     * Crea un nuevo producto.
     */
    @PostMapping
    public ResponseEntity<ProductoDTO> create(@Valid @RequestBody ProductoDTO dto) {
        log.info("POST /api/v1/productos - Creando producto: {}", dto.getNombre());
        return ResponseEntity.status(HttpStatus.CREATED).body(service.create(dto));
    }
    
    /**
     * Actualiza un producto existente.
     */
    @PutMapping("/{id}")
    public ResponseEntity<ProductoDTO> update(
            @PathVariable Long id,
            @Valid @RequestBody ProductoDTO dto) {
        log.info("PUT /api/v1/productos/{}", id);
        return ResponseEntity.ok(service.update(id, dto));
    }
    
    /**
     * Elimina un producto.
     */
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        log.info("DELETE /api/v1/productos/{}", id);
        service.delete(id);
        return ResponseEntity.noContent().build();
    }
    
    /**
     * Busca productos por nombre.
     */
    @GetMapping("/search")
    public ResponseEntity<List<ProductoDTO>> search(@RequestParam String nombre) {
        log.info("GET /api/v1/productos/search?nombre={}", nombre);
        return ResponseEntity.ok(service.searchByNombre(nombre));
    }
}
```

### 11. Exception Handling

#### ⚠️ ELEMENTO CRÍTICO: GlobalExceptionHandler

**Este componente es OBLIGATORIO para manejo correcto de errores.**

**Problema que resuelve:**
- Sin este handler, los stack traces completos se envían al cliente (riesgo de seguridad)
- Los mensajes de error no son consistentes
- Difícil debugging en producción

**Primero, crear las excepciones personalizadas:**

```java
package com.uda.nombreproyecto.exception;

/**
 * Excepción lanzada cuando no se encuentra un recurso.
 */
public class ResourceNotFoundException extends RuntimeException {
    
    public ResourceNotFoundException(String message) {
        super(message);
    }
    
    public ResourceNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

```java
package com.uda.nombreproyecto.exception;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;

/**
 * Manejador global de excepciones.
 * Captura y formatea las excepciones de la aplicación.
 */
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    /**
     * Maneja excepciones de recurso no encontrado.
     */
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
    
    /**
     * Maneja excepciones de validación.
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {
        log.error("Error de validación: {}", ex.getMessage());
        
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
    
    /**
     * Maneja excepciones genéricas.
     */
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        log.error("Error interno del servidor: {}", ex.getMessage(), ex);
        
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

```java
package com.uda.nombreproyecto.exception;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;
import java.util.Map;

/**
 * Estructura de respuesta para errores.
 */
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ErrorResponse {
    private LocalDateTime timestamp;
    private int status;
    private String error;
    private String message;
    private Map<String, String> validationErrors;
}
```

---

## Frontend - Configuración Esencial

### 1. package.json

```json
{
  "name": "proyecto-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
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
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.0",
    "vite": "^5.1.0"
  }
}
```

### 2. vite.config.js

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  }
})
```

### 3. api.js (Configuración Axios)

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8080/nombre-proyecto/api/v1',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Interceptor para agregar token
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor para manejar errores
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### 4. Servicio Básico

```javascript
import api from './api';

const productoService = {
  getAll: async () => {
    const response = await api.get('/productos');
    return response.data;
  },

  getById: async (id) => {
    const response = await api.get(`/productos/${id}`);
    return response.data;
  },

  create: async (data) => {
    const response = await api.post('/productos', data);
    return response.data;
  },

  update: async (id, data) => {
    const response = await api.put(`/productos/${id}`, data);
    return response.data;
  },

  delete: async (id) => {
    await api.delete(`/productos/${id}`);
  },

  search: async (nombre) => {
    const response = await api.get('/productos/search', {
      params: { nombre }
    });
    return response.data;
  },
};

export default productoService;
```

### 5. main.jsx

```javascript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

### 6. App.jsx

```javascript
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import { ThemeProvider } from '@mui/material/styles';
import { CssBaseline } from '@mui/material';
import theme from './styles/theme';
import ProductoList from './pages/ProductoList';
import ProductoForm from './pages/ProductoForm';
import Home from './pages/Home';

function App() {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <Router>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/productos" element={<ProductoList />} />
          <Route path="/productos/new" element={<ProductoForm />} />
          <Route path="/productos/:id/edit" element={<ProductoForm />} />
        </Routes>
      </Router>
    </ThemeProvider>
  );
}

export default App;
```

### 7. theme.js

```javascript
import { createTheme } from '@mui/material/styles';

const theme = createTheme({
  palette: {
    primary: {
      main: '#1976d2',
    },
    secondary: {
      main: '#dc004e',
    },
  },
  typography: {
    fontFamily: 'Roboto, Arial, sans-serif',
  },
});

export default theme;
```

### 8. ProductoList.jsx

```javascript
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import {
  Box, Button, Table, TableBody, TableCell, TableContainer,
  TableHead, TableRow, Paper, IconButton, CircularProgress,
  Container, Typography, TextField
} from '@mui/material';
import { Add, Edit, Delete, Search } from '@mui/icons-material';
import productoService from '../services/productoService';

function ProductoList() {
  const [productos, setProductos] = useState([]);
  const [loading, setLoading] = useState(true);
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
      alert('Error al cargar los productos');
    } finally {
      setLoading(false);
    }
  };

  const handleSearch = async () => {
    if (!searchTerm.trim()) {
      loadProductos();
      return;
    }
    
    try {
      setLoading(true);
      const data = await productoService.search(searchTerm);
      setProductos(data);
    } catch (error) {
      console.error('Error en la búsqueda:', error);
      alert('Error al buscar productos');
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
        <CircularProgress />
      </Box>
    );
  }

  return (
    <Container maxWidth="lg" sx={{ mt: 4 }}>
      <Box display="flex" justifyContent="space-between" alignItems="center" mb={3}>
        <Typography variant="h4" component="h1">
          Productos
        </Typography>
        <Button
          variant="contained"
          startIcon={<Add />}
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
          startIcon={<Search />}
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
              <TableCell>Stock</TableCell>
              <TableCell>Categoría</TableCell>
              <TableCell align="right">Acciones</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {productos.length === 0 ? (
              <TableRow>
                <TableCell colSpan={7} align="center">
                  No hay productos disponibles
                </TableCell>
              </TableRow>
            ) : (
              productos.map((producto) => (
                <TableRow key={producto.id}>
                  <TableCell>{producto.id}</TableCell>
                  <TableCell>{producto.nombre}</TableCell>
                  <TableCell>{producto.descripcion}</TableCell>
                  <TableCell>${producto.precio}</TableCell>
                  <TableCell>{producto.stock}</TableCell>
                  <TableCell>{producto.categoria}</TableCell>
                  <TableCell align="right">
                    <IconButton
                      color="primary"
                      onClick={() => navigate(`/productos/${producto.id}/edit`)}
                    >
                      <Edit />
                    </IconButton>
                    <IconButton
                      color="error"
                      onClick={() => handleDelete(producto.id)}
                    >
                      <Delete />
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

### 9. ProductoForm.jsx

```javascript
import { useState, useEffect } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import { useForm, Controller } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';
import {
  Box, Button, Paper, TextField, Container, Typography, Grid
} from '@mui/material';
import { Save, Cancel } from '@mui/icons-material';
import productoService from '../services/productoService';

const schema = yup.object({
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
    .min(0, 'El stock no puede ser negativo')
    .integer('El stock debe ser un número entero')
    .typeError('Ingrese un stock válido'),
  categoria: yup
    .string()
    .max(50, 'La categoría no puede exceder 50 caracteres'),
}).required();

function ProductoForm() {
  const { id } = useParams();
  const navigate = useNavigate();
  const [loading, setLoading] = useState(false);
  const isEdit = Boolean(id);

  const {
    control,
    handleSubmit,
    reset,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(schema),
    defaultValues: {
      nombre: '',
      descripcion: '',
      precio: '',
      stock: 0,
      categoria: '',
    },
  });

  useEffect(() => {
    if (isEdit) {
      loadProducto();
    }
  }, [id]);

  const loadProducto = async () => {
    try {
      setLoading(true);
      const data = await productoService.getById(id);
      reset(data);
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
      navigate('/productos');
    } catch (error) {
      console.error('Error al guardar:', error);
      alert('Error al guardar el producto');
    } finally {
      setLoading(false);
    }
  };

  return (
    <Container maxWidth="md" sx={{ mt: 4 }}>
      <Typography variant="h4" component="h1" mb={3}>
        {isEdit ? 'Editar Producto' : 'Nuevo Producto'}
      </Typography>

      <Paper sx={{ p: 3 }}>
        <form onSubmit={handleSubmit(onSubmit)}>
          <Grid container spacing={3}>
            <Grid item xs={12}>
              <Controller
                name="nombre"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}
                    label="Nombre"
                    fullWidth
                    required
                    error={!!errors.nombre}
                    helperText={errors.nombre?.message}
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

            <Grid item xs={12} sm={4}>
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

            <Grid item xs={12} sm={4}>
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

            <Grid item xs={12} sm={4}>
              <Controller
                name="categoria"
                control={control}
                render={({ field }) => (
                  <TextField
                    {...field}
                    label="Categoría"
                    fullWidth
                    error={!!errors.categoria}
                    helperText={errors.categoria?.message}
                  />
                )}
              />
            </Grid>

            <Grid item xs={12}>
              <Box display="flex" gap={2} justifyContent="flex-end">
                <Button
                  variant="outlined"
                  startIcon={<Cancel />}
                  onClick={() => navigate('/productos')}
                  disabled={loading}
                >
                  Cancelar
                </Button>
                <Button
                  type="submit"
                  variant="contained"
                  startIcon={<Save />}
                  disabled={loading}
                >
                  {loading ? 'Guardando...' : 'Guardar'}
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

### 10. Home.jsx

```javascript
import { Container, Typography, Box, Button, Grid, Card, CardContent, CardActions } from '@mui/material';
import { useNavigate } from 'react-router-dom';
import { Inventory, Add, Search } from '@mui/icons-material';

function Home() {
  const navigate = useNavigate();

  return (
    <Container maxWidth="lg" sx={{ mt: 8, mb: 4 }}>
      <Box textAlign="center" mb={6}>
        <Typography variant="h2" component="h1" gutterBottom>
          Sistema de Gestión de Productos
        </Typography>
        <Typography variant="h5" color="text.secondary" paragraph>
          Aplicación UDA - Spring Boot + React + Oracle
        </Typography>
      </Box>

      <Grid container spacing={4}>
        <Grid item xs={12} md={4}>
          <Card sx={{ height: '100%', display: 'flex', flexDirection: 'column' }}>
            <CardContent sx={{ flexGrow: 1, textAlign: 'center' }}>
              <Inventory sx={{ fontSize: 60, color: 'primary.main', mb: 2 }} />
              <Typography variant="h5" component="h2" gutterBottom>
                Ver Productos
              </Typography>
              <Typography variant="body2" color="text.secondary">
                Consulta el catálogo completo de productos disponibles en el sistema
              </Typography>
            </CardContent>
            <CardActions sx={{ justifyContent: 'center', pb: 2 }}>
              <Button 
                variant="contained" 
                startIcon={<Search />}
                onClick={() => navigate('/productos')}
              >
                Ver Listado
              </Button>
            </CardActions>
          </Card>
        </Grid>

        <Grid item xs={12} md={4}>
          <Card sx={{ height: '100%', display: 'flex', flexDirection: 'column' }}>
            <CardContent sx={{ flexGrow: 1, textAlign: 'center' }}>
              <Add sx={{ fontSize: 60, color: 'primary.main', mb: 2 }} />
              <Typography variant="h5" component="h2" gutterBottom>
                Nuevo Producto
              </Typography>
              <Typography variant="body2" color="text.secondary">
                Agrega nuevos productos al catálogo del sistema
              </Typography>
            </CardContent>
            <CardActions sx={{ justifyContent: 'center', pb: 2 }}>
              <Button 
                variant="contained" 
                startIcon={<Add />}
                onClick={() => navigate('/productos/new')}
              >
                Crear Producto
              </Button>
            </CardActions>
          </Card>
        </Grid>

        <Grid item xs={12} md={4}>
          <Card sx={{ height: '100%', display: 'flex', flexDirection: 'column' }}>
            <CardContent sx={{ flexGrow: 1, textAlign: 'center' }}>
              <Search sx={{ fontSize: 60, color: 'primary.main', mb: 2 }} />
              <Typography variant="h5" component="h2" gutterBottom>
                Buscar
              </Typography>
              <Typography variant="body2" color="text.secondary">
                Busca productos específicos por nombre o categoría
              </Typography>
            </CardContent>
            <CardActions sx={{ justifyContent: 'center', pb: 2 }}>
              <Button 
                variant="outlined"
                onClick={() => navigate('/productos')}
              >
                Ir a Búsqueda
              </Button>
            </CardActions>
          </Card>
        </Grid>
      </Grid>

      <Box mt={8} textAlign="center">
        <Typography variant="body2" color="text.secondary">
          Desarrollado con Spring Boot 3.5.0 + React 18 + Oracle Database
        </Typography>
      </Box>
    </Container>
  );
}

export default Home;
```

## Base de Datos

### Script SQL Completo

```sql
-- ============================================
-- Script de creación de base de datos
-- Proyecto: Sistema de Gestión de Productos
-- Base de datos: Oracle
-- ============================================

-- Crear secuencia para PRODUCTOS
CREATE SEQUENCE PRODUCTO_SEQ
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

-- Crear tabla PRODUCTOS
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) NOT NULL,
    NOMBRE VARCHAR2(100) NOT NULL,
    DESCRIPCION VARCHAR2(500),
    PRECIO NUMBER(10,2) NOT NULL,
    STOCK NUMBER(10) DEFAULT 0,
    CATEGORIA VARCHAR2(50),
    ACTIVO NUMBER(1) DEFAULT 1 NOT NULL,
    CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
    UPDATED_AT TIMESTAMP,
    CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID),
    CONSTRAINT CK_PRODUCTOS_ACTIVO CHECK (ACTIVO IN (0, 1)),
    CONSTRAINT CK_PRODUCTOS_PRECIO CHECK (PRECIO > 0),
    CONSTRAINT CK_PRODUCTOS_STOCK CHECK (STOCK >= 0)
);

-- Crear índices
CREATE INDEX IDX_PRODUCTOS_NOMBRE ON PRODUCTOS(NOMBRE);
CREATE INDEX IDX_PRODUCTOS_CATEGORIA ON PRODUCTOS(CATEGORIA);
CREATE INDEX IDX_PRODUCTOS_ACTIVO ON PRODUCTOS(ACTIVO);

-- Comentarios en tabla y columnas
COMMENT ON TABLE PRODUCTOS IS 'Tabla de productos del sistema';
COMMENT ON COLUMN PRODUCTOS.ID IS 'Identificador único del producto';
COMMENT ON COLUMN PRODUCTOS.NOMBRE IS 'Nombre del producto';
COMMENT ON COLUMN PRODUCTOS.DESCRIPCION IS 'Descripción detallada del producto';
COMMENT ON COLUMN PRODUCTOS.PRECIO IS 'Precio del producto';
COMMENT ON COLUMN PRODUCTOS.STOCK IS 'Cantidad disponible en inventario';
COMMENT ON COLUMN PRODUCTOS.CATEGORIA IS 'Categoría del producto';
COMMENT ON COLUMN PRODUCTOS.ACTIVO IS 'Indica si el producto está activo (1) o inactivo (0)';
COMMENT ON COLUMN PRODUCTOS.CREATED_AT IS 'Fecha de creación del registro';
COMMENT ON COLUMN PRODUCTOS.UPDATED_AT IS 'Fecha de última actualización';

-- Insertar datos de ejemplo
INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, CATEGORIA, ACTIVO, CREATED_AT)
VALUES (PRODUCTO_SEQ.NEXTVAL, 'Laptop Dell XPS 15', 'Laptop de alto rendimiento con procesador Intel i7', 1299.99, 10, 'Electrónica', 1, CURRENT_TIMESTAMP);

INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, CATEGORIA, ACTIVO, CREATED_AT)
VALUES (PRODUCTO_SEQ.NEXTVAL, 'Mouse Logitech MX Master 3', 'Mouse inalámbrico ergonómico', 99.99, 25, 'Accesorios', 1, CURRENT_TIMESTAMP);

INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, CATEGORIA, ACTIVO, CREATED_AT)
VALUES (PRODUCTO_SEQ.NEXTVAL, 'Teclado Mecánico Keychron K2', 'Teclado mecánico compacto', 89.99, 15, 'Accesorios', 1, CURRENT_TIMESTAMP);

COMMIT;
```

## Seguridad Básica

Esta sección contiene las configuraciones de seguridad y serialización de la aplicación.

### Índice de Configuraciones

1. [SecurityConfig.java](#securityconfigjava) - Seguridad y autenticación
2. [WebConfig.java](#webconfigjava) - Configuración web
3. [JacksonConfig.java](#jacksonconfigjava) - Serialización JSON

---

### SecurityConfig.java

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/config/SecurityConfig.java`

Configuración de Spring Security con autenticación básica y CORS.

```java
package com.uda.[nombre-proyecto].config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;

import java.util.Arrays;
import java.util.List;

/**
 * Configuración de seguridad de la aplicación.
 * Configura CORS, autenticación y autorización.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            .csrf(csrf -> csrf.disable())
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/public/**").permitAll()
                .requestMatchers("/api/v1/auth/**").permitAll()
                .requestMatchers("/actuator/health").permitAll()
                .requestMatchers("/h2-console/**").permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic(basic -> {});
        
        // Permitir frames para H2 Console
        http.headers(headers -> headers.frameOptions().sameOrigin());
        
        return http.build();
    }

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(List.of("http://localhost:5173", "http://localhost:3000"));
        configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE", "OPTIONS"));
        configuration.setAllowedHeaders(List.of("*"));
        configuration.setAllowCredentials(true);
        
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

### WebConfig.java

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/config/WebConfig.java`

Configuración adicional de Spring MVC.

```java
package com.uda.[nombre-proyecto].config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * Configuración Web de la aplicación.
 * 
 * @author UDA
 * @version 1.0.0
 */
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
    }
}
```

---

### JacksonConfig.java

**Ubicación:** `backend/src/main/java/com/uda/[proyecto]/config/JacksonConfig.java`

Configuración de Jackson para serialización correcta de fechas y otros tipos de Java 8+.

```java
package com.uda.[nombre-proyecto].config;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.json.Jackson2ObjectMapperBuilder;

/**
 * Configuración de Jackson para serialización correcta de fechas.
 * 
 * Problema sin esta configuración:
 * LocalDateTime se serializa como array: [2025, 11, 3, 9, 30, 31, 191998000]
 * 
 * Solución con esta configuración:
 * LocalDateTime se serializa como String ISO-8601: "2025-11-03T09:30:31.191998"
 * 
 * @author UDA
 * @version 1.0.0
 */
@Configuration
public class JacksonConfig {

    /**
     * Configura ObjectMapper para serialización correcta de fechas.
     * 
     * @return ObjectMapper configurado
     */
    @Bean
    public ObjectMapper objectMapper() {
        return Jackson2ObjectMapperBuilder.json()
            // Registrar módulo para Java 8 Date/Time API
            .modules(new JavaTimeModule())
            // Deshabilitar serialización de fechas como timestamps
            .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
            .build();
    }
}
```

**Características:**

✅ **Serialización de LocalDateTime:** Convierte a formato ISO-8601  
✅ **Serialización de LocalDate:** Formato `yyyy-MM-dd`  
✅ **Serialización de LocalTime:** Formato `HH:mm:ss`  
✅ **Deserialización:** Acepta múltiples formatos de entrada  

**Ejemplo de Salida:**

```json
{
  "id": 1,
  "titulo": "Cien años de soledad",
  "createdAt": "2025-11-03T09:30:31.191998",
  "updatedAt": null
}
```

**Alternativa con Anotaciones en DTO:**

Si necesitas formatos personalizados por campo:

```java
import com.fasterxml.jackson.annotation.JsonFormat;

public class LibroDTO {
    
    // Formato personalizado
    @JsonFormat(pattern = "dd/MM/yyyy HH:mm:ss")
    private LocalDateTime createdAt;
    
    // Solo fecha
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate fechaPublicacion;
    
    // Solo hora
    @JsonFormat(pattern = "HH:mm")
    private LocalTime horaApertura;
}
```

**Dependencia Requerida:**

Esta configuración requiere la dependencia `jackson-datatype-jsr310` que ya está incluida en `spring-boot-starter-web`:

```xml
<!-- Ya incluida en spring-boot-starter-web -->
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
</dependency>
```

---

## Despliegue

### 1. Preparación del Entorno

#### Requisitos Previos
- **Java 21 JDK** instalado
- **Apache Tomcat 10.x** instalado
- **Oracle Database 19c+** configurado y accesible
- **Node.js 18+** y npm instalados (para build del frontend)
- **Maven 3.9+** instalado

#### Variables de Entorno Necesarias

Crear archivo `.env` en la raíz del proyecto backend:

```bash
# Base de datos
DB_HOST=oracle-server.example.com
DB_PORT=1521
DB_SID=PROD
DB_USERNAME=uda_prod
DB_PASSWORD=SecurePassword123!

# JWT
JWT_SECRET=YourSuperSecretKeyMinimum256BitsForProduction2024!
JWT_EXPIRATION=86400000

# Servidor
SERVER_PORT=8080
```

### 2. Build del Backend

#### Compilar el proyecto

```bash
cd backend
mvn clean package -DskipTests
```

Esto genera: `target/nombre-proyecto.war`

#### Verificar el WAR generado

```bash
ls -lh target/nombre-proyecto.war
```

#### Configuración para producción

Crear `application-prod.yml` si no existe:

```yaml
spring:
  application:
    name: nombre-proyecto
    
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST}:${DB_PORT}:${DB_SID}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    driver-class-name: oracle.jdbc.OracleDriver
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
  
  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false

server:
  port: ${SERVER_PORT:8080}
  servlet:
    context-path: /${spring.application.name}
  compression:
    enabled: true

logging:
  level:
    root: WARN
    com.uda: INFO
  file:
    name: /var/log/uda/nombre-proyecto.log
```

### 3. Despliegue en Tomcat

#### Opción A: Despliegue Manual

```bash
# Detener Tomcat
$TOMCAT_HOME/bin/shutdown.sh

# Copiar WAR
cp target/nombre-proyecto.war $TOMCAT_HOME/webapps/

# Configurar variables de entorno en Tomcat
# Editar $TOMCAT_HOME/bin/setenv.sh (crear si no existe)
```

Contenido de `setenv.sh`:

```bash
#!/bin/bash

export JAVA_OPTS="$JAVA_OPTS -Xms512m -Xmx2048m"
export JAVA_OPTS="$JAVA_OPTS -Dspring.profiles.active=prod"
export JAVA_OPTS="$JAVA_OPTS -DDB_HOST=oracle-server.example.com"
export JAVA_OPTS="$JAVA_OPTS -DDB_PORT=1521"
export JAVA_OPTS="$JAVA_OPTS -DDB_SID=PROD"
export JAVA_OPTS="$JAVA_OPTS -DDB_USERNAME=uda_prod"
export JAVA_OPTS="$JAVA_OPTS -DDB_PASSWORD=SecurePassword123!"
export JAVA_OPTS="$JAVA_OPTS -DJWT_SECRET=YourSuperSecretKey"
```

```bash
# Dar permisos de ejecución
chmod +x $TOMCAT_HOME/bin/setenv.sh

# Iniciar Tomcat
$TOMCAT_HOME/bin/startup.sh

# Verificar logs
tail -f $TOMCAT_HOME/logs/catalina.out
```

#### Opción B: Despliegue con Manager de Tomcat

1. Acceder a: `http://servidor:8080/manager/html`
2. En la sección "WAR file to deploy", seleccionar el archivo WAR
3. Click en "Deploy"
4. Verificar que la aplicación aparece en la lista

#### Verificar despliegue

```bash
# Verificar que la aplicación está desplegada
curl http://localhost:8080/nombre-proyecto/api/v1/productos

# Verificar health (si tienes actuator)
curl http://localhost:8080/nombre-proyecto/actuator/health
```

### 4. Build del Frontend

#### Configurar variables de entorno

Crear archivo `.env.production`:

```bash
VITE_API_URL=http://servidor-produccion.example.com:8080/nombre-proyecto/api/v1
```

#### Compilar para producción

```bash
cd frontend
npm install
npm run build
```

Esto genera la carpeta `dist/` con los archivos estáticos optimizados.

#### Verificar build

```bash
ls -lh dist/
# Debe contener: index.html, assets/, etc.
```

### 5. Despliegue del Frontend

#### Opción A: Servir desde Tomcat

```bash
# Crear directorio para el frontend
mkdir -p $TOMCAT_HOME/webapps/nombre-proyecto-frontend

# Copiar archivos build
cp -r dist/* $TOMCAT_HOME/webapps/nombre-proyecto-frontend/

# Acceder a: http://servidor:8080/nombre-proyecto-frontend
```

#### Opción B: Servir con Nginx (Recomendado)

Instalar Nginx:

```bash
sudo apt-get update
sudo apt-get install nginx
```

Configurar Nginx (`/etc/nginx/sites-available/nombre-proyecto`):

```nginx
server {
    listen 80;
    server_name tu-dominio.com;
    
    root /var/www/nombre-proyecto;
    index index.html;
    
    # Compresión Gzip
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript 
               application/x-javascript application/xml+rss application/json;
    
    # Cache para assets estáticos
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Manejo de rutas de React Router
    location / {
        try_files $uri $uri/ /index.html;
        add_header Cache-Control "no-cache";
    }
    
    # Proxy para API backend
    location /api {
        proxy_pass http://localhost:8080/nombre-proyecto/api;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

Activar configuración y desplegar:

```bash
# Copiar archivos del frontend
sudo mkdir -p /var/www/nombre-proyecto
sudo cp -r dist/* /var/www/nombre-proyecto/

# Activar sitio
sudo ln -s /etc/nginx/sites-available/nombre-proyecto /etc/nginx/sites-enabled/

# Verificar configuración
sudo nginx -t

# Recargar Nginx
sudo systemctl reload nginx
```

#### Opción C: Servir con Apache HTTP Server

Configurar Apache (`/etc/apache2/sites-available/nombre-proyecto.conf`):

```apache
<VirtualHost *:80>
    ServerName tu-dominio.com
    DocumentRoot /var/www/nombre-proyecto
    
    <Directory /var/www/nombre-proyecto>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
        
        # Rewrite para React Router
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
    
    # Proxy para API
    ProxyPass /api http://localhost:8080/nombre-proyecto/api
    ProxyPassReverse /api http://localhost:8080/nombre-proyecto/api
    
    ErrorLog ${APACHE_LOG_DIR}/nombre-proyecto-error.log
    CustomLog ${APACHE_LOG_DIR}/nombre-proyecto-access.log combined
</VirtualHost>
```

Activar y desplegar:

```bash
# Habilitar módulos necesarios
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_http

# Copiar archivos
sudo cp -r dist/* /var/www/nombre-proyecto/

# Activar sitio
sudo a2ensite nombre-proyecto

# Recargar Apache
sudo systemctl reload apache2
```

### 6. Configuración de Base de Datos en Producción

#### Ejecutar scripts SQL

```bash
# Conectar a Oracle
sqlplus uda_prod/SecurePassword123!@oracle-server.example.com:1521/PROD

# Ejecutar script de creación
SQL> @database/schema/01_create_tables.sql
SQL> @database/schema/02_create_sequences.sql
SQL> @database/schema/03_create_indexes.sql
SQL> @database/schema/04_create_constraints.sql

# Ejecutar datos iniciales (opcional)
SQL> @database/data/01_insert_roles.sql

SQL> EXIT;
```

#### Verificar tablas creadas

```sql
SELECT table_name FROM user_tables WHERE table_name LIKE 'PRODUCTOS%';
SELECT sequence_name FROM user_sequences;
```

### 7. Configuración SSL/HTTPS (Producción)

#### Con Nginx y Let's Encrypt

```bash
# Instalar Certbot
sudo apt-get install certbot python3-certbot-nginx

# Obtener certificado SSL
sudo certbot --nginx -d tu-dominio.com

# Renovación automática (ya configurada por certbot)
sudo certbot renew --dry-run
```

Nginx configurará automáticamente HTTPS. Verificar en `/etc/nginx/sites-available/nombre-proyecto`.

#### Con Apache y Let's Encrypt

```bash
# Instalar Certbot
sudo apt-get install certbot python3-certbot-apache

# Obtener certificado
sudo certbot --apache -d tu-dominio.com
```

### 8. Monitoreo y Logs

#### Logs del Backend (Tomcat)

```bash
# Logs de Tomcat
tail -f $TOMCAT_HOME/logs/catalina.out

# Logs de la aplicación (si configuraste logging.file)
tail -f /var/log/uda/nombre-proyecto.log
```

#### Logs del Frontend (Nginx)

```bash
# Access logs
tail -f /var/log/nginx/access.log

# Error logs
tail -f /var/log/nginx/error.log
```

#### Monitoreo de la aplicación

```bash
# Verificar que Tomcat está corriendo
ps aux | grep tomcat

# Verificar puertos
netstat -tulpn | grep 8080

# Verificar uso de memoria
free -h

# Verificar uso de disco
df -h
```

### 9. Backup y Restauración

#### Backup de Base de Datos

```bash
# Backup con expdp (Oracle Data Pump)
expdp uda_prod/SecurePassword123!@PROD \
  directory=DATA_PUMP_DIR \
  dumpfile=nombre-proyecto_$(date +%Y%m%d).dmp \
  logfile=nombre-proyecto_$(date +%Y%m%d).log \
  schemas=uda_prod
```

#### Backup de la aplicación

```bash
# Backup del WAR
cp $TOMCAT_HOME/webapps/nombre-proyecto.war \
   /backup/nombre-proyecto_$(date +%Y%m%d).war

# Backup del frontend
tar -czf /backup/frontend_$(date +%Y%m%d).tar.gz \
   /var/www/nombre-proyecto
```

#### Restauración

```bash
# Restaurar base de datos
impdp uda_prod/SecurePassword123!@PROD \
  directory=DATA_PUMP_DIR \
  dumpfile=nombre-proyecto_20241030.dmp \
  logfile=restore_$(date +%Y%m%d).log

# Restaurar aplicación
cp /backup/nombre-proyecto_20241030.war $TOMCAT_HOME/webapps/
$TOMCAT_HOME/bin/shutdown.sh
$TOMCAT_HOME/bin/startup.sh
```

### 10. Troubleshooting

#### Problema: Aplicación no inicia

```bash
# Verificar logs de Tomcat
tail -100 $TOMCAT_HOME/logs/catalina.out

# Verificar que el puerto no está en uso
lsof -i :8080

# Verificar variables de entorno
echo $JAVA_OPTS
```

#### Problema: Error de conexión a base de datos

```bash
# Verificar conectividad
tnsping PROD

# Probar conexión manual
sqlplus uda_prod/password@PROD

# Verificar configuración en application-prod.yml
```

#### Problema: Frontend no carga

```bash
# Verificar que Nginx está corriendo
sudo systemctl status nginx

# Verificar configuración de Nginx
sudo nginx -t

# Verificar permisos de archivos
ls -la /var/www/nombre-proyecto
```

#### Problema: CORS errors

Verificar en `SecurityConfig.java`:

```java
configuration.setAllowedOrigins(List.of(
    "http://tu-dominio.com",
    "https://tu-dominio.com"
));
```

### 11. Actualización de la Aplicación

#### Actualizar Backend

```bash
# 1. Compilar nueva versión
cd backend
mvn clean package -DskipTests

# 2. Detener Tomcat
$TOMCAT_HOME/bin/shutdown.sh

# 3. Backup de la versión actual
cp $TOMCAT_HOME/webapps/nombre-proyecto.war \
   /backup/nombre-proyecto_$(date +%Y%m%d_%H%M%S).war

# 4. Eliminar despliegue anterior
rm -rf $TOMCAT_HOME/webapps/nombre-proyecto
rm -rf $TOMCAT_HOME/webapps/nombre-proyecto.war

# 5. Copiar nueva versión
cp target/nombre-proyecto.war $TOMCAT_HOME/webapps/

# 6. Iniciar Tomcat
$TOMCAT_HOME/bin/startup.sh

# 7. Verificar logs
tail -f $TOMCAT_HOME/logs/catalina.out
```

#### Actualizar Frontend

```bash
# 1. Compilar nueva versión
cd frontend
npm run build

# 2. Backup de la versión actual
sudo tar -czf /backup/frontend_$(date +%Y%m%d_%H%M%S).tar.gz \
     /var/www/nombre-proyecto

# 3. Actualizar archivos
sudo rm -rf /var/www/nombre-proyecto/*
sudo cp -r dist/* /var/www/nombre-proyecto/

# 4. Recargar Nginx
sudo systemctl reload nginx
```

### 12. Script de Despliegue Automatizado

Crear `deploy.sh`:

```bash
#!/bin/bash

set -e

echo "🚀 Iniciando despliegue de nombre-proyecto..."

# Variables
TOMCAT_HOME=/opt/tomcat
APP_NAME=nombre-proyecto
BACKUP_DIR=/backup
FRONTEND_DIR=/var/www/$APP_NAME

# Colores
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m'

# Función para imprimir mensajes
print_message() {
    echo -e "${GREEN}[$(date +'%Y-%m-%d %H:%M:%S')]${NC} $1"
}

print_error() {
    echo -e "${RED}[$(date +'%Y-%m-%d %H:%M:%S')] ERROR:${NC} $1"
}

# 1. Build Backend
print_message "Compilando backend..."
cd backend
mvn clean package -DskipTests || {
    print_error "Falló la compilación del backend"
    exit 1
}

# 2. Build Frontend
print_message "Compilando frontend..."
cd ../frontend
npm install
npm run build || {
    print_error "Falló la compilación del frontend"
    exit 1
}

# 3. Backup
print_message "Creando backup..."
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
mkdir -p $BACKUP_DIR

if [ -f "$TOMCAT_HOME/webapps/$APP_NAME.war" ]; then
    cp $TOMCAT_HOME/webapps/$APP_NAME.war \
       $BACKUP_DIR/${APP_NAME}_${TIMESTAMP}.war
fi

if [ -d "$FRONTEND_DIR" ]; then
    tar -czf $BACKUP_DIR/frontend_${TIMESTAMP}.tar.gz $FRONTEND_DIR
fi

# 4. Detener Tomcat
print_message "Deteniendo Tomcat..."
$TOMCAT_HOME/bin/shutdown.sh || true
sleep 5

# 5. Desplegar Backend
print_message "Desplegando backend..."
rm -rf $TOMCAT_HOME/webapps/$APP_NAME
rm -f $TOMCAT_HOME/webapps/$APP_NAME.war
cp ../backend/target/$APP_NAME.war $TOMCAT_HOME/webapps/

# 6. Desplegar Frontend
print_message "Desplegando frontend..."
sudo rm -rf $FRONTEND_DIR/*
sudo cp -r dist/* $FRONTEND_DIR/

# 7. Iniciar Tomcat
print_message "Iniciando Tomcat..."
$TOMCAT_HOME/bin/startup.sh

# 8. Verificar despliegue
print_message "Esperando que la aplicación inicie..."
sleep 15

if curl -f http://localhost:8080/$APP_NAME/api/v1/productos > /dev/null 2>&1; then
    print_message "✅ Despliegue completado exitosamente"
else
    print_error "La aplicación no responde. Verificar logs."
    tail -50 $TOMCAT_HOME/logs/catalina.out
    exit 1
fi

# 9. Recargar Nginx
print_message "Recargando Nginx..."
sudo systemctl reload nginx

print_message "🎉 Despliegue finalizado"
```

Dar permisos y ejecutar:

```bash
chmod +x deploy.sh
./deploy.sh
```

---

## Checklist de Generación

### ⚠️ Elementos Críticos (OBLIGATORIO)

**Verificar ANTES de continuar:**

#### Backend Crítico (5 elementos)
- [ ] ⚠️ **JacksonConfig.java** existe en `config/` y está completo
- [ ] ⚠️ **application.yml** existe con H2 configurado
- [ ] ⚠️ **application-prod.yml** existe con Oracle configurado
- [ ] ⚠️ **Application.java** extiende SpringBootServletInitializer
- [ ] ⚠️ **pom.xml** tiene:
  - [ ] `<packaging>war</packaging>`
  - [ ] Tomcat con `scope=provided`
  - [ ] Dependencia H2
  - [ ] `<finalName>` sin versión

#### Manejo de Errores (1 elemento)
- [ ] ⚠️ **GlobalExceptionHandler** existe y maneja:
  - [ ] ResourceNotFoundException (404)
  - [ ] MethodArgumentNotValidException (400)
  - [ ] Exception genérica (500)

#### Validaciones (2 elementos)
- [ ] ⚠️ **Validaciones en DTOs**: Todos los DTOs tienen Bean Validation
- [ ] ⚠️ **validationSchemas.js**: Existe con Yup (si hay formularios)

**SI FALTA ALGUNO: DETENER Y CORREGIR ANTES DE CONTINUAR**

### Backend Mínimo
- [ ] `pom.xml` con todas las dependencias y configuración de plugins
- [ ] `Application.java` extendiendo `SpringBootServletInitializer`
- [ ] `application.yml` con configuración completa de Oracle
- [ ] `SecurityConfig.java` y `WebConfig.java`
- [ ] Al menos 1 entidad completa con:
  - [ ] Entity con anotaciones JPA
  - [ ] DTO con validaciones Bean Validation
  - [ ] Mapper con MapStruct
  - [ ] Repository con métodos personalizados
  - [ ] Service interface e implementación
  - [ ] Controller con endpoints REST y @Valid
- [ ] `GlobalExceptionHandler` con manejo de errores
- [ ] `ResourceNotFoundException` y `ErrorResponse`

### Frontend Mínimo
- [ ] `package.json` con dependencias (incluyendo yup)
- [ ] `vite.config.js` configurado
- [ ] `theme.js` con tema de Material UI
- [ ] `api.js` con Axios configurado
- [ ] `validationSchemas.js` con Yup
- [ ] Al menos un servicio completo
- [ ] `App.jsx` con routing
- [ ] `Header.jsx`
- [ ] `Footer.jsx`
- [ ] Página de listado funcional
- [ ] Página de formulario funcional con validación Yup
- [ ] Archivos .env para diferentes entornos

### Base de Datos
- [ ] Script SQL de creación de tablas
- [ ] Secuencias para IDs
- [ ] Índices apropiados
- [ ] Constraints (PK, FK, UK, CK)
- [ ] Comentarios en tablas y columnas
- [ ] Datos de ejemplo (opcional)

### Documentación
- [ ] `README.md` con instrucciones de instalación
- [ ] Comentarios JavaDoc en clases Java
- [ ] Comentarios JSDoc en funciones JavaScript
- [ ] Variables de entorno documentadas en .env.example

### Despliegue
- [ ] Configuración para generar WAR
- [ ] Scripts de build
- [ ] Variables de entorno documentadas
- [ ] Perfiles de Spring (dev, prod)

---

### ✅ Verificación Final

**Ejecutar estos comandos para verificar:**

```bash
# Backend: Verificar que compila y genera WAR
cd backend
mvn clean package
ls -lh target/*.war  # Debe existir archivo .war

# Backend: Verificar que funciona con H2 (perfil dev por defecto)
mvn spring-boot:run
# Acceder a: http://localhost:8080/[nombre-proyecto]/h2-console

# Frontend: Verificar que compila
cd frontend
npm install
npm run build
ls -lh dist/  # Debe existir carpeta con archivos

# Frontend: Verificar que funciona
npm run dev
# Acceder a: http://localhost:5173
```

**Si algún comando falla, revisar los elementos críticos.**

---

## Comandos Rápidos

```bash
# Backend
cd backend
mvn clean install          # Compilar e instalar
mvn spring-boot:run        # Ejecutar en desarrollo
mvn clean package          # Generar WAR para producción
mvn test                   # Ejecutar tests

# Frontend
cd frontend
npm install                # Instalar dependencias
npm run dev                # Ejecutar en desarrollo (puerto 5173)
npm run build              # Compilar para producción
npm run preview            # Previsualizar build de producción

# Oracle (desde SQL*Plus)
sqlplus usuario/password@localhost:1521/ORCL
@script.sql                # Ejecutar script SQL
```

---

**Importante para IAs**: Este documento contiene las especificaciones completas y ejemplos de código funcional. Seguir **todas** las indicaciones al pie de la letra para garantizar consistencia y funcionalidad.
