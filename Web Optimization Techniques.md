## 1. Image Optimization Techniques

### a. Responsive Images

- Use the `<picture>` tag to serve different image sizes based on device width.
- Example:
  ```html
  <picture>
    <source srcset="/images/large.avif" type="image/avif" media="(min-width: 800px)" />
    <source srcset="/images/small.webp" type="image/webp" media="(max-width: 799px)" />
    <img src="/images/fallback.jpg" alt="Landscape" loading="lazy" decoding="async" />
  </picture>
  ```
- **Why:** Saves bandwidth on smaller devices by loading lower-resolution images.

### b. Image Compression

- Use tools like **ImageOptim**, **TinyPNG**, **Squoosh**, **Sharp** to reduce image file size without losing quality.
- **Recommended Formats:**
  - **WebP** or **AVIF** for high compression and quality.
  - **SVG** for vector graphics (logos, icons).
- **Why:** Reduces page load time and saves bandwidth.

### c. Lazy Loading for Offscreen Images

- Uses `loading="lazy"` to load images only when they enter the viewport.
- Example:
  ```html
  <img src="/images/large-photo.jpg" alt="Landscape" loading="lazy" />
  ```

### d. Using Image CDNs

- Use CDNs like **Cloudinary** or **Imgix** to dynamically resize and optimize images.
- Example:
  ```html
  <img src="https://cdn.example.com/image.jpg?w=800&h=600&format=webp" alt="Optimized Image" />
  ```
- **Why:** Reduces server load and delivers images faster from nearby servers.

---

## 2. Script Optimization

### a. Defer and Async Scripts

- **Async:** Loads without blocking page rendering.
- **Defer:** Loads in the background and executes after HTML parsing.
- Example:
  ```html
  <script src="script.js" async></script>
  <script src="another-script.js" defer></script>
  ```
- **Why:** Improves page responsiveness.

### b. Minification and Bundling

- Use tools like **Webpack**, **Rollup**, or **esbuild** to minimize script size.
- Example (Webpack config):
  ```javascript
  module.exports = {
    optimization: {
      minimize: true,
    },
  };
  ```

### c. Code Splitting

- Split large JavaScript files into smaller, manageable chunks.
- Example (React with Webpack):

  ```javascript
  import React, { lazy, Suspense } from 'react';

  const LazyComponent = lazy(() => import('./MyComponent'));

  function App() {
    return (
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    );
  }
  ```

---

## 3. CSS Optimization

### a. Minify CSS

- Use **cssnano** to reduce CSS size.
- Example:
  ```bash
  npx cssnano styles.css -o styles.min.css
  ```

### b. Critical CSS

- Inline essential CSS for faster loading.
- Example:
  ```html
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
    }
  </style>
  ```

### c. Avoid Render-Blocking CSS

- Load non-essential CSS asynchronously:
  ```html
  <link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'" />
  ```

---

## 4. Caching Techniques

### a. Browser Caching

- Set cache headers for static files to improve load time.
- Example (Apache config):
  ```apache
  <FilesMatch "\.(jpg|png|css|js)$">
    Header set Cache-Control "public, max-age=31536000"
  </FilesMatch>
  ```

### b. Service Workers

- Cache resources locally for offline use.
- Example:
  ```javascript
  self.addEventListener('fetch', (event) => {
    event.respondWith(
      caches.match(event.request).then((response) => {
        return response || fetch(event.request);
      }),
    );
  });
  ```

---

## 5. Network Optimization

### a. HTTP/2 and HTTP/3

- Use **HTTP/2** for multiplexing requests and **HTTP/3** for faster connections.
- Example (Nginx config):
  ```nginx
  listen 443 ssl http2;
  ```

### b. Content Delivery Networks (CDN)

- Use CDNs to serve static assets from servers closer to users.
- Example: **Cloudflare**, **Akamai**, **AWS CloudFront**.

---

## 6. Font Optimization

### a. Preload Fonts

- Preload critical fonts to avoid delays.
  ```html
  <link rel="preload" href="/fonts/font.woff2" as="font" type="font/woff2" crossorigin />
  ```

### b. Use Modern Formats

- Prefer **WOFF2** for better compression.
- Example:
  ```css
  @font-face {
    font-family: 'CustomFont';
    src: url('/fonts/custom.woff2') format('woff2');
  }
  ```

### c. Fallback Fonts

- Use system fonts as a backup to prevent Flash of Invisible Text (FOIT).
  ```css
  body {
    font-family: 'Roboto', Arial, sans-serif;
  }
  ```

---

## 7. Other Optimizations

### a. Prefetch and Preconnect

- Prefetch for anticipated resources, preconnect to reduce DNS lookup.
  ```html
  <link rel="preconnect" href="https://example.com" /> <link rel="prefetch" href="/next-page.html" />
  ```

### b. Reduce DOM Complexity

- Simplify the DOM structure to improve rendering performance.
- Example:
  ```html
  <div class="container">
    <p>Optimized content here</p>
  </div>
  ```

---

## Summary

1. Image Optimization: Lazy load, compress, responsive formats.
2. Script Optimization: Async, defer, minify, bundle.
3. CSS Optimization: Minify, critical CSS, non-blocking styles.
4. Caching: Browser caching, service workers.
5. Network Optimization: HTTP/2, CDN, prefetch.
6. Font Optimization: Preload, modern formats, fallback fonts.
7. Other Techniques: Prefetch, preconnect, simplify DOM.
