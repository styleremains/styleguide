# SCSS and CSS Style Guide

In general all syling is done using BEM conventions. Good sources to get
a hang of the BEM conventions are:

- http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax
- http://cssguidelin.es/#naming-conventions

## Coding Style

* Do not use the `&` to reference the parent selector when creating BEM
  elements or modifiers. Only use it for pseudo classes.

  ```scss
  # bad
  .product-summary {
    &__brand {
     /* ... */
    }
    &__title {
     /* ... */
    }
    &__key {
     /* ... */
    }
    &__value {
     /* ... */
    }
    &__value--full {
     /* ... */
    }
  }
  
  # good
  .product-summary {
     /* ... */
  }
  .product-summary__brand {
     /* ... */
  }
  .product-summary__title {
     /* ... */
  }
  .product-summary__key {
     /* ... */
  }
  .product-summary__value {
     /* ... */
  }
  .product-summary__value--full {
     /* ... */
  }
  ```
