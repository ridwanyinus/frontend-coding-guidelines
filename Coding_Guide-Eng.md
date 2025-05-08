# Coding Guidelines

## 🔵🔵🔵 HTML 🔵🔵🔵

#### Principles

- W3C Standards Compliance: Write valid HTML code that follows W3C recommendations.
- Semantics: Correctly understand the meaning of elements and choose/use appropriate semantic elements. Avoid using redundant `div` elements that lack meaning or content.
- Accessibility: Ensure content is accessible to all users, including screen reader users and keyboard operators, by meeting basic accessibility requirements such as providing alternative text (`alt`), associating form controls with labels, and creating appropriate heading structures. Also check out [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/)
- Performance: Avoid unnecessary elements and markup, optimize images, and utilize lazy loading (`loading="lazy"`) to improve loading speed.

#### Compliance Verification

Adherence to the above principles is primarily verified by the static analysis tool [markuplint](https://markuplint.dev/ja/) that has been implemented in the project. Please treat lint errors and warnings output by markuplint as issues that need to be fixed.

## 🔴🔴🔴 CSS 🔴🔴🔴

### Basic Policy

Adopt one of the following methods and maintain consistency throughout the project:

- SCSS + BEM notation: Use SCSS as a CSS preprocessor and adopt BEM (Block, Element, Modifier) for class naming conventions.
- Tailwind CSS: Use Tailwind CSS, a utility-first CSS framework.

### Common Principles

Whether using SCSS/BEM or Tailwind CSS, adhere to the following principles:

#### 1. Definition and Use of Design Tokens

- Define design tokens for colors, font sizes, spacing, breakpoints, etc., and use them through variables or configuration files rather than hardcoding values directly in CSS.
- This maintains design consistency and facilitates easier change management.

##### Tailwind

```js
module.exports = {
  theme: {
    extend: {
      // When extending the existing theme
      colors: {
        primary: 'oklch(45% 0.2 270)', // Define custom color 'primary'
        secondary: '#ff4500',
        // Other design tokens (spacing, fontSize, etc. defined similarly)
      },
    },
  },
  // ...other settings
};
```

```jsx
<button className='bg-primary'>button</button>
```

##### SCSS

```scss
$color-primary: oklch(45% 0.2 270);

.button--primary {
  background-color: $color-primary;
  color: white;
}
```

---

#### 2. Component-Based Reusable Style Patterns

- HTML elements with frequently repeated style combinations should be abstracted and encapsulated as reusable components.
- This reduces code duplication and significantly improves maintainability. This is particularly important when using Tailwind CSS to avoid direct inscription of numerous utility classes on elements.

```jsx
// Button with many Tailwind classes defined
<button className="bg-yellow-700 border-2 font-semibold border border-gray-300 text-green p-4 rounded">
Custom Button
</button>

// Instead of writing the above structure repeatedly, create a reusable component
<CustomButton>Custom Button</CustomButton>
```

---

#### 3. Style Management Policy

- Tailwind

  - Use utility classes as the foundation.
  - However, when combinations of many utility classes are needed, consider consolidating the style pattern into a component or extracting it into a custom CSS class using the @apply directive. This avoids overwhelming HTML elements with utility classes.

- SCSS + BEM
  - Strictly follow BEM naming conventions (block\_\_element--modifier) so that element roles and states can be inferred from class names.
  - Avoid overusing generic utility classes for single CSS properties (e.g., .mt-10, .text-bold). Instead, define styles within BEM elements or modifiers, or in base styles.

---

#### 4. Additional Principles When Using SCSS

- Limiting Nesting
  - Use SCSS nesting features only when essential, such as when writing styles for pseudo-classes (`&:hover`, `&:focus`) or state classes (`&.is-active`) relative to parent selectors.
  - Avoid excessive nesting to represent element structure. Deep nesting unnecessarily increases selector specificity and significantly reduces code readability, searchability, and maintainability.

```scss
// NG
.hoge {
  &__title {
    color: black;
  }
}

// OK
.hoge__title {
  color: black;
}
```

#### Compliance Verification

- Of the above principles, syntax, basic formatting, and some naming pattern rules are automatically checked by the static analysis tool [stylelint](https://stylelint.io/) if it's implemented in the project. Treat lint errors and warnings output by stylelint as issues that must be fixed.
- However, some principles, such as semantics, component-based patterns with high reusability, and deeper intentions behind naming conventions, cannot be fully covered by stylelint alone. These are enforced through code reviews and common understanding within the team.

## 🟡🟡🟡 JavaScript 🟡🟡🟡

## 📷📷📷 ASSETS 🎥🎥🎥

Static assets (images, videos, etc.) significantly impact website performance. Follow these guidelines to use properly processed assets:

### Images

#### Principles

Always compress and optimize image assets managed and delivered within the project before distribution. This reduces file size and improves page loading speed.

#### Implementation Examples

- Recommended tools include Node.js library `sharp` or image optimization features provided by the framework you're using (such as Next.js's Image component).
- When possible, it's recommended to use AVIF format as the base due to its high compression ratio and quality, with WebP format as a fallback for browsers that don't support AVIF.

```html
<picture>
  <source srcset="/images/hoge.avif" type="image/avif" />
  <img src="/images/hoge.webp" width="1600" height="1200" alt="" loading="lazy" decoding="async" />
</picture>
```

### Videos

#### Principles

Video assets managed and delivered within the project must also be compressed before distribution. Video files tend to be large, so appropriate compression is essential for page performance and reducing user data consumption.

#### Implementation Examples

- For projects with few videos or where simple compression is sufficient, browser-based online compression tools (e.g., [VideoSmaller](https://www.videosmaller.com/jp/)) can be effective options.
- For ongoing handling of numerous videos or when more detailed settings are needed, consider command-line tools like `FFmpeg` or dedicated video conversion/optimization services.

## 💡💡💡 Tips 💡💡💡

### 1. Stopping Unnecessary Processing for Off-Screen Elements

#### Principle

Processes that continuously consume resources even when elements are off-screen, such as loop animations or scroll events, should generally only run when the elements are visible on the screen.

#### Reason

When elements continue to move or events continue to be monitored off-screen, they consume unnecessary CPU resources in parts invisible to the user, potentially leading to decreased page performance.

#### Implementation Method

Use the `Intersection Observer API` to detect when elements enter or exit the screen and control processing (adding/removing classes, adding/removing event listeners, etc.).

#### Implementation Example (Animation Control)

Example of adding a specific class when an element enters the screen and removing it when the element exits:

```ts
const target = document.querySelector('.js-hoge') as HTMLElement;

const io = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      // When the element enters the screen
      target.classList.add('is-loop'); // Add class to start animation
    } else {
      // When the element exits the screen
      target.classList.remove('is-loop'); // Remove class to stop animation
    }
  });
});

// Start monitoring the target element
io.observe(target);
```

#### Implementation Example (Scroll Event Control)

Example of adding a scroll event listener when an element enters the screen and removing it when the element exits:

```ts
const target = document.querySelector('.js-hoge') as HTMLElement;

/**
 * @description Function to execute on scroll
 */
const onScroll = (): void => {
  // Write the process to be executed by the scroll event here...
  console.log('Scrolling inside target element');
};

const io = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      // When the element enters the screen
      // Add scroll event listener
      target.addEventListener('scroll', onScroll);
      console.log('Scroll listener added');
    } else {
      // When the element exits the screen
      // Remove scroll event listener
      target.removeEventListener('scroll', onScroll);
      console.log('Scroll listener removed');
    }
  });
});

// Start monitoring the target element
io.observe(target);
```

---

### 2. Controlling `:hover` Behavior on Touch Devices

#### Issue

The standard `:hover` pseudo-class on touch devices like smartphones and tablets applies hover styles when an element is tapped, and this state is maintained until tapped again (known as the "hover stuck" problem). This can lead to unintended style persistence and decreased user experience.

#### Solution

Use the CSS media query `@media (any-hover: hover)` to apply hover styles only to devices capable of hover operations.

#### Implementation Example

```css
/* This style will not be applied on touch devices where hover is not the primary operation */
@media (any-hover: hover) {
  .hoge:hover {
    opacity: 0.5;
    transition: opacity 0.3s ease;
  }
}
```

---

### 3. Fixing Viewport for Narrow Screens (Less Than 375px)

#### Purpose

Detailed responsive design for very narrow screens (e.g., less than 375px) tends to increase implementation costs. By fixing the viewport display width at a specific breakpoint, we limit the scope of support and improve development efficiency.

#### Implementation Method

Use JavaScript to rewrite the `content` property of the viewport to a fixed value (e.g., `"width=375"`) when `window.outerWidth` falls below a certain width (e.g., 375px).

#### Implementation Example

```ts
const viewport = document.querySelector('meta[name="viewport"]');

/**
 * @description Function to adjust/fix viewport display width based on window width
 * For window widths of 375px or less, the viewport is fixed at width=375.
 */
export const viewportFix = (): void => {
  // Get current window outer width
  const outerWidth = window.outerWidth;

  // Normal responsive setting if greater than 375px, otherwise fixed at 375px
  const value = outerWidth > 375 ? 'width=device-width,initial-scale=1' : 'width=375';

  // Update only if different from current viewport setting
  if (viewport?.getAttribute('content') !== value) {
    viewport?.setAttribute('content', value);
    console.log(`Viewport set to: ${value}`); // Log for confirmation
  }
};

// Execute viewportFix function on page load and window resize
window.addEventListener('resize', viewportFix);
window.addEventListener('load', viewportFix);
```
