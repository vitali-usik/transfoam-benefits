# TransFoam Accordion — Engineering Specification

## 1. Objective

Implement a desktop-first accordion component using only HTML, CSS, and vanilla JavaScript. The final component will be inserted into a Tilda HTML block.

The component must reproduce the behavior and general layout shown in the supplied design references:

- `Screenshot 2026-07-21 at 21.37.51.png` — collapsed state with multiple cards.
- `Screenshot 2026-07-21 at 21.38.06.png` — expanded state of one card.

The implementation must be dependency-free, easy to maintain, and safe to embed into a Tilda page.

---

## 2. Scope

### Included in version 1

- Desktop layout.
- Up to 10 accordion cards.
- Independent open/closed state for every card.
- Multiple cards may remain open simultaneously.
- Opening by clicking only the arrow button.
- Closing by clicking only `.accordion-close`.
- Collapsed view is replaced by expanded view.
- The large number on the left remains visible and keeps its position.
- Card height grows naturally according to expanded content.
- Lightweight fade-in animation.
- Keyboard-accessible buttons and appropriate ARIA attributes.
- One self-contained HTML file suitable for later insertion into a Tilda HTML block.

### Explicitly excluded from version 1

- Mobile layout.
- Height animation.
- Automatic closing of other cards.
- Closing by clicking outside a card.
- Closing by clicking the expanded content.
- Closing by clicking the collapsed header area.
- Closing with Escape.
- External libraries or frameworks.
- Final production icons. Placeholder paths must be used until icon files are supplied.

---

## 3. Core behavior

Each accordion card has two mutually exclusive right-side states:

1. `.tf-accordion__collapsed`
2. `.tf-accordion__expanded`

The large number in `.tf-accordion__number` is outside both states and remains visible whether the card is open or closed.

### Opening

A card opens only when its `.tf-accordion__open` arrow button is activated.

When opening a card:

- add `.is-open` to the corresponding `[data-accordion-item]`;
- set `aria-expanded="true"` on the open button;
- remove `hidden` from `.tf-accordion__expanded`;
- hide `.tf-accordion__collapsed`;
- do not inspect or modify any other card.

### Closing

A card closes only when its `.accordion-close` button is activated.

When closing a card:

- remove `.is-open` from the corresponding `[data-accordion-item]`;
- set `aria-expanded="false"` on the open button;
- add `hidden` to `.tf-accordion__expanded`;
- show `.tf-accordion__collapsed`;
- return focus to the card's `.tf-accordion__open` button;
- do not inspect or modify any other card.

### Independence requirement

There must be no global `activeItem`, `currentItem`, or similar single-open-state variable.

Opening item 2 must not close item 1. All items may be open at the same time.

---

## 4. Recommended project structure

Create a small standalone implementation that can be previewed locally:

```text
/
├── index.html
├── assets/
│   ├── icons/
│   │   ├── thermal.svg
│   │   ├── acoustic.svg
│   │   ├── weight.svg
│   │   ├── arrow-down.svg
│   │   └── arrow-up.svg
│   └── references/
│       ├── collapsed.png
│       └── expanded.png
└── docs/
    └── accordion-spec.md
```

For the first implementation, missing icons may be represented by placeholder file paths or simple temporary inline placeholders. Do not attempt to recreate the final icon artwork in CSS.

---

## 5. Tilda integration strategy

Use one Tilda HTML block containing:

```html
<style>
  /* Component styles */
</style>

<section class="tf-accordion" data-accordion>
  <!-- All accordion items -->
</section>

<script>
  /* Component behavior */
</script>
```

All cards should live in this single block. This avoids duplicate styles, duplicate scripts, and inconsistent spacing.

The final implementation must not depend on Tilda's internal DOM structure, block IDs, or generated class names.

---

## 6. Content strategy

Keep all text content directly in HTML.

Do not generate the cards from a JavaScript data array in version 1.

Reasons:

- content remains visible and inspectable without JavaScript;
- easier formatting of paragraphs, lists, links, and emphasis;
- better accessibility and SEO;
- fewer layout shifts;
- simpler debugging in Tilda;
- easier maintenance for a small number of rarely changed cards.

JavaScript must control behavior only.

To edit copy later, update the relevant text directly inside the HTML block.

---

## 7. Required HTML structure

Use a single root container:

```html
<section class="tf-accordion" data-accordion>
  <!-- items -->
</section>
```

Each card must follow this pattern:

```html
<article class="tf-accordion__item" data-accordion-item>
  <div class="tf-accordion__number" aria-hidden="true">
    1
  </div>

  <div class="tf-accordion__panel">
    <div class="tf-accordion__collapsed">
      <img
        class="tf-accordion__icon"
        src="./assets/icons/icon-temp.png"
        alt=""
      >

      <h2 class="tf-accordion__collapsed-title">
        Najlepsza izolacja cieplna pojazdów
      </h2>

      <button
        class="tf-accordion__open"
        type="button"
        aria-expanded="false"
        aria-controls="tf-accordion-panel-1"
        aria-label="Open: Najlepsza izolacja cieplna pojazdów"
      >
        <img src="./assets/icons/arrow-down.svg" alt="">
      </button>
    </div>

    <div
      class="tf-accordion__expanded"
      id="tf-accordion-panel-1"
      hidden
    >
      <header class="tf-accordion__expanded-header">
        <img
          class="tf-accordion__icon"
          src="./assets/icons/icon-temp.png"
          alt=""
        >

        <h2 class="tf-accordion__expanded-title">
          Superior thermal insulation performance
        </h2>

        <button
          class="accordion-close"
          type="button"
          aria-label="Close: Superior thermal insulation performance"
        >
          <img src="./assets/icons/arrow-up.svg" alt="">
        </button>
      </header>

      <div class="tf-accordion__body">
        <p class="tf-accordion__intro">
          TransFoam® delivers exceptional thermal insulation...
        </p>

        <section class="tf-accordion__benefits">
          <h3 class="tf-accordion__benefits-title">
            Benefits include:
          </h3>

          <ul class="tf-accordion__benefits-list">
            <li>Reduced heat loss in winter and heat gain in summer</li>
            <li>Improved HVAC efficiency</li>
            <li>Lower energy consumption</li>
            <li>Enhanced passenger comfort</li>
            <li>Reduced insulation thickness requirements</li>
          </ul>
        </section>

        <p class="tf-accordion__footer-text">
          Additional description...
        </p>
      </div>
    </div>
  </div>
</article>
```

### HTML constraints

- Use unique `id` values for every expanded panel.
- Match each `aria-controls` value to the corresponding panel `id`.
- Use real `<button>` elements for open and close controls.
- The collapsed content area itself must not be a button.
- Decorative images must use `alt=""`.
- Do not attach inline `onclick` handlers.
- Do not duplicate element IDs.

---

## 8. CSS architecture

Use a strict `tf-accordion` prefix for all component classes, except the required `.accordion-close` class.

Avoid generic global selectors such as:

```css
button {}
h2 {}
ul {}
/*noinspection CssUnusedSymbol*/
.container {}
/*noinspection CssUnusedSymbol*/
.active {}
/*noinspection CssUnusedSymbol*/
.open {}
```

### Local reset

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion,
.tf-accordion * {
  box-sizing: border-box;
}

.tf-accordion button {
  font: inherit;
}
```

### Design tokens

Define central values as CSS custom properties:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion {
  --tf-color-orange: #df560c;
  --tf-color-blue: #315d93;
  --tf-color-peach: #f3c3a8;
  --tf-color-light-peach: #f4ddd2;
  --tf-color-gray: #e0e4eb;
  --tf-color-white: #ffffff;
  --tf-color-black: #000000;

  --tf-radius-card: 18px;
  --tf-radius-panel: 16px;
  --tf-card-gap: 56px;
  --tf-number-column-width: 25%;
  --tf-card-padding: 20px;
}
```

Exact values should be tuned against the supplied screenshots.

### Main card layout

Use CSS Grid:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__item {
  display: grid;
  grid-template-columns: minmax(180px, var(--tf-number-column-width)) minmax(0, 1fr);
  align-items: stretch;
  width: 100%;
  height: auto;
}
```

The number area must stretch to the full card height:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__number {
  display: flex;
  align-items: center;
  justify-content: center;
}
```

The right panel must allow shrinking without horizontal overflow:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__panel {
  min-width: 0;
  height: auto;
}
```

### Collapsed layout

The collapsed right-side state should use three columns:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__collapsed {
  display: grid;
  grid-template-columns: auto minmax(0, 1fr) auto;
  align-items: center;
}
```

Only the arrow button is interactive.

### Expanded layout

The expanded view must remain in normal document flow:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__expanded {
  width: 100%;
  height: auto;
}
```

Do not position the full expanded view absolutely.

### State selectors

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__item:not(.is-open) .tf-accordion__expanded {
  display: none;
}

/*noinspection CssUnusedSymbol*/
.tf-accordion__item.is-open .tf-accordion__collapsed {
  display: none;
}

/*noinspection CssUnusedSymbol*/
.tf-accordion__item.is-open .tf-accordion__expanded {
  display: block;
}

/*noinspection CssUnusedSymbol*/
.tf-accordion__expanded[hidden] {
  display: none;
}
```

### Height rules

Do not use:

- fixed expanded heights;
- arbitrary large `max-height` values;
- JavaScript `scrollHeight` calculations;
- repeated measurement loops;
- absolute positioning for the full expanded section.

The card must grow naturally according to its content.

---

## 9. Animation

Use fade-in only for version 1:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__expanded:not([hidden]) {
  animation: tfAccordionFadeIn 180ms ease-out;
}

@keyframes tfAccordionFadeIn {
  from {
    opacity: 0;
    transform: translateY(-4px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

Respect reduced-motion preferences:

```css
@media (prefers-reduced-motion: reduce) {
  /*noinspection CssUnusedSymbol*/
  .tf-accordion__expanded:not([hidden]) {
    animation: none;
  }
}
```

Do not animate height in this version.

---

## 10. JavaScript architecture

Use one delegated click listener per accordion root.

The script must be idempotent because Tilda may execute initialization more than once.

Reference implementation pattern:

```js
(function () {
  function initAccordion(root) {
    if (root.dataset.accordionInitialized === "true") {
      return;
    }

    root.dataset.accordionInitialized = "true";

    root.addEventListener("click", function (event) {
      const openButton = event.target.closest(".tf-accordion__open");
      const closeButton = event.target.closest(".accordion-close");

      if (openButton && root.contains(openButton)) {
        const item = openButton.closest("[data-accordion-item]");

        if (item) {
          openItem(item);
        }

        return;
      }

      if (closeButton && root.contains(closeButton)) {
        const item = closeButton.closest("[data-accordion-item]");

        if (item) {
          closeItem(item);
        }
      }
    });
  }

  function openItem(item) {
    if (item.classList.contains("is-open")) {
      return;
    }

    const openButton = item.querySelector(".tf-accordion__open");
    const expanded = item.querySelector(".tf-accordion__expanded");

    if (!openButton || !expanded) {
      return;
    }

    item.classList.add("is-open");
    openButton.setAttribute("aria-expanded", "true");
    expanded.hidden = false;
  }

  function closeItem(item) {
    if (!item.classList.contains("is-open")) {
      return;
    }

    const openButton = item.querySelector(".tf-accordion__open");
    const expanded = item.querySelector(".tf-accordion__expanded");

    if (!openButton || !expanded) {
      return;
    }

    item.classList.remove("is-open");
    openButton.setAttribute("aria-expanded", "false");
    expanded.hidden = true;
    openButton.focus({ preventScroll: true });
  }

  function init() {
    document
      .querySelectorAll("[data-accordion]")
      .forEach(initAccordion);
  }

  if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", init, { once: true });
  } else {
    init();
  }

  document.addEventListener("tilda:page:load", init);
})();
```

### JavaScript constraints

Do not:

- close sibling cards;
- query all `.is-open` cards during opening;
- keep one global active card;
- add a separate event listener to every item;
- inject card content from JavaScript;
- calculate the expanded height;
- rely on Tilda-specific generated class names;
- use jQuery;
- use inline handlers.

---

## 11. Accessibility requirements

- Open and close controls must be native buttons.
- Open controls must have `aria-expanded`.
- Open controls must have `aria-controls` pointing to the expanded panel.
- Close controls must have descriptive `aria-label` values.
- Keyboard activation must work through native button behavior.
- Closing must restore focus to the corresponding open button.
- Add visible focus styles.

Example:

```css
/*noinspection CssUnusedSymbol*/
.tf-accordion__open:focus-visible,
.accordion-close:focus-visible {
  outline: 3px solid currentColor;
  outline-offset: 4px;
}
```

---

## 12. Desktop visual requirements

Tune the implementation to the supplied screenshots.

Key characteristics:

- Cards are stacked vertically with generous spacing.
- Outer card background is peach.
- A large white number occupies the left section.
- The right section has a white framed panel with an orange border.
- A light gray offset/shadow panel appears behind the right section in collapsed state.
- The inner collapsed header has a light peach background.
- Main icon is blue.
- Arrow is orange.
- Collapsed title is uppercase with wide letter spacing.
- Expanded view has a white outer background with orange border.
- Expanded header uses the same icon/title/arrow arrangement.
- Expanded body contains peach text blocks, an orange benefits title strip, and gray list rows.
- The number area remains unchanged when expanded.
- Expanded content determines the final card height.

Pixel-perfect values are not required in the first pass, but the structure, proportions, colors, spacing, and hierarchy should closely match the reference.

---

## 13. Placeholder content

Implement three sample cards matching the references:

### Card 1

Collapsed title:

```text
NAJLEPSZA IZOLACJA CIEPLNA POJAZDÓW
```

Expanded title:

```text
SUPERIOR THERMAL INSULATION PERFORMANCE
```

### Card 2

Collapsed title:

```text
DOSKONAŁA IZOLACJA AKUSTYCZNA I WIBROIZOLACJA
```

### Card 3

Collapsed title:

```text
NAJNIŻSZA MASA I WIĘKSZA EFEKTYWNOŚĆ ENERGETYCZNA
```

For expanded body text, use the content visible in the supplied expanded-state reference as placeholder copy.

Structure the HTML so additional cards can be added by copying one `<article data-accordion-item>` block and changing:

- number;
- unique IDs;
- titles;
- body copy;
- icon path;
- ARIA labels.

No JavaScript changes should be required when adding cards.

---

## 14. Testing checklist

Verify all of the following:

1. Clicking card 1's arrow opens card 1.
2. Clicking card 2's arrow opens card 2 while card 1 remains open.
3. All sample cards can be open simultaneously.
4. Clicking card content does not close it.
5. Clicking outside the component does nothing.
6. Clicking the collapsed title does nothing.
7. Clicking `.accordion-close` closes only its own card.
8. Closing one card leaves all other cards unchanged.
9. Rapid repeated clicks do not break state.
10. Expanded height always matches content.
11. Long titles wrap without horizontal scrolling.
12. Focus is visible on arrow controls.
13. Closing returns focus to the corresponding open arrow.
14. Initialization can run more than once without duplicate listeners.
15. Two separate `[data-accordion]` roots on one page work independently.
16. Adding a fourth card requires no JavaScript changes.
17. No external network dependency is needed except final image URLs.
18. No console errors occur.
19. The component works in a plain local HTML file.
20. The component remains functional after being copied into a Tilda HTML block.

---

## 15. Acceptance criteria

The implementation is complete when:

- it uses only HTML, CSS, and vanilla JavaScript;
- all cards work independently;
- multiple cards remain open simultaneously;
- only the arrow opens a card;
- only `.accordion-close` closes a card;
- collapsed content is replaced by expanded content;
- the left number remains visible and aligned;
- card height is content-driven;
- fade-in is used without height animation;
- text content is stored in HTML;
- the code is self-contained and Tilda-safe;
- classes are properly namespaced;
- adding cards does not require JavaScript changes;
- the desktop layout closely resembles the supplied references;
- the code is clear enough for later mobile adaptation.

---

## 16. Deliverables

Codex should produce:

1. `index.html` containing the complete working component.
2. Three sample cards.
3. Embedded CSS and JavaScript in the same file for easy Tilda transfer.
4. Placeholder icon paths that are easy to replace later.
5. Brief comments marking:
   - design tokens;
   - card markup;
   - accordion state logic;
   - Tilda initialization handling.
6. A short `README.md` explaining:
   - how to preview locally;
   - how to add another card;
   - how to replace icon paths;
   - how to copy the component into Tilda.

---

## 17. Suggested initial Codex prompt

```text
Read docs/accordion-spec.md and inspect the supplied reference screenshots.
Implement the desktop version exactly according to the specification.

Start by creating a self-contained index.html with three sample cards and a short README.md.
Use only HTML, CSS, and vanilla JavaScript.
Do not implement the mobile version yet.
Do not generate the card content from JavaScript.
Do not close other cards when one card opens.
Use placeholder asset paths for icons until the final files are provided.

After implementation, review the testing checklist in the specification and fix any issues you find.
```
