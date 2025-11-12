# Design System UDA - Material-UI

---

## 📋 Decisión de Design System

**Design System Oficial**: **Material-UI (MUI) v5+**

### Justificación

✅ **Mantenimiento**: Actualizado y mantenido por Google/MUI Team  
✅ **Documentación**: Extensa y de alta calidad  
✅ **Comunidad**: Grande y activa  
✅ **Accesibilidad**: WCAG 2.1 AA compliant  
✅ **Componentes**: +50 componentes listos para usar  
✅ **Personalización**: Sistema de theming potente  
✅ **TypeScript**: Soporte completo  
✅ **Performance**: Optimizado y tree-shakeable  

---

## 🎨 Tema UDA Estandarizado

### Estructura del Tema

```
frontend/src/theme/
├── index.js           # Tema principal
├── palette.js         # Colores
├── typography.js      # Tipografía
├── components.js      # Overrides de componentes
└── shadows.js         # Sombras personalizadas
```

### 1. Tema Principal

```javascript:frontend/src/theme/index.js
import { createTheme } from '@mui/material/styles';
import palette from './palette';
import typography from './typography';
import components from './components';
import shadows from './shadows';

/**
 * Tema oficial UDA
 * Todas las aplicaciones UDA deben usar este tema
 * 
 * @author UDA
 * @version 1.0.0
 */
const theme = createTheme({
  palette,
  typography,
  components,
  shadows,
  
  // Forma de los componentes
  shape: {
    borderRadius: 8,
  },
  
  // Sistema de espaciado (base: 8px)
  spacing: 8,
  
  // Breakpoints responsive
  breakpoints: {
    values: {
      xs: 0,
      sm: 600,
      md: 960,
      lg: 1280,
      xl: 1920,
    },
  },
  
  // Transiciones
  transitions: {
    duration: {
      shortest: 150,
      shorter: 200,
      short: 250,
      standard: 300,
      complex: 375,
      enteringScreen: 225,
      leavingScreen: 195,
    },
  },
  
  // Z-index
  zIndex: {
    mobileStepper: 1000,
    speedDial: 1050,
    appBar: 1100,
    drawer: 1200,
    modal: 1300,
    snackbar: 1400,
    tooltip: 1500,
  },
});

export default theme;
```

### 2. Paleta de Colores

```javascript:frontend/src/theme/palette.js
/**
 * Paleta de colores oficial UDA
 * Basada en Material Design 3
 */
const palette = {
  mode: 'light',
  
  // Color primario - Azul UDA
  primary: {
    main: '#1976d2',
    light: '#42a5f5',
    dark: '#1565c0',
    contrastText: '#ffffff',
  },
  
  // Color secundario
  secondary: {
    main: '#dc004e',
    light: '#e33371',
    dark: '#9a0036',
    contrastText: '#ffffff',
  },
  
  // Estados
  error: {
    main: '#d32f2f',
    light: '#ef5350',
    dark: '#c62828',
  },
  warning: {
    main: '#ed6c02',
    light: '#ff9800',
    dark: '#e65100',
  },
  info: {
    main: '#0288d1',
    light: '#03a9f4',
    dark: '#01579b',
  },
  success: {
    main: '#2e7d32',
    light: '#4caf50',
    dark: '#1b5e20',
  },
  
  // Grises
  grey: {
    50: '#fafafa',
    100: '#f5f5f5',
    200: '#eeeeee',
    300: '#e0e0e0',
    400: '#bdbdbd',
    500: '#9e9e9e',
    600: '#757575',
    700: '#616161',
    800: '#424242',
    900: '#212121',
  },
  
  // Texto
  text: {
    primary: 'rgba(0, 0, 0, 0.87)',
    secondary: 'rgba(0, 0, 0, 0.6)',
    disabled: 'rgba(0, 0, 0, 0.38)',
  },
  
  // Fondos
  background: {
    default: '#fafafa',
    paper: '#ffffff',
  },
  
  // Divisores
  divider: 'rgba(0, 0, 0, 0.12)',
  
  // Acciones
  action: {
    active: 'rgba(0, 0, 0, 0.54)',
    hover: 'rgba(0, 0, 0, 0.04)',
    selected: 'rgba(0, 0, 0, 0.08)',
    disabled: 'rgba(0, 0, 0, 0.26)',
    disabledBackground: 'rgba(0, 0, 0, 0.12)',
  },
};

export default palette;
```

### 3. Tipografía

```javascript:frontend/src/theme/typography.js
/**
 * Sistema tipográfico UDA
 */
const typography = {
  fontFamily: [
    'Roboto',
    '-apple-system',
    'BlinkMacSystemFont',
    '"Segoe UI"',
    'Arial',
    'sans-serif',
  ].join(','),
  
  fontSize: 14,
  fontWeightLight: 300,
  fontWeightRegular: 400,
  fontWeightMedium: 500,
  fontWeightBold: 700,
  
  h1: {
    fontSize: '2.5rem',
    fontWeight: 300,
    lineHeight: 1.2,
  },
  h2: {
    fontSize: '2rem',
    fontWeight: 300,
    lineHeight: 1.2,
  },
  h3: {
    fontSize: '1.75rem',
    fontWeight: 400,
    lineHeight: 1.2,
  },
  h4: {
    fontSize: '1.5rem',
    fontWeight: 400,
    lineHeight: 1.2,
  },
  h5: {
    fontSize: '1.25rem',
    fontWeight: 400,
    lineHeight: 1.2,
  },
  h6: {
    fontSize: '1rem',
    fontWeight: 500,
    lineHeight: 1.2,
  },
  subtitle1: {
    fontSize: '1rem',
    fontWeight: 400,
    lineHeight: 1.75,
  },
  subtitle2: {
    fontSize: '0.875rem',
    fontWeight: 500,
    lineHeight: 1.57,
  },
  body1: {
    fontSize: '1rem',
    fontWeight: 400,
    lineHeight: 1.5,
  },
  body2: {
    fontSize: '0.875rem',
    fontWeight: 400,
    lineHeight: 1.43,
  },
  button: {
    fontSize: '0.875rem',
    fontWeight: 500,
    lineHeight: 1.75,
    textTransform: 'none', // ⚠️ IMPORTANTE: Sin uppercase
  },
  caption: {
    fontSize: '0.75rem',
    fontWeight: 400,
    lineHeight: 1.66,
  },
  overline: {
    fontSize: '0.75rem',
    fontWeight: 400,
    lineHeight: 2.66,
    textTransform: 'uppercase',
  },
};

export default typography;
```

### 4. Personalización de Componentes

```javascript:frontend/src/theme/components.js
/**
 * Personalización de componentes MUI para UDA
 */
const components = {
  // Botones
  MuiButton: {
    styleOverrides: {
      root: {
        borderRadius: 8,
        textTransform: 'none',
        fontWeight: 500,
      },
      contained: {
        boxShadow: 'none',
        '&:hover': {
          boxShadow: '0px 2px 4px rgba(0, 0, 0, 0.1)',
        },
      },
    },
    defaultProps: {
      disableElevation: true,
    },
  },
  
  // Cards
  MuiCard: {
    styleOverrides: {
      root: {
        borderRadius: 12,
        boxShadow: '0px 2px 8px rgba(0, 0, 0, 0.08)',
      },
    },
  },
  
  // TextField
  MuiTextField: {
    defaultProps: {
      variant: 'outlined',
      size: 'small',
    },
  },
  
  // Paper
  MuiPaper: {
    styleOverrides: {
      root: {
        borderRadius: 12,
      },
    },
  },
  
  // Chip
  MuiChip: {
    styleOverrides: {
      root: {
        borderRadius: 8,
        fontWeight: 500,
      },
    },
  },
  
  // Dialog
  MuiDialog: {
    styleOverrides: {
      paper: {
        borderRadius: 16,
      },
    },
  },
  
  // AppBar
  MuiAppBar: {
    styleOverrides: {
      root: {
        boxShadow: '0px 1px 3px rgba(0, 0, 0, 0.12)',
      },
    },
  },
  
  // Table
  MuiTableCell: {
    styleOverrides: {
      root: {
        borderBottom: '1px solid rgba(0, 0, 0, 0.06)',
      },
      head: {
        fontWeight: 600,
        backgroundColor: '#fafafa',
      },
    },
  },
};

export default components;
```

### 5. Sombras

```javascript:frontend/src/theme/shadows.js
/**
 * Sistema de sombras UDA
 * Más suaves que las sombras por defecto de MUI
 */
const shadows = [
  'none',
  '0px 2px 4px rgba(0, 0, 0, 0.08)',
  '0px 4px 8px rgba(0, 0, 0, 0.08)',
  '0px 8px 16px rgba(0, 0, 0, 0.08)',
  '0px 12px 24px rgba(0, 0, 0, 0.08)',
  '0px 16px 32px rgba(0, 0, 0, 0.08)',
  '0px 20px 40px rgba(0, 0, 0, 0.08)',
  '0px 24px 48px rgba(0, 0, 0, 0.08)',
  '0px 2px 4px rgba(0, 0, 0, 0.08)',
  '0px 4px 8px rgba(0, 0, 0, 0.08)',
  '0px 8px 16px rgba(0, 0, 0, 0.08)',
  '0px 12px 24px rgba(0, 0, 0, 0.08)',
  '0px 16px 32px rgba(0, 0, 0, 0.08)',
  '0px 20px 40px rgba(0, 0, 0, 0.08)',
  '0px 24px 48px rgba(0, 0, 0, 0.08)',
  '0px 2px 4px rgba(0, 0, 0, 0.08)',
  '0px 4px 8px rgba(0, 0, 0, 0.08)',
  '0px 8px 16px rgba(0, 0, 0, 0.08)',
  '0px 12px 24px rgba(0, 0, 0, 0.08)',
  '0px 16px 32px rgba(0, 0, 0, 0.08)',
  '0px 20px 40px rgba(0, 0, 0, 0.08)',
  '0px 24px 48px rgba(0, 0, 0, 0.08)',
  '0px 2px 4px rgba(0, 0, 0, 0.08)',
  '0px 4px 8px rgba(0, 0, 0, 0.08)',
  '0px 8px 16px rgba(0, 0, 0, 0.08)',
];

export default shadows;
```

---

## 📦 Dependencias Obligatorias

```json:frontend/package.json
{
  "dependencies": {
    "@mui/material": "^5.15.0",
    "@mui/icons-material": "^5.15.0",
    "@emotion/react": "^11.11.0",
    "@emotion/styled": "^11.11.0"
  }
}
```

---

## 🎯 Reglas de Uso

### ✅ SIEMPRE

1. **Usar componentes de MUI**: No crear componentes desde cero
2. **Usar el tema**: Envolver la app con `ThemeProvider`
3. **Usar `sx` prop**: Para estilos personalizados
4. **Usar `theme.spacing()`**: Para espaciado
5. **Usar `theme.palette.*`**: Para colores
6. **Usar `Typography`**: Para todo el texto
7. **Usar breakpoints**: Para responsive design
8. **Usar iconos de MUI**: `@mui/icons-material`

### ❌ NUNCA

1. **No usar estilos inline**: `style={{ color: 'red' }}`
2. **No hardcodear colores**: `color: '#1976d2'`
3. **No usar px directamente**: `padding: '16px'`
4. **No mezclar HTML y MUI**: `<div>` + `<Button>`
5. **No usar `!important`**: Resolver conflictos correctamente
6. **No crear wrappers innecesarios**: Usar MUI directamente

---

## 📚 Componentes Comunes UDA

### Biblioteca de Patrones

Crear una carpeta con componentes reutilizables **basados 100% en MUI**:

```
frontend/src/components/common/
├── PageHeader.jsx       # Header de página
├── DataTable.jsx        # Tabla con paginación
├── SearchBar.jsx        # Barra de búsqueda
├── EmptyState.jsx       # Estado vacío
├── LoadingOverlay.jsx   # Overlay de carga
├── ConfirmDialog.jsx    # Diálogo de confirmación
├── StatusChip.jsx       # Chip de estado
└── FileUpload.jsx       # Subida de archivos
```

**Importante**: Todos estos componentes son **composiciones de componentes MUI**, no wrappers.

---

## 🎨 Ejemplos de Uso

### Aplicar el Tema

```jsx:frontend/src/main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import theme from './theme';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <App />
    </ThemeProvider>
  </React.StrictMode>
);
```

### Usar Colores del Tema

```jsx
// ✅ CORRECTO
<Box sx={{ bgcolor: 'primary.main', color: 'primary.contrastText' }}>
  Contenido
</Box>

// ❌ INCORRECTO
<Box style={{ backgroundColor: '#1976d2', color: 'white' }}>
  Contenido
</Box>
```

### Usar Espaciado del Tema

```jsx
// ✅ CORRECTO
<Box sx={{ p: 3, mt: 2, mb: 4 }}>
  Contenido
</Box>

// ❌ INCORRECTO
<Box style={{ padding: '24px', marginTop: '16px', marginBottom: '32px' }}>
  Contenido
</Box>
```

### Responsive Design

```jsx
// ✅ CORRECTO
<Box
  sx={{
    width: {
      xs: '100%',    // móvil
      sm: '80%',     // tablet
      md: '60%',     // desktop
    },
    p: {
      xs: 2,         // móvil
      md: 4,         // desktop
    },
  }}
>
  Contenido
</Box>
```

### Tipografía

```jsx
// ✅ CORRECTO
<Typography variant="h4" component="h1" gutterBottom>
  Título Principal
</Typography>
<Typography variant="body1" color="text.secondary">
  Descripción
</Typography>

// ❌ INCORRECTO
<h1 style={{ fontSize: '24px' }}>Título Principal</h1>
<p style={{ color: '#666' }}>Descripción</p>
```

---

## 🔧 Personalización Avanzada

### Extender el Tema

Si un proyecto necesita colores adicionales:

```javascript:frontend/src/theme/palette.js
const palette = {
  // ... colores base
  
  // Colores personalizados del proyecto
  custom: {
    purple: '#9c27b0',
    orange: '#ff9800',
    teal: '#009688',
  },
};
```

### Modo Oscuro (Opcional)

```javascript:frontend/src/theme/index.js
import { createTheme } from '@mui/material/styles';
import { useMemo, useState } from 'react';

export function useAppTheme() {
  const [mode, setMode] = useState('light');

  const theme = useMemo(
    () =>
      createTheme({
        palette: {
          mode,
          // ... resto de la paleta
        },
        // ... resto del tema
      }),
    [mode]
  );

  const toggleMode = () => {
    setMode((prevMode) => (prevMode === 'light' ? 'dark' : 'light'));
  };

  return { theme, mode, toggleMode };
}
```

---

## 📖 Documentación de Referencia

- **MUI Docs**: https://mui.com/material-ui/getting-started/
- **Theming**: https://mui.com/material-ui/customization/theming/
- **Components**: https://mui.com/material-ui/all-components/
- **Icons**: https://mui.com/material-ui/material-icons/
- **System**: https://mui.com/system/getting-started/

---

## ✅ Checklist de Implementación

Al generar una aplicación UDA, verificar:

- [ ] Tema UDA está en `frontend/src/theme/`
- [ ] `ThemeProvider` envuelve la aplicación
- [ ] `CssBaseline` está incluido
- [ ] Todos los componentes usan MUI
- [ ] No hay estilos inline
- [ ] No hay colores hardcodeados
- [ ] Se usa `sx` prop para estilos
- [ ] Se usa `theme.spacing()` para espaciado
- [ ] Componentes comunes están en `components/common/`
- [ ] Responsive design implementado
- [ ] Iconos de `@mui/icons-material`

---

## 🎯 Conclusión

**Material-UI es el design system oficial y único para aplicaciones UDA.**

No crear wrappers, no crear componentes custom innecesarios. Usar MUI directamente con el tema UDA estandarizado garantiza:

✅ Consistencia entre proyectos  
✅ Mantenibilidad a largo plazo  
✅ Actualizaciones automáticas  
✅ Documentación siempre disponible  
✅ Comunidad de soporte  
✅ Accesibilidad garantizada  

---
