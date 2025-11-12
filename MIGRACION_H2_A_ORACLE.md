# 🔄 Guía de Migración de H2 a Oracle Database

---

## 📋 Índice

1. [¿Por qué H2 para Desarrollo?](#por-qué-h2-para-desarrollo)
2. [Configuración Actual con H2](#configuración-actual-con-h2)
3. [Preparación para Oracle](#preparación-para-oracle)
4. [Migración Paso a Paso](#migración-paso-a-paso)
5. [Diferencias Importantes](#diferencias-importantes)
6. [Solución de Problemas](#solución-de-problemas)
7. [Checklist de Migración](#checklist-de-migración)

---

## 🎯 ¿Por qué H2 para Desarrollo?

### **Ventajas de H2**

| Característica | Beneficio |
|----------------|-----------|
| ✅ **Configuración Cero** | No requiere instalación de base de datos |
| ✅ **Velocidad** | Ejecución en memoria, ideal para desarrollo |
| ✅ **Portabilidad** | Funciona en cualquier sistema operativo |
| ✅ **Consola Web** | Interfaz gráfica incluida en `http://localhost:8080/h2-console` |
| ✅ **Datos de Prueba** | Fácil cargar y resetear datos de ejemplo |
| ✅ **Tests** | Perfecto para tests de integración |
| ✅ **Sin Dependencias** | No requiere servicios externos |

### **Cuándo Migrar a Oracle**

| Escenario | Razón |
|-----------|-------|
| ⚠️ **Desarrollo Avanzado** | Necesitas características específicas de Oracle |
| ⚠️ **Pre-Producción** | Probar en entorno similar a producción |
| ⚠️ **Producción** | **Siempre** usar Oracle en producción |
| ⚠️ **Integración Continua** | Tests en entorno real |

---

## 📊 Configuración Actual con H2

### **1. Dependencia en pom.xml**

```xml
<!-- H2 Database (desarrollo) -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

### **2. Configuración en application-dev.yml**

```yaml
spring:
  config:
    activate:
      on-profile: dev
  
  datasource:
    url: jdbc:h2:mem:${spring.application.name}
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
```

### **3. Acceso a H2 Console**

```
URL:       http://localhost:8080/[nombre-app]/h2-console
JDBC URL:  jdbc:h2:mem:[nombre-app]
Username:  sa
Password:  (vacío)
```

### **4. Datos de Ejemplo (data.sql)**

```sql
-- src/main/resources/data.sql
INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, ACTIVO, CREATED_AT)
VALUES (1, 'Producto 1', 'Descripción 1', 99.99, 10, true, CURRENT_TIMESTAMP);

INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, ACTIVO, CREATED_AT)
VALUES (2, 'Producto 2', 'Descripción 2', 149.99, 5, true, CURRENT_TIMESTAMP);
```

---

## 🔧 Preparación para Oracle

### **Opción Recomendada: Docker**

```bash
# Ejecutar Oracle XE en Docker
docker run -d \
  --name oracle-xe \
  -p 1521:1521 \
  -e ORACLE_PWD=OraclePassword123 \
  container-registry.oracle.com/database/express:21.3.0-xe

# Verificar que está listo
docker logs -f oracle-xe
# Esperar: "DATABASE IS READY TO USE!"
```

---

### **Crear Usuario y Schema**

**Conectar como SYSTEM:**

```bash
sqlplus system/OraclePassword123@localhost:1521/XE
```

**Ejecutar script:**

```sql
-- Crear usuario
CREATE USER uda_user IDENTIFIED BY uda_password;

-- Otorgar permisos básicos
GRANT CONNECT, RESOURCE TO uda_user;
GRANT CREATE SESSION TO uda_user;
GRANT CREATE TABLE TO uda_user;
GRANT CREATE SEQUENCE TO uda_user;
GRANT CREATE VIEW TO uda_user;

-- Otorgar espacio ilimitado (solo desarrollo)
GRANT UNLIMITED TABLESPACE TO uda_user;

-- Verificar creación
SELECT username, account_status, created 
FROM dba_users 
WHERE username = 'UDA_USER';
```

**Conectar como nuevo usuario:**

```bash
sqlplus uda_user/uda_password@localhost:1521/XE
```

---

## 🚀 Migración Paso a Paso

### **PASO 1: Actualizar pom.xml**

**Mantener H2 para desarrollo:**

```xml
<!-- H2 Database (desarrollo) -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Agregar Oracle para producción:**

```xml
<!-- Oracle JDBC Driver (producción) -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc11</artifactId>
    <version>23.3.0.23.09</version>
</dependency>

<!-- Opcional: Oracle UCP (Connection Pool) -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ucp</artifactId>
    <version>23.3.0.23.09</version>
</dependency>
```

---

### **PASO 2: Configurar Perfiles**

#### **application.yml (común)**

```yaml
spring:
  application:
    name: mi-aplicacion
  
  jpa:
    open-in-view: false
    properties:
      hibernate:
        format_sql: true
        use_sql_comments: true

server:
  port: 8080
  servlet:
    context-path: /${spring.application.name}
```

#### **application-dev.yml (H2)**

```yaml
spring:
  config:
    activate:
      on-profile: dev
  
  datasource:
    url: jdbc:h2:mem:${spring.application.name}
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

logging:
  level:
    root: INFO
    com.uda: DEBUG
    org.hibernate.SQL: DEBUG
```

#### **application-prod.yml (Oracle)**

```yaml
spring:
  config:
    activate:
      on-profile: prod
  
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST:localhost}:${DB_PORT:1521}:${DB_SID:XE}
    driver-class-name: oracle.jdbc.OracleDriver
    username: ${DB_USERNAME:uda_user}
    password: ${DB_PASSWORD:uda_password}
    hikari:
      maximum-pool-size: 10
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
  
  jpa:
    database-platform: org.hibernate.dialect.OracleDialect
    hibernate:
      ddl-auto: validate
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
    show-sql: false
    properties:
      hibernate:
        jdbc:
          batch_size: 20
        order_inserts: true
        order_updates: true

logging:
  level:
    root: WARN
    com.uda: INFO
    org.hibernate.SQL: WARN
```

---

### **PASO 3: Adaptar Entidades JPA**

#### **Configuración de Secuencias**

**H2 (auto-increment):**

```java
@Entity
@Table(name = "PRODUCTOS")
public class Producto {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
}
```

**Oracle (secuencias):**

```java
@Entity
@Table(name = "PRODUCTOS")
public class Producto {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "productos_seq")
    @SequenceGenerator(
        name = "productos_seq",
        sequenceName = "PRODUCTOS_SEQ",
        allocationSize = 1
    )
    @Column(name = "ID")
    private Long id;
    
    @Column(name = "NOMBRE", nullable = false, length = 200)
    private String nombre;
    
    @Column(name = "PRECIO", precision = 10, scale = 2)
    private BigDecimal precio;
    
    // Boolean en Oracle = NUMBER(1)
    @Column(name = "ACTIVO")
    private Boolean activo;
    
    @Column(name = "CREATED_AT")
    private LocalDateTime createdAt;
    
    @PrePersist
    protected void onCreate() {
        if (createdAt == null) {
            createdAt = LocalDateTime.now();
        }
        if (activo == null) {
            activo = true;
        }
    }
}
```

---

### **PASO 4: Crear Scripts SQL para Oracle**

#### **V1__initial_schema.sql**

```sql
-- Crear secuencia
CREATE SEQUENCE PRODUCTOS_SEQ
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

-- Crear tabla
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) NOT NULL,
    NOMBRE VARCHAR2(200) NOT NULL,
    DESCRIPCION VARCHAR2(1000),
    PRECIO NUMBER(10,2) NOT NULL,
    STOCK NUMBER(10) DEFAULT 0 NOT NULL,
    ACTIVO NUMBER(1) DEFAULT 1 NOT NULL,
    CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
    UPDATED_AT TIMESTAMP,
    CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID),
    CONSTRAINT CK_PRODUCTOS_ACTIVO CHECK (ACTIVO IN (0, 1)),
    CONSTRAINT CK_PRODUCTOS_PRECIO CHECK (PRECIO >= 0),
    CONSTRAINT CK_PRODUCTOS_STOCK CHECK (STOCK >= 0)
);

-- Crear índices
CREATE INDEX IDX_PRODUCTOS_NOMBRE ON PRODUCTOS(NOMBRE);
CREATE INDEX IDX_PRODUCTOS_ACTIVO ON PRODUCTOS(ACTIVO);
CREATE INDEX IDX_PRODUCTOS_CREATED ON PRODUCTOS(CREATED_AT);

-- Comentarios
COMMENT ON TABLE PRODUCTOS IS 'Tabla de productos del sistema';
COMMENT ON COLUMN PRODUCTOS.ID IS 'Identificador único del producto';
COMMENT ON COLUMN PRODUCTOS.NOMBRE IS 'Nombre del producto';
COMMENT ON COLUMN PRODUCTOS.DESCRIPCION IS 'Descripción detallada del producto';
COMMENT ON COLUMN PRODUCTOS.PRECIO IS 'Precio unitario del producto';
COMMENT ON COLUMN PRODUCTOS.STOCK IS 'Cantidad disponible en inventario';
COMMENT ON COLUMN PRODUCTOS.ACTIVO IS 'Estado del producto (1=activo, 0=inactivo)';
COMMENT ON COLUMN PRODUCTOS.CREATED_AT IS 'Fecha y hora de creación del registro';
COMMENT ON COLUMN PRODUCTOS.UPDATED_AT IS 'Fecha y hora de última actualización';
```

#### **V2__insert_data.sql**

```sql
-- Insertar datos de ejemplo
INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, ACTIVO, CREATED_AT)
VALUES (PRODUCTOS_SEQ.NEXTVAL, 'Producto 1', 'Descripción del producto 1', 99.99, 10, 1, CURRENT_TIMESTAMP);

INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, ACTIVO, CREATED_AT)
VALUES (PRODUCTOS_SEQ.NEXTVAL, 'Producto 2', 'Descripción del producto 2', 149.99, 5, 1, CURRENT_TIMESTAMP);

INSERT INTO PRODUCTOS (ID, NOMBRE, DESCRIPCION, PRECIO, STOCK, ACTIVO, CREATED_AT)
VALUES (PRODUCTOS_SEQ.NEXTVAL, 'Producto 3', 'Descripción del producto 3', 79.99, 15, 1, CURRENT_TIMESTAMP);

COMMIT;
```

---

### **PASO 5: Configurar Variables de Entorno**

#### **.env (desarrollo local)**

```env
# Oracle Database
DB_HOST=localhost
DB_PORT=1521
DB_SID=XE
DB_USERNAME=uda_user
DB_PASSWORD=uda_password

# Spring Profile
SPRING_PROFILES_ACTIVE=prod
```

#### **application-prod.yml (leer variables)**

```yaml
spring:
  datasource:
    url: jdbc:oracle:thin:@${DB_HOST}:${DB_PORT}:${DB_SID}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

---

### **PASO 6: Ejecutar con Oracle**

#### **Opción 1: Variable de entorno**

```bash
export SPRING_PROFILES_ACTIVE=prod
mvn spring-boot:run
```

#### **Opción 2: Parámetro de Maven**

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

#### **Opción 3: IntelliJ IDEA**

```
Run > Edit Configurations > Active profiles: prod
```

#### **Opción 4: Línea de comandos del JAR**

```bash
java -jar -Dspring.profiles.active=prod target/mi-aplicacion.jar
```

---

## ⚠️ Diferencias Importantes H2 vs Oracle

### **Diferencias Principales**

| Aspecto | H2 | Oracle |
|---------|-----|--------|
| **Boolean** | `BOOLEAN` | `NUMBER(1)` |
| **String** | `VARCHAR(n)` | `VARCHAR2(n)` |
| **ID** | `AUTO_INCREMENT` | `SEQUENCE.NEXTVAL` |
| **Paginación** | `LIMIT/OFFSET` | `OFFSET/FETCH` |
| **NULL** | `IFNULL()` | `NVL()` |

**Ejemplo secuencia Oracle:**
```sql
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1;
CREATE TABLE PRODUCTOS (ID NUMBER(19) PRIMARY KEY);
INSERT INTO PRODUCTOS VALUES (PRODUCTOS_SEQ.NEXTVAL);
```

---

## 🔍 Solución de Problemas

### **Problema 1: "ORA-12154: TNS:could not resolve the connect identifier"**

**Causa:** URL de conexión incorrecta o servicio no disponible

**Soluciones:**

```yaml
# Opción 1: SID
url: jdbc:oracle:thin:@localhost:1521:XE

# Opción 2: Service Name
url: jdbc:oracle:thin:@//localhost:1521/XEPDB1

# Opción 3: TNS Name
url: jdbc:oracle:thin:@ORCL
```

**Verificar servicio:**

```bash
# Linux/Mac
lsnrctl status

# Windows
lsnrctl status
```

---

### **Problema 2: "ORA-01017: invalid username/password"**

**Causa:** Credenciales incorrectas o usuario bloqueado

**Soluciones:**

```sql
-- Verificar usuario existe
SELECT username, account_status FROM dba_users WHERE username = 'UDA_USER';

-- Desbloquear usuario
ALTER USER uda_user ACCOUNT UNLOCK;

-- Resetear password
ALTER USER uda_user IDENTIFIED BY nueva_password;

-- Verificar permisos
SELECT * FROM dba_sys_privs WHERE grantee = 'UDA_USER';
```

---

### **Problema 3: "ORA-00942: table or view does not exist"**

**Causa:** Tabla no existe o está en otro schema

**Soluciones:**

```sql
-- Verificar tablas del usuario actual
SELECT table_name FROM user_tables;

-- Verificar todas las tablas accesibles
SELECT owner, table_name FROM all_tables WHERE table_name = 'PRODUCTOS';

-- Verificar schema actual
SELECT SYS_CONTEXT('USERENV', 'CURRENT_SCHEMA') FROM DUAL;

-- Cambiar schema
ALTER SESSION SET CURRENT_SCHEMA = uda_user;

-- Ejecutar scripts de creación
@V1__initial_schema.sql
```

---

### **Problema 4: "Sequence does not exist"**

**Causa:** Secuencia no creada o nombre incorrecto

**Soluciones:**

```sql
-- Verificar secuencias
SELECT sequence_name FROM user_sequences;

-- Crear secuencia si falta
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1 INCREMENT BY 1;

-- Verificar valor actual
SELECT PRODUCTOS_SEQ.CURRVAL FROM DUAL;

-- Resetear secuencia
DROP SEQUENCE PRODUCTOS_SEQ;
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1 INCREMENT BY 1;
```

---

### **Problema 5: Hibernate no crea tablas**

**Causa:** `ddl-auto: validate` no crea tablas automáticamente

**Soluciones:**

```yaml
# Para desarrollo (crea tablas automáticamente)
spring:
  jpa:
    hibernate:
      ddl-auto: update

# Para producción (solo valida)
spring:
  jpa:
    hibernate:
      ddl-auto: validate
```

**Ejecutar scripts manualmente:**

```bash
sqlplus uda_user/uda_password@localhost:1521/XE @src/main/resources/db/migration/V1__initial_schema.sql
```

---

### **Problema 6: "ORA-01722: invalid number"**

**Causa:** Intentando insertar texto en columna numérica

**Soluciones:**

```java
// ❌ INCORRECTO
@Column(name = "ACTIVO")
private String activo; // Oracle espera NUMBER(1)

// ✅ CORRECTO
@Column(name = "ACTIVO")
private Boolean activo; // Hibernate convierte a 0/1
```

---

### **Problema 7: Fechas como Arrays en JSON**

**Causa:** Falta configuración de Jackson

**Solución:**

```java
// Crear JacksonConfig.java
@Configuration
public class JacksonConfig {
    @Bean
    public ObjectMapper objectMapper() {
        return Jackson2ObjectMapperBuilder.json()
            .modules(new JavaTimeModule())
            .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
            .build();
    }
}
```

---

### **Problema 8: "Connection refused"**

**Causa:** Oracle no está ejecutándose

**Soluciones:**

```bash
# Docker
docker ps | grep oracle
docker start oracle-xe

# Linux
sudo systemctl status oracle-xe
sudo systemctl start oracle-xe

# Windows
net start OracleServiceXE
```

---

## 📋 Checklist de Migración

### **Antes de Migrar**

- [ ] Oracle Database instalado y funcionando
- [ ] Usuario y schema creados en Oracle
- [ ] Permisos otorgados al usuario
- [ ] Conexión probada con SQL Developer o sqlplus
- [ ] Backup de datos actuales (si existen)

### **Configuración del Proyecto**

- [ ] Dependencia de Oracle agregada al `pom.xml`
- [ ] Dependencia de H2 mantenida para desarrollo
- [ ] `application-dev.yml` configurado para H2
- [ ] `application-prod.yml` configurado para Oracle
- [ ] Variables de entorno documentadas en `.env.example`

### **Adaptación de Código**

- [ ] Entidades actualizadas con `@SequenceGenerator`
- [ ] Scripts SQL creados para Oracle
- [ ] Secuencias creadas para todas las tablas
- [ ] Índices definidos en columnas importantes
- [ ] Constraints (PK, FK, UK, CK) configurados
- [ ] `JacksonConfig.java` creado para serialización de fechas

### **Testing**

- [ ] Aplicación inicia sin errores con perfil `dev` (H2)
- [ ] Aplicación inicia sin errores con perfil `prod` (Oracle)
- [ ] Tablas creadas correctamente en Oracle
- [ ] Secuencias funcionando correctamente
- [ ] CRUD completo funciona en ambos perfiles
- [ ] Datos de prueba cargados correctamente
- [ ] Fechas se serializan correctamente en JSON
- [ ] Performance aceptable en Oracle

### **Documentación**

- [ ] README actualizado con instrucciones de Oracle
- [ ] Variables de entorno documentadas
- [ ] Scripts SQL documentados
- [ ] Guía de troubleshooting creada

---

## 🎓 Mejores Prácticas

### **1. Usar Perfiles de Spring**

```bash
# Desarrollo con H2 (rápido, sin instalación)
mvn spring-boot:run -Dspring-boot.run.profiles=dev

# Producción con Oracle (entorno real)
mvn spring-boot:run -Dspring-boot.run.profiles=prod

# Tests de integración con H2
mvn test -Dspring.profiles.active=test
```

---

### **2. Flyway para Migraciones**

**Agregar dependencia:**

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

**Configurar:**

```yaml
spring:
  flyway:
    enabled: true
    locations: classpath:db/migration
    baseline-on-migrate: true
    baseline-version: 0
```

**Estructura de scripts:**

```
src/main/resources/db/migration/
├── V1__initial_schema.sql
├── V2__add_categorias.sql
├── V3__add_usuarios.sql
└── V4__add_foreign_keys.sql
```

---

### **3. Scripts SQL Versionados**

**Nomenclatura:**

```
V{version}__{descripcion}.sql

Ejemplos:
V1__initial_schema.sql
V2__add_categorias_table.sql
V3__insert_initial_data.sql
V4__add_index_productos_nombre.sql
```

**Contenido:**

```sql
-- V1__initial_schema.sql
-- Descripción: Creación de esquema inicial
-- Autor: UDA
-- Fecha: 2024-01-15

-- Crear secuencias
CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1 INCREMENT BY 1;

-- Crear tablas
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) PRIMARY KEY,
    NOMBRE VARCHAR2(200) NOT NULL
);

-- Comentarios
COMMENT ON TABLE PRODUCTOS IS 'Tabla de productos';
```

---

### **4. Backup Regular**

**Exportar datos:**

```bash
# Exportar schema completo
expdp uda_user/uda_password \
  directory=DATA_PUMP_DIR \
  dumpfile=backup_$(date +%Y%m%d).dmp \
  logfile=backup_$(date +%Y%m%d).log \
  schemas=uda_user

# Exportar solo tablas específicas
expdp uda_user/uda_password \
  directory=DATA_PUMP_DIR \
  dumpfile=productos_backup.dmp \
  tables=PRODUCTOS,CATEGORIAS
```

**Importar datos:**

```bash
impdp uda_user/uda_password \
  directory=DATA_PUMP_DIR \
  dumpfile=backup_20240115.dmp \
  logfile=import.log \
  table_exists_action=replace
```

---

### **5. Monitoreo de Performance**

**Queries lentas:**

```sql
-- Ver queries más lentas
SELECT sql_text, elapsed_time, executions
FROM v$sql
WHERE elapsed_time > 1000000
ORDER BY elapsed_time DESC;

-- Ver plan de ejecución
EXPLAIN PLAN FOR
SELECT * FROM PRODUCTOS WHERE NOMBRE LIKE '%texto%';

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

**Índices faltantes:**

```sql
-- Verificar índices de una tabla
SELECT index_name, column_name, column_position
FROM user_ind_columns
WHERE table_name = 'PRODUCTOS'
ORDER BY index_name, column_position;
```

---

### **6. Seguridad**

**No hardcodear credenciales:**

```yaml
# ❌ INCORRECTO
spring:
  datasource:
    username: uda_user
    password: uda_password123

# ✅ CORRECTO
spring:
  datasource:
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

**Usar variables de entorno:**

```bash
# Linux/Mac
export DB_USERNAME=uda_user
export DB_PASSWORD=uda_password

# Windows
set DB_USERNAME=uda_user
set DB_PASSWORD=uda_password

# Docker
docker run -e DB_USERNAME=uda_user -e DB_PASSWORD=uda_password ...
```

**Archivo .env (no commitear):**

```env
# .env
DB_HOST=localhost
DB_PORT=1521
DB_SID=XE
DB_USERNAME=uda_user
DB_PASSWORD=uda_password
```

**Agregar a .gitignore:**

```gitignore
# Credenciales
.env
.env.local
.env.*.local
application-local.yml
```

---

### **7. Optimización de Conexiones**

**Configurar HikariCP:**

```yaml
spring:
  datasource:
    hikari:
      # Pool de conexiones
      maximum-pool-size: 10
      minimum-idle: 5
      
      # Timeouts
      connection-timeout: 30000      # 30 segundos
      idle-timeout: 600000           # 10 minutos
      max-lifetime: 1800000          # 30 minutos
      
      # Validación
      connection-test-query: SELECT 1 FROM DUAL
      validation-timeout: 5000
      
      # Leak detection
      leak-detection-threshold: 60000  # 1 minuto
      
      # Pool name
      pool-name: HikariPool-Oracle
```

**Monitorear conexiones:**

```sql
-- Ver sesiones activas
SELECT username, machine, program, status, COUNT(*)
FROM v$session
WHERE username = 'UDA_USER'
GROUP BY username, machine, program, status;

-- Ver conexiones por IP
SELECT machine, COUNT(*) as connections
FROM v$session
WHERE username = 'UDA_USER'
GROUP BY machine;

-- Matar sesión si es necesario
ALTER SYSTEM KILL SESSION 'sid,serial#';
```

---

### **8. Testing con Múltiples Perfiles**

**Estructura de tests:**

```
src/test/
├── java/
│   └── com/uda/proyecto/
│       ├── integration/        # Tests con Oracle
│       │   └── ProductoIntegrationTest.java
│       └── unit/              # Tests con H2
│           └── ProductoServiceTest.java
└── resources/
    ├── application-test.yml   # Configuración para tests
    └── data-test.sql          # Datos de prueba
```

**application-test.yml:**

```yaml
spring:
  config:
    activate:
      on-profile: test
  
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password: 
  
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: create-drop
    show-sql: true
  
  sql:
    init:
      mode: always
      data-locations: classpath:data-test.sql
```

**Test con H2:**

```java
@SpringBootTest
@ActiveProfiles("test")
class ProductoServiceTest {
    
    @Autowired
    private ProductoService productoService;
    
    @Test
    void testCrearProducto() {
        ProductoDTO dto = ProductoDTO.builder()
            .nombre("Producto Test")
            .precio(new BigDecimal("99.99"))
            .build();
        
        ProductoDTO created = productoService.create(dto);
        
        assertNotNull(created.getId());
        assertEquals("Producto Test", created.getNombre());
    }
}
```

**Test de integración con Oracle:**

```java
@SpringBootTest
@ActiveProfiles("prod")
@Testcontainers
class ProductoIntegrationTest {
    
    @Container
    static OracleContainer oracle = new OracleContainer("gvenzl/oracle-xe:21-slim")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");
    
    @DynamicPropertySource
    static void setProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", oracle::getJdbcUrl);
        registry.add("spring.datasource.username", oracle::getUsername);
        registry.add("spring.datasource.password", oracle::getPassword);
    }
    
    @Autowired
    private ProductoService productoService;
    
    @Test
    void testCrearProductoEnOracle() {
        ProductoDTO dto = ProductoDTO.builder()
            .nombre("Producto Oracle")
            .precio(new BigDecimal("149.99"))
            .build();
        
        ProductoDTO created = productoService.create(dto);
        
        assertNotNull(created.getId());
        assertEquals("Producto Oracle", created.getNombre());
    }
}
```

---

## 🐳 Docker Compose para Desarrollo

### **docker-compose.yml**

```yaml
version: '3.8'

services:
  # Oracle Database
  oracle:
    image: container-registry.oracle.com/database/express:21.3.0-xe
    container_name: oracle-dev
    restart: unless-stopped
    ports:
      - "1521:1521"
      - "5500:5500"
    environment:
      ORACLE_PWD: OraclePassword123
      ORACLE_CHARACTERSET: AL32UTF8
    volumes:
      - oracle-data:/opt/oracle/oradata
      - ./database/scripts:/docker-entrypoint-initdb.d
    healthcheck:
      test: ["CMD", "sqlplus", "-L", "sys/OraclePassword123@//localhost:1521/XE as sysdba", "@healthcheck.sql"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 60s
    networks:
      - app-network

  # Aplicación Spring Boot
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: app-backend
    restart: unless-stopped
    depends_on:
      oracle:
        condition: service_healthy
    ports:
      - "8080:8080"
    environment:
      SPRING_PROFILES_ACTIVE: prod
      DB_HOST: oracle
      DB_PORT: 1521
      DB_SID: XE
      DB_USERNAME: uda_user
      DB_PASSWORD: uda_password
    networks:
      - app-network

  # Frontend React
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: app-frontend
    restart: unless-stopped
    depends_on:
      - backend
    ports:
      - "3000:80"
    environment:
      REACT_APP_API_URL: http://localhost:8080/api/v1
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  oracle-data:
    driver: local
```

### **Scripts de Inicialización**

**database/scripts/01-create-user.sql:**

```sql
-- Esperar a que Oracle esté listo
WHENEVER SQLERROR EXIT SQL.SQLCODE

-- Conectar como SYSTEM
CONNECT system/OraclePassword123@XE

-- Crear usuario
CREATE USER uda_user IDENTIFIED BY uda_password;

-- Otorgar permisos
GRANT CONNECT, RESOURCE TO uda_user;
GRANT CREATE SESSION TO uda_user;
GRANT CREATE TABLE TO uda_user;
GRANT CREATE SEQUENCE TO uda_user;
GRANT CREATE VIEW TO uda_user;
GRANT UNLIMITED TABLESPACE TO uda_user;

-- Verificar
SELECT username, account_status, created 
FROM dba_users 
WHERE username = 'UDA_USER';

EXIT;
```

**database/scripts/healthcheck.sql:**

```sql
SELECT 'OK' FROM DUAL;
EXIT;
```

### **Comandos Docker Compose**

```bash
# Iniciar todos los servicios
docker-compose up -d

# Ver logs
docker-compose logs -f

# Ver logs solo de Oracle
docker-compose logs -f oracle

# Detener servicios
docker-compose down

# Detener y eliminar volúmenes (CUIDADO: borra datos)
docker-compose down -v

# Reconstruir imágenes
docker-compose build --no-cache

# Reiniciar un servicio específico
docker-compose restart backend
```

---

## 📊 Comparativa Final: H2 vs Oracle

### **Tabla Comparativa**

| Característica | H2 | Oracle | Recomendación |
|----------------|-----|--------|---------------|
| **Instalación** | ✅ Ninguna | ⚠️ Compleja | H2 para desarrollo rápido |
| **Velocidad** | ✅ Muy rápida (memoria) | ⚠️ Depende de hardware | H2 para tests |
| **Persistencia** | ⚠️ Opcional | ✅ Siempre | Oracle para producción |
| **Características** | ⚠️ Básicas | ✅ Avanzadas | Oracle para features complejas |
| **Escalabilidad** | ❌ Limitada | ✅ Excelente | Oracle para producción |
| **Costo** | ✅ Gratis | ⚠️ Licencia (XE gratis) | H2 para desarrollo |
| **Compatibilidad SQL** | ⚠️ Parcial | ✅ Completa | Oracle para SQL complejo |
| **Herramientas** | ⚠️ Básicas | ✅ Muchas | Oracle para administración |
| **Soporte** | ⚠️ Comunidad | ✅ Comercial | Oracle para empresas |
| **Curva de aprendizaje** | ✅ Baja | ⚠️ Alta | H2 para principiantes |

---

### **Estrategia Recomendada**

```
┌─────────────────────────────────────────────────────────┐
│                    CICLO DE DESARROLLO                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. DESARROLLO LOCAL                                    │
│     ├─ H2 en memoria (rápido, sin configuración)       │
│     ├─ Datos de prueba automáticos                     │
│     └─ Iteración rápida                                │
│                                                         │
│  2. TESTS UNITARIOS                                     │
│     ├─ H2 en memoria                                    │
│     ├─ Datos controlados                               │
│     └─ Ejecución rápida                                │
│                                                         │
│  3. TESTS DE INTEGRACIÓN                                │
│     ├─ Oracle en Docker/Testcontainers                 │
│     ├─ Entorno similar a producción                    │
│     └─ Validación de SQL específico                    │
│                                                         │
│  4. PRE-PRODUCCIÓN                                      │
│     ├─ Oracle Database                                  │
│     ├─ Datos reales anonimizados                       │
│     └─ Performance testing                             │
│                                                         │
│  5. PRODUCCIÓN                                          │
│     ├─ Oracle Database                                  │
│     ├─ Alta disponibilidad                             │
│     └─ Backups automáticos                             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🎓 Recursos Adicionales

### **Documentación Oficial**

- **Oracle Database:** https://docs.oracle.com/en/database/
- **Spring Boot:** https://docs.spring.io/spring-boot/docs/current/reference/html/
- **Hibernate:** https://hibernate.org/orm/documentation/
- **H2 Database:** https://www.h2database.com/html/main.html
- **HikariCP:** https://github.com/brettwooldridge/HikariCP

### **Herramientas Útiles**

| Herramienta | Propósito | URL |
|-------------|-----------|-----|
| **SQL Developer** | IDE para Oracle | https://www.oracle.com/database/sqldeveloper/ |
| **DBeaver** | Cliente universal de BD | https://dbeaver.io/ |
| **Flyway** | Migraciones de BD | https://flywaydb.org/ |
| **Liquibase** | Migraciones de BD | https://www.liquibase.org/ |
| **Testcontainers** | Tests con Docker | https://www.testcontainers.org/ |

### **Tutoriales Recomendados**

1. **Oracle Database Express Edition:**
   - https://www.oracle.com/database/technologies/appdev/xe.html

2. **Spring Boot + Oracle:**
   - https://spring.io/guides/gs/accessing-data-jpa/

3. **Docker + Oracle:**
   - https://github.com/oracle/docker-images/tree/main/OracleDatabase

4. **Flyway Migrations:**
   - https://flywaydb.org/documentation/usage/maven/

---

## 🆘 Soporte y Ayuda

### **Problemas Comunes**

Si encuentras problemas no cubiertos en esta guía:

1. **Revisar logs de la aplicación:**
   ```bash
   tail -f logs/application.log
   ```

2. **Revisar logs de Oracle:**
   ```bash
   # Docker
   docker logs oracle-dev
   
   # Instalación local
   tail -f $ORACLE_HOME/diag/rdbms/xe/XE/trace/alert_XE.log
   ```

3. **Verificar conectividad:**
   ```bash
   # Ping al servidor
   ping localhost
   
   # Verificar puerto abierto
   telnet localhost 1521
   
   # Test de conexión SQL
   sqlplus uda_user/uda_password@localhost:1521/XE
   ```

4. **Consultar documentación:**
   - Revisar `ESPECIFICACION_COMPLETA.md`
   - Revisar `ESTRUCTURA_PROYECTO.md`
   - Revisar `CHECKLIST_CALIDAD.md`

### **Comunidad**

- **Stack Overflow:** https://stackoverflow.com/questions/tagged/oracle
- **Oracle Community:** https://community.oracle.com/
- **Spring Community:** https://spring.io/community

---

## 📝 Notas Finales

### **Recomendaciones Generales**

1. ✅ **Siempre usar H2 para desarrollo local** - Es más rápido y no requiere configuración
2. ✅ **Probar con Oracle antes de producción** - Evita sorpresas en deployment
3. ✅ **Mantener scripts SQL versionados** - Facilita migraciones y rollbacks
4. ✅ **Usar variables de entorno** - Nunca hardcodear credenciales
5. ✅ **Documentar diferencias** - Si usas SQL específico de Oracle, documentarlo
6. ✅ **Hacer backups regulares** - Especialmente antes de migraciones
7. ✅ **Monitorear performance** - Oracle puede ser más lento que H2 en desarrollo

### **Checklist Final**

Antes de considerar la migración completa:

- [ ] Aplicación funciona correctamente con H2
- [ ] Aplicación funciona correctamente con Oracle
- [ ] Todos los tests pasan en ambos entornos
- [ ] Scripts SQL ejecutados y verificados
- [ ] Datos de prueba cargados correctamente
- [ ] Performance aceptable en Oracle
- [ ] Documentación actualizada
- [ ] Equipo capacitado en Oracle
- [ ] Plan de rollback definido
- [ ] Backups configurados

### **Próximos Pasos**

Una vez completada la migración:

1. **Optimización:**
   - Analizar queries lentas
   - Agregar índices necesarios
   - Ajustar pool de conexiones

2. **Monitoreo:**
   - Configurar alertas
   - Revisar logs regularmente
   - Monitorear uso de recursos

3. **Mantenimiento:**
   - Actualizar estadísticas de Oracle
   - Limpiar datos antiguos
   - Revisar plan de backups

4. **Documentación:**
   - Actualizar README
   - Documentar procedimientos
   - Crear runbooks

---

## 🎉 Conclusión

La migración de H2 a Oracle es un proceso **gradual y controlado**. No es necesario migrar todo de una vez:

```
Fase 1: Desarrollo con H2 (rápido, sin fricción)
   ↓
Fase 2: Tests con Oracle (validación)
   ↓
Fase 3: Pre-producción con Oracle (staging)
   ↓
Fase 4: Producción con Oracle (final)
```

**Ventajas de este enfoque:**

✅ Desarrollo rápido con H2  
✅ Validación temprana con Oracle  
✅ Menor riesgo en producción  
✅ Equipo familiarizado con ambas BD  
✅ Flexibilidad para elegir según necesidad  

**Recuerda:**

> "H2 para velocidad de desarrollo, Oracle para robustez en producción"
