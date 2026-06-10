# Bonanza Lunch — Documentación del Proyecto

## Stack Tecnológico

| Tecnología | Versión | Uso |
|------------|---------|-----|
| **Astro** | ^6.4.4 | Framework web (static site generation, islands architecture) |
| **Tailwind CSS** | ^4.3.0 | Estilos utilitarios (v4 con `@theme` en lugar de `tailwind.config`) |
| **@tailwindcss/vite** | ^4.3.0 | Plugin de Vite para Tailwind v4 |
| **pnpm** | — | Gestor de paquetes |
| **Node** | >=22.12.0 | Runtime |

### Dependencias
```json
{
  "dependencies": {
    "@tailwindcss/vite": "^4.3.0",
    "astro": "^6.4.4",
    "tailwindcss": "^4.3.0"
  },
  "devDependencies": {
    "localtunnel": "^2.0.2",
    "ngrok": "5.0.0-beta.2"
  }
}
```

### Scripts disponibles
```bash
npm run dev      # Inicia servidor de desarrollo (astro dev)
npm run build    # Build a producción (astro build)
npm run preview  # Previsualiza build (astro preview)
npm run astro    # CLI de Astro
```

---

## Estructura de Archivos

```
PaginaWeb/
├── .astro/                   # Caché interna de Astro (types, settings, content)
│   ├── types.d.ts
│   ├── settings.json
│   └── content.d.ts
├── .git/
├── .vscode/
│   ├── extensions.json
│   └── launch.json
├── dist/                     # Build de producción
│   ├── index.html
│   ├── Primera.png
│   ├── segunda.png
│   ├── aboutj.png
│   └── _astro/
│       └── index.CO8CFEez.css
├── public/                   # Assets estáticos
│   ├── Primera.png           # Imagen de galería (platillo)
│   ├── segunda.png           # Imagen de galería (decoración)
│   └── aboutj.png            # Imagen sección "Nosotros"
│   (FALTA: favicon.svg)
├── src/
│   ├── components/
│   │   ├── Navbar.astro      # Barra de navegación fija
│   │   ├── Hero.astro        # Hero section (portada)
│   │   ├── About.astro       # Sección "Nosotros"
│   │   ├── MenuSection.astro # Menú con filtros por categoría
│   │   ├── Gallery.astro     # Galería de imágenes
│   │   ├── Testimonials.astro# Reseñas de Google
│   │   ├── Contact.astro     # Formulario de contacto corporativo
│   │   └── Footer.astro      # Footer
│   ├── layouts/
│   │   └── Layout.astro      # Layout base (head, fonts, meta, slot)
│   ├── pages/
│   │   └── index.astro       # Página única (single-page)
│   └── styles/
│       └── global.css        # Tailwind + theme + animaciones
├── astro.config.mjs          # Configuración de Astro (+ Tailwind v4 via Vite)
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml       # Innecesario (solo 1 proyecto)
├── tsconfig.json
├── .gitignore
├── PAGINAWEB                 # Archivo vacío (probablemente residual)
├── dev*.log / dev*err.log    # ~50 archivos de log residuales (por limpiar)
└── PAGINAWEB.md              # Este archivo
```

---

## Arquitectura

### Single Page Application (SPA-like)
Es una **página única** con scroll suave y navegación por anclas (`#inicio`, `#nosotros`, `#menu`, etc.). No hay enrutamiento real — todo en `src/pages/index.astro`.

### Flujo de componentes
```
Layout.astro (head, fonts, body classes, slot)
└── index.astro
    ├── Navbar.astro       — Fixed top, mobile hamburger menu
    ├── Hero.astro         — Full-screen hero con CTA
    ├── About.astro        — Historia, stats (15+ años, 200+ platos, 50K+ clientes)
    ├── MenuSection.astro  — Grid de platos con filtros por categoría (JS)
    ├── Gallery.astro      — Grid mosaico de imágenes
    ├── Testimonials.astro — Cards con reseñas de Google (estrellas, texto, autor)
    ├── Contact.astro      — Formulario corporativo + info de contacto
    └── Footer.astro       — Links, horarios, redes sociales
```

---

## Sistema de Diseño (Tailwind v4 Theme)

### Colores
```css
@theme {
  --color-cream: #F4F3EF;       /* Fondo principal claro */
  --color-charcoal: #2B2625;    /* Texto oscuro, fondos oscuros */
  --color-beige: #C6A98A;       /* Acentos suaves */
  --color-green: #4A6B42;       /* Acción principal (CTAs) */
  --color-green-dark: #3A5534;  /* Hover de acción */
  --color-wood: #D8A15A;        /* Acentos dorados/destacados */
}
```

### Tipografía
- **Headings**: `DM Serif Display` (serif, elegante)
- **Body**: `Nunito` (sans-serif, limpio), pesos 300-700

### Animaciones (CSS Keyframes)
```css
@keyframes fadeUp    — translateY(30px) → translateY(0) con opacidad
@keyframes fadeIn    — opacity 0 → 1
@keyframes scaleIn   — scale(0.9) → scale(1) con opacidad
```

Clases utilitarias: `.animate-fade-up`, `.animate-fade-in`, `.animate-scale-in`
Stagger classes: `.stagger-1` a `.stagger-6` (delay 100ms-600ms, opacidad inicial 0)

---

## Secciones en Detalle

### 1. Navbar (`Navbar.astro`)
- **Fija** en parte superior (`fixed top-0`, z-50)
- **Desktop**: menú horizontal con links a todas las secciones + CTA "PEDIR COTIZACIÓN" (bg-green)
- **Mobile**: hamburger menu con toggle via JS (`id="menuToggle"`, `id="mobileMenu"`)
- **Problema conocido**: Usa `h-18` (height 4.5rem) que **no existe** en Tailwind v4 por defecto
- **Problema**: Al cerrar el menú mobile al hacer clic en un link, el **ícono del hamburger no cambia** a X

### 2. Hero (`Hero.astro`)
- **Full screen** (`min-h-screen`) con gradiente oscuro
- Fondo con textura decorativa (SVG inline de emoji 🌿 en data URI — puede variar entre navegadores)
- Texto animado con stagger: "Sabor *rústico* que enamora"
- Dos CTAs: "Explorar Menú" (verde) y "Reservar Mesa" (outline)
- Bounce arrow en la parte inferior

### 3. About (`About.astro`)
- Fondo beige/30, grid de 5 columnas (2:3)
- Imagen: `/aboutj.png` con decorative shapes (cuadrado verde, borde wood)
- Texto: historia desde 2010, mención a chef Doña Marta (25+ años)
- Stats: 15+ años, 200+ platos, 50K+ clientes felices

### 4. MenuSection (`MenuSection.astro`)
- Fondo cream, filtros por categoría: Todos / Entradas / Principales / Postres / Bebidas
- **12 items hardcodeados** con data-category, nombre, precio ($MNX), descripción e iconos SVG
- Las imágenes son **degradados CSS** (no fotos reales)
- Filtro JS: oculta/muestra items con transición de opacidad
- **Precios usan `$MNX`** — parece un typo (sería MXN)

### 5. Gallery (`Gallery.astro`)
- Fondo charcoal (oscuro), grid 2-4 columnas
- **8 slots**: 2 con imágenes reales (`Primera.png`, `segunda.png`), **6 son degradados CSS** sin foto real
- Hover: overlay gradient + texto que sube desde abajo
- Una celda destacada: `md:col-span-2 md:row-span-2`

### 6. Testimonials (`Testimonials.astro`)
- **6 reseñas** con estrellas SVG, texto, iniciales del autor, nombre y fuente ("Google Reviews")
- Calificaciones mixtas (5/5, 4/5 y 3/5) — se ve auténtico
- **Problema**: Una card forzada con `md:col-span-2 lg:col-span-1` para corregir layout roto en responsive medio

### 7. Contact (`Contact.astro`)
- Enfocado a **catering corporativo** ("Soluciones Gastronómicas Corporativas")
- Formulario: nombre, email, teléfono, comensales, fecha, hora, comentarios
- **El formulario no envía datos** — el JS solo cambia el texto del botón a "¡Solicitud enviada!" por 3s y resetea el form. No hay fetch, no hay API, no hay backend.
- Sin `<label>` — solo placeholders (mal para accesibilidad)
- Info de contacto: dirección placeholder ("Calle Principal 123, Colonia Centro"), teléfono (+52 555 123 4567), horarios
- Sin Google Maps embed

### 8. Footer (`Footer.astro`)
- Logo, descripción, redes sociales (Instagram, Facebook, TikTok — sin links reales, solo `#`)
- Navegación rápida, horarios, delivery
- Copyright dinámico con `new Date().getFullYear()`

---

## Interactividad (JavaScript del lado cliente)

Todo el JS es **inline** dentro de los componentes `.astro` (no hay bundling separado):

| Componente | Funcionalidad |
|------------|---------------|
| Navbar | Toggle menú mobile (show/hide) |
| MenuSection | Filtro de platos por categoría |
| Contact | Simulación de envío de formulario (no real) |

No se usa ningún framework de JS (React, Vue, Svelte, etc.) — solo vanilla JS.

---

## SEO y Meta

### Actual
- `<title>` dinámico vía prop de Layout
- `<meta name="description">` vía prop
- `<html lang="es">`
- Favicon referenciado: `/favicon.svg` (no existe en `/public/`)

### Ausente
- ❌ Open Graph (`og:title`, `og:description`, `og:image`)
- ❌ Twitter Cards
- ❌ Schema.org / JSON-LD (para restaurante)
- ❌ Sitemap
- ❌ `robots.txt`

---

## Problemas Identificados

### 🔴 Críticos
1. **Formulario no funcional** — no envía datos a ningún backend o servicio externo
2. **Favicon faltante** — `/favicon.svg` referenciado pero no existe

### 🟡 Importantes
3. **`$MNX`** — moneda incorrecta (debería ser `MXN`)
4. **`h-18`** — no existe en Tailwind v4 por defecto (no aplica)
5. **~50 archivos `dev*.log`** en la raíz del proyecto
6. **Galería: 6/8 slots son placeholders** (degradados sin foto)
7. **Dirección placeholder** ("Calle Principal 123")
8. **Layout forzado** en testimonios para corregir salto en md

### 🟢 Mejoras
9. Sin Open Graph / metadatos sociales
10. Sin `<label>` en formulario (solo placeholders)
11. Sin Google Maps
12. Sin lazy loading en imágenes
13. Sin optimización de imágenes (PNG → WebP/AVIF)
14. Sin `aria-current` en navegación activa
15. `localtunnel` y `ngrok` en devDependencies (sin uso aparente)
16. `pnpm-workspace.yaml` innecesario
17. Menú hardcodeado — debería ser data-driven (JSON/API/CMS)
18. Sin CSP (Content Security Policy)

---

## Posibles Mejoras Futuras

- Conectar formulario a servicio como Formspree, EmailJS, o backend propio con validación
- Reemplazar PNGs con imágenes WebP/AVIF y usar `<Image />` de Astro para optimización
- Agregar CMS headless (Decap CMS, Sanity, Strapi) para manejar menú dinámicamente
- Agregar Google Maps embed
- Implementar Open Graph y Schema.org (Restaurant)
- Migrar JS inline a archivos `.astro` con `<script>` module o a archivos separados en `src/scripts/`
- Agregar modo oscuro (el sitio ya es mayormente oscuro en varias secciones)
- Agregar reseñas dinámicas desde API de Google Places
- Agregar un blog o sección de eventos
- Eliminar dependencias no utilizadas (localtunnel, ngrok)
