# Checklist de Calidad - Aplicación UDA

---

## 📋 Propósito

Este documento proporciona una lista de verificación exhaustiva que la IA debe completar antes de entregar una aplicación UDA generada. Cada ítem debe ser verificado y marcado como completado.

**⚠️ IMPORTANTE:** Para verificar los 8 elementos críticos obligatorios, consulta [REGLAS_GENERACION.md](REGLAS_GENERACION.md#elementos-críticos-obligatorios)

**💡 Nota:** Para validación paso a paso con ejemplos, consulta [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)

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
- [ ] `application-prod.yml` existe con configuración de producción
- [ ] Configuración de datasource usa variables de entorno
- [ ] Configuración de JPA está correcta (dialect: Oracle)
- [ ] `ddl-auto` está en `validate` para producción
- [ ] Configuración de logging está presente
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

### Entidades

#### Entidades de Dominio

Para cada entidad de dominio:
- [ ] Extiende `BaseEntity` (si existe)
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
- [ ] Mensajes de validación son descriptivos en español
- [ ] No incluye campos sensibles (passwords sin encriptar)
- [ ] Tiene JavaDoc completo

#### DTOs Especiales

- [ ] `LoginRequestDTO` existe con `username` y `password`
- [ ] `LoginResponseDTO` existe con `token`, `type`, `id`, `username`, `email`, `roles`
- [ ] `RegisterRequestDTO` existe con validaciones completas
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

### Controllers

Para cada Controller:
- [ ] Nombre sigue formato `[Entidad]Controller.java`
- [ ] Tiene anotaciones:
  - [ ] `@RestController`
  - [ ] `@RequestMapping("/api/v1/[entidades]")`
  - [ ] `@RequiredArgsConstructor`
  - [ ] `@Slf4j`
  - [ ] `@CrossOrigin(origins = "*")`
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
- [ ] Loguea requests importantes
- [ ] Tiene JavaDoc completo

### Excepciones

- [ ] `GlobalExceptionHandler.java` existe
- [ ] Tiene anotación `@RestControllerAdvice`
- [ ] Maneja excepciones:
  - [ ] `ResourceNotFoundException` → 404
  - [ ] `MethodArgumentNotValidException` → 400
  - [ ] `Exception` (genérica) → 500
- [ ] Retorna `ErrorResponse` estructurado
- [ ] Loguea errores apropiadamente
- [ ] No expone stack traces en producción
- [ ] Incluye timestamp en respuestas de error

#### Excepciones Personalizadas

- [ ] `ResourceNotFoundException.java` existe
- [ ] Todas extienden `RuntimeException`
- [ ] Tienen constructores apropiados
- [ ] Tienen JavaDoc

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
  - [ ] `testDelete_Success()`
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
  - [ ] `testCreate_Success()`
  - [ ] `testUpdate_Success()`
  - [ ] `testDelete_Success()`
- [ ] Verifica códigos de estado HTTP
- [ ] Verifica contenido de respuestas JSON

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
- [ ] `vite.config.js` existe y configura:
  - [ ] Plugin de React
  - [ ] Puerto del servidor (5173)
  - [ ] Proxy para API
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

**📖 Referencia**: [UI_STANDARDS.md](UI_STANDARDS.md)

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

#### Servicios de Dominio

Para cada servicio:
- [ ] Nombre sigue formato `[entidad]Service.js`
- [ ] Métodos CRUD implementados:
  - [ ] `getAll(params)`
  - [ ] `getById(id)`
  - [ ] `create(data)`
  - [ ] `update(id, data)`
  - [ ] `delete(id)`
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
  - [ ] `logout()`
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

### Utilidades

#### validationSchemas.js

- [ ] `validationSchemas.js` existe
- [ ] Esquemas Yup exportados:
  - [ ] `loginSchema`
  - [ ] Esquemas para cada formulario de dominio
- [ ] Mensajes de error descriptivos en español
- [ ] Validaciones completas

#### constants.js

- [ ] `constants.js` existe
- [ ] Define constantes en UPPER_SNAKE_CASE
- [ ] Incluye:
  - [ ] `API_BASE_URL`
  - [ ] Códigos de estado HTTP
  - [ ] Mensajes comunes

### Tests Frontend

- [ ] Tests componentes principales existen
- [ ] Usa Vitest + React Testing Library
- [ ] Tests cubren funcionalidad básica

---

## 📊 Base de Datos

- [ ] Scripts SQL en MAYÚSCULAS
- [ ] Secuencias + Índices + Constraints
- [ ] Datos iniciales (roles)

---

## 📝 Documentación

- [ ] README.md completo
- [ ] JavaDoc en clases públicas
- [ ] JSDoc en servicios
- [ ] .env.example documentado

---

## 📊 Criterios de Calidad

**Puntuación mínima:** 80/100

- **Elementos Críticos (55 pts):** Ver [REGLAS_GENERACION.md](REGLAS_GENERACION.md)
- **Funcionalidad (25 pts):** CRUD completo + Autenticación
- **Calidad (20 pts):** Tests + Documentación + Buenas prácticas

**Validación rápida:**
```bash
# Backend
mvn clean package  # Debe generar WAR
mvn spring-boot:run  # Debe iniciar con H2

# Frontend
npm run build  # Debe generar dist/
npm run dev    # Debe iniciar sin errores
```