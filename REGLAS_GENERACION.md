# Reglas de Generación para IA

---

## 📋 Propósito

Este documento define las **reglas estrictas** que cualquier IA debe seguir al generar una aplicación UDA. Estas reglas garantizan consistencia, calidad y adherencia a los estándares definidos.

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
1. Usar `javax.*` en lugar de `jakarta.*` (Spring Boot 3.x usa Jakarta EE)
2. Usar `System.out.println()` (usar logger)
3. Hardcodear credenciales en el código
4. Exponer Entities en Controllers
5. Usar `@Autowired` en campos (usar constructor injection)
6. Ignorar excepciones con `catch` vacío
7. Retornar `null` (usar `Optional`)
8. Usar SQL nativo sin justificación
9. Deshabilitar CSRF sin razón válida
10. Loguear información sensible (passwords, tokens)
11. Usar `SELECT *` en queries

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

## 📚 Ejemplos Detallados: DO vs DON'T

### Backend - Java

#### ✅ DO: Inyección de dependencias por constructor

```java
// ✅ CORRECTO
@Service
@RequiredArgsConstructor
public class ProductoServiceImpl implements ProductoService {
    private final ProductoRepository repository;
    private final ProductoMapper mapper;
    
    // Constructor generado automáticamente por Lombok
}
```

```java
// ❌ INCORRECTO
@Service
public class ProductoServiceImpl implements ProductoService {
    @Autowired
    private ProductoRepository repository;
    
    @Autowired
    private ProductoMapper mapper;
}
```

#### ✅ DO: Usar Optional para valores que pueden ser null

```java
// ✅ CORRECTO
@Override
public ProductoDTO findById(Long id) {
    return repository.findById(id)
        .map(mapper::toDTO)
        .orElseThrow(() -> new ResourceNotFoundException("Producto no encontrado"));
}
```

```java
// ❌ INCORRECTO
@Override
public ProductoDTO findById(Long id) {
    Producto producto = repository.findById(id).get(); // Puede lanzar NoSuchElementException
    if (producto == null) {
        return null; // No retornar null
    }
    return mapper.toDTO(producto);
}
```

#### ✅ DO: Logging apropiado

```java
// ✅ CORRECTO
@Slf4j
public class ProductoServiceImpl {
    public ProductoDTO create(ProductoDTO dto) {
        log.debug("Creando producto: {}", dto.getNombre());
        // ... lógica
        log.info("Producto creado con ID: {}", saved.getId());
        return mapper.toDTO(saved);
    }
}
```

```java
// ❌ INCORRECTO
public class ProductoServiceImpl {
    public ProductoDTO create(ProductoDTO dto) {
        System.out.println("Creando producto: " + dto.getNombre()); // NO usar System.out
        // ... lógica
        return mapper.toDTO(saved);
    }
}
```

#### ✅ DO: Transacciones apropiadas

```java
// ✅ CORRECTO
@Service
@Transactional(readOnly = true) // Por defecto solo lectura
public class ProductoServiceImpl {
    
    @Override
    public List<ProductoDTO> findAll() {
        // Solo lectura, usa la transacción de clase
    }
    
    @Override
    @Transactional // Escritura, sobrescribe la de clase
    public ProductoDTO create(ProductoDTO dto) {
        // Operación de escritura
    }
}
```

```java
// ❌ INCORRECTO
@Service
public class ProductoServiceImpl {
    // Sin @Transactional, cada método abre su propia transacción
    
    public List<ProductoDTO> findAll() {
        // Sin control transaccional
    }
}
```

#### ✅ DO: Validaciones en DTOs

```java
// ✅ CORRECTO
@Data
public class ProductoDTO {
    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 3, max = 100, message = "El nombre debe tener entre 3 y 100 caracteres")
    private String nombre;
    
    @NotNull(message = "El precio es obligatorio")
    @DecimalMin(value = "0.01", message = "El precio debe ser mayor a 0")
    private BigDecimal precio;
}
```

```java
// ❌ INCORRECTO
@Data
public class ProductoDTO {
    private String nombre; // Sin validaciones
    private BigDecimal precio; // Sin validaciones
}
```

#### ✅ DO: Manejo de excepciones

```java
// ✅ CORRECTO
@Override
public ProductoDTO findById(Long id) {
    return repository.findById(id)
        .map(mapper::toDTO)
        .orElseThrow(() -> new ResourceNotFoundException(
            "Producto no encontrado con ID: " + id
        ));
}
```

```java
// ❌ INCORRECTO
@Override
public ProductoDTO findById(Long id) {
    try {
        Producto producto = repository.findById(id).get();
        return mapper.toDTO(producto);
    } catch (Exception e) {
        // Catch genérico y vacío
        return null;
    }
}
```

### Frontend - React

#### ✅ DO: Hooks en el orden correcto

```jsx
// ✅ CORRECTO
function ProductoList() {
  // 1. Hooks de estado
  const [productos, setProductos] = useState([]);
  const [loading, setLoading] = useState(false);
  
  // 2. Hooks de navegación/contexto
  const navigate = useNavigate();
  const { showError } = useNotification();
  
  // 3. useEffect
  useEffect(() => {
    loadProductos();
  }, []);
  
  // 4. Funciones
  const loadProductos = async () => {
    // ...
  };
  
  // 5. Render
  return <div>...</div>;
}
```

```jsx
// ❌ INCORRECTO
function ProductoList() {
  const navigate = useNavigate();
  
  const loadProductos = async () => {
    // ...
  };
  
  const [productos, setProductos] = useState([]); // Hooks después de funciones
  
  useEffect(() => {
    loadProductos();
  }, []);
  
  const [loading, setLoading] = useState(false); // Hooks desordenados
  
  return <div>...</div>;
}
```

#### ✅ DO: Manejo de errores en llamadas API

```jsx
// ✅ CORRECTO
const loadProductos = async () => {
  try {
    setLoading(true);
    const data = await productoService.getAll();
    setProductos(data);
  } catch (error) {
    console.error('Error al cargar productos:', error);
    showError('Error al cargar los productos');
  } finally {
    setLoading(false);
  }
};
```

```jsx
// ❌ INCORRECTO
const loadProductos = async () => {
  const data = await productoService.getAll(); // Sin try-catch
  setProductos(data);
};
```

#### ✅ DO: Validación de formularios

```jsx
// ✅ CORRECTO
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

const schema = yup.object({
  nombre: yup.string().required('El nombre es obligatorio'),
  precio: yup.number().positive().required('El precio es obligatorio'),
});

function ProductoForm() {
  const { control, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(schema),
  });
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="nombre"
        control={control}
        render={({ field }) => (
          <TextField
            {...field}
            error={!!errors.nombre}
            helperText={errors.nombre?.message}
          />
        )}
      />
    </form>
  );
}
```

```jsx
// ❌ INCORRECTO
function ProductoForm() {
  const [nombre, setNombre] = useState('');
  
  const handleSubmit = () => {
    // Sin validación
    productoService.create({ nombre });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input value={nombre} onChange={(e) => setNombre(e.target.value)} />
    </form>
  );
}
```

#### ✅ DO: Uso de PropTypes

```jsx
// ✅ CORRECTO
import PropTypes from 'prop-types';

function ProductoCard({ producto, onEdit, onDelete }) {
  return <Card>...</Card>;
}

ProductoCard.propTypes = {
  producto: PropTypes.shape({
    id: PropTypes.number.isRequired,
    nombre: PropTypes.string.isRequired,
    precio: PropTypes.number.isRequired,
  }).isRequired,
  onEdit: PropTypes.func.isRequired,
  onDelete: PropTypes.func.isRequired,
};

export default ProductoCard;
```

```jsx
// ❌ INCORRECTO
function ProductoCard({ producto, onEdit, onDelete }) {
  return <Card>...</Card>; // Sin PropTypes
}

export default ProductoCard;
```

### Base de Datos - Oracle

#### ✅ DO: Nomenclatura en MAYÚSCULAS

```sql
-- ✅ CORRECTO
CREATE TABLE PRODUCTOS (
    ID NUMBER(19) NOT NULL,
    NOMBRE VARCHAR2(100) NOT NULL,
    PRECIO NUMBER(10,2) NOT NULL,
    CONSTRAINT PK_PRODUCTOS PRIMARY KEY (ID)
);

CREATE SEQUENCE PRODUCTOS_SEQ START WITH 1 INCREMENT BY 1;
CREATE INDEX IDX_PRODUCTOS_NOMBRE ON PRODUCTOS(NOMBRE);
```

```sql
-- ❌ INCORRECTO
create table productos (
    id number(19) not null,
    nombre varchar2(100) not null,
    precio number(10,2) not null,
    primary key (id)  -- Sin nombre de constraint
);

create sequence productos_seq;  -- Minúsculas
```

#### ✅ DO: Constraints con nombres

```sql
-- ✅ CORRECTO
ALTER TABLE PRODUCTOS
ADD CONSTRAINT FK_PRODUCTOS_CATEGORIA 
FOREIGN KEY (CATEGORIA_ID) REFERENCES CATEGORIAS(ID);

ALTER TABLE PRODUCTOS
ADD CONSTRAINT CK_PRODUCTOS_PRECIO 
CHECK (PRECIO > 0);

ALTER TABLE PRODUCTOS
ADD CONSTRAINT UK_PRODUCTOS_SKU 
UNIQUE (SKU);
```

```sql
-- ❌ INCORRECTO
ALTER TABLE PRODUCTOS
ADD FOREIGN KEY (CATEGORIA_ID) REFERENCES CATEGORIAS(ID);  -- Sin nombre

ALTER TABLE PRODUCTOS
ADD CHECK (PRECIO > 0);  -- Sin nombre

ALTER TABLE PRODUCTOS
ADD UNIQUE (SKU);  -- Sin nombre
```

---

## 🔄 Orden Detallado de Generación de Archivos

La IA debe generar los archivos **exactamente en este orden**:

### Fase 1: Configuración Base del Backend (5 archivos)

1. `backend/pom.xml`
2. `backend/src/main/resources/application.yml`
3. `backend/src/main/resources/application-prod.yml`
4. `backend/src/main/java/com/uda/[proyecto]/Application.java`
5. `backend/.gitignore`

### Fase 2: Configuración de Seguridad y Web (2 archivos)

6. `backend/src/main/java/com/uda/[proyecto]/config/SecurityConfig.java`
7. `backend/src/main/java/com/uda/[proyecto]/config/WebConfig.java`

### Fase 3: Excepciones (3 archivos)

8. `backend/src/main/java/com/uda/[proyecto]/exception/ResourceNotFoundException.java`
9. `backend/src/main/java/com/uda/[proyecto]/exception/ErrorResponse.java`
10. `backend/src/main/java/com/uda/[proyecto]/exception/GlobalExceptionHandler.java`

### Fase 4: Entidades (por cada entidad, 6 archivos)

11. `backend/src/main/java/com/uda/[proyecto]/entity/Producto.java`
12. `backend/src/main/java/com/uda/[proyecto]/dto/ProductoDTO.java`
13. `backend/src/main/java/com/uda/[proyecto]/mapper/ProductoMapper.java`
14. `backend/src/main/java/com/uda/[proyecto]/repository/ProductoRepository.java`
15. `backend/src/main/java/com/uda/[proyecto]/service/ProductoService.java`
16. `backend/src/main/java/com/uda/[proyecto]/service/impl/ProductoServiceImpl.java`

### Fase 5: Controladores (1 archivo por entidad)

17. `backend/src/main/java/com/uda/[proyecto]/controller/ProductoController.java`

### Fase 6: Base de Datos (1 archivo)

18. `database/schema.sql` (con tablas, secuencias, índices y datos de ejemplo)

### Fase 7: Configuración Base del Frontend (5 archivos)

19. `frontend/package.json`
20. `frontend/vite.config.js`
21. `frontend/index.html`
22. `frontend/.gitignore`
23. `frontend/.env.example`

### Fase 8: Configuración de Estilos y API (2 archivos)

24. `frontend/src/styles/theme.js`
25. `frontend/src/services/api.js`

### Fase 9: Servicios del Frontend (1 archivo por entidad)

26. `frontend/src/services/productoService.js`

### Fase 10: Componentes Principales (2 archivos)

27. `frontend/src/main.jsx`
28. `frontend/src/App.jsx`

### Fase 11: Páginas (3 archivos por entidad + 1 home)

29. `frontend/src/pages/Home.jsx`
30. `frontend/src/pages/ProductoList.jsx`
31. `frontend/src/pages/ProductoForm.jsx`

### Fase 12: Documentación (1 archivo)

32. `README.md`

---

## ✅ Checklist de Validación Post-Generación

Después de generar todos los archivos, la IA debe verificar:

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

## 📚 Referencias

- **ESTRUCTURA_PROYECTO.md**: Estructura exacta de carpetas y archivos
- **ESPECIFICACION_COMPLETA.md**: Detalles técnicos completos
- **CHECKLIST_CALIDAD.md**: Lista de verificación de calidad
- **docs/**: Documentación detallada por área

---

## ⚠️ Nota Importante

Estas reglas son **OBLIGATORIAS** y **NO NEGOCIABLES**. La IA debe seguirlas al pie de la letra para garantizar:

1. **Consistencia**: Todas las aplicaciones generadas son idénticas en estructura
2. **Calidad**: El código cumple con estándares profesionales
3. **Mantenibilidad**: El código es fácil de entender y modificar
4. **Seguridad**: Se implementan las mejores prácticas de seguridad
5. **Funcionalidad**: La aplicación es completamente funcional

**Cualquier desviación de estas reglas debe ser justificada explícitamente y documentada.**
