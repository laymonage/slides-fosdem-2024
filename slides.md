---
theme: the-unnamed
# background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## ✨ Modern editing experience for your Django models with Wagtail 🐦

  Presented at DjangoCon US 2023.
drawings:
  persist: false
transition: slide-left
title: ✨ Modern editing experience for your Django models with Wagtail 🐦
hideInToc: true
mdc: true
---

<h1 class="!text-5xl font-semibold">
✨ Modern editing experience for your Django models with Wagtail 🐦
</h1>

<p class="!opacity-90">Sage Abdullah</p>

<div class="abs-br m-6 flex gap-2">
  <a href="https://slides.laymonage.com/integrate-wagtail" target="_blank"
    class="text-xl slidev-icon-btn !py-1 opacity-50 !border-none !hover:text-white">
    slides.laymonage.com/integrate-wagtail
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
hideInToc: true
---

# About me

<div class="!text-2xl">

- Sage Abdullah / **@laymonage**
- **Wagtail Developer** at Torchbox
- Google Summer of Code 2019 student with Django
  - Cross-database **`JSONField`**
- Google Summer of Code & Outreachy mentor for Wagtail

</div>

<style>
  li {
    @apply !mb-3;
  }

  ul li:has(ul) {
    @apply !mb-2;
  }
</style>

<!--
Here is another comment.
-->

---
layout: default
hideInToc: true
---

# Outline

<Toc maxDepth="1"></Toc>

---
layout: image-right
image: ./images/wagtail.png
---

# Wagtail CMS

An open source content management system (CMS) built on Django.

The `Page` model acts as both "model" and "view". The tree structure of the model is reflected in your website's URL structure.

Wagtail gives you the power to:
- see a live preview as you edit
- save revisions
- manage live/draft versions
- moderate your content
- and much more!

---
layout: default
transition: fade
---

# Features

```mermaid
classDiagram
    class Page {
      serve_preview()
      save_revision()
      ...()
      live
      locked
      workflow_states
      ...
    }
```

---
layout: default
level: 2
---

# Features

```mermaid
classDiagram
    class Page {
      ...
    }
    PreviewableMixin <-- Page
    RevisionMixin <-- Page
    DraftStateMixin <-- Page
    LockableMixin <-- Page
    WorkflowMixin <-- Page
    class PreviewableMixin{
      serve_preview()
      ...()
    }
    class RevisionMixin{
      save_revision()
      ...()
    }
    class DraftStateMixin {
      live
      ...()
    }
    class LockableMixin {
      locked
      ...()
    }
    class WorkflowMixin {
      workflow_states
      ...()
    }
```

---
layout: default
---

# Integrating Wagtail

- Install
  ```shell
  python -m pip install wagtail
  ```
- Add to `INSTALLED_APPS`
- Add middleware to `MIDDLEWARES`
- Add URLs to `urls.py`
- Run the migrations
- Hack away!

---
layout: iframe
url: https://docs.wagtail.org/en/stable/getting_started/integrating_into_django.html
---

---
layout: default
---

# Registering your models

Wagtail primarily works with its `Page` model. For other models, you can register them as "snippets".

Add a `@register_snippet` decorator to your model.

```python
from wagtail.snippets.models import register_snippet


@register_snippet
class Person(models.Model):
    ...
```

---
layout: image-right
image: ./images/preview.png
---

# Enabling live preview

<div v-click="[0, 1]">

The preview feature comes from the `PreviewableMixin` class.

To use it, add it as a superclass to your model. Then, override the `get_preview_template` and `get_preview_context` methods.

```python
from wagtail.models import PreviewableMixin


@register_snippet
class Product(PreviewableMixin, models.Model):
    ...

    def get_preview_template(self, request, preview_mode):
        return f"products/detail.html"

    def get_preview_context(self, request, preview_mode):
        return {"product": self}
```

</div>

<div v-after>

You can define different preview modes using the `preview_modes` attribute.

```python
...
class Product(PreviewableMixin, models.Model):
    ...

    preview_modes = [
        ("index", "Index"),
        ("detail", "Detail"),
    ]

    def get_preview_template(self, request, preview_mode):
        return f"products/{preview_mode}.html"

    def get_preview_context(self, request, preview_mode):
        if preview_mode == "index":
            return {"products": [self]*20}
        return {"product": self}
```

</div>

<style>
  .slidev-vclick-hidden {
    display: none;
  }
  .grid-cols-2 {
    grid-template-columns: 4fr 3fr !important;
  }
</style>

---
preload: false
---

# Animations

Animations are powered by [@vueuse/motion](https://motion.vueuse.org/).

```html
<div
  v-motion
  :initial="{ x: -80 }"
  :enter="{ x: 0 }">
  Slidev
</div>
```

<div class="w-60 relative mt-6">
  <div class="relative w-40 h-40">
    <img
      v-motion
      :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-square.png"
      alt=""
    />
    <img
      v-motion
      :initial="{ y: 500, x: -100, scale: 2 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-circle.png"
      alt=""
    />
    <img
      v-motion
      :initial="{ x: 600, y: 400, scale: 2, rotate: 100 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-triangle.png"
      alt=""
    />
  </div>

  <div
    class="text-5xl absolute top-14 left-40 text-[#2B90B6] -z-1"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: 0, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    Slidev
  </div>
</div>

<!-- vue script setup scripts can be directly used in markdown, and will only affects current page -->
<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}
</script>

<div
  v-motion
  :initial="{ x:35, y: 40, opacity: 0}"
  :enter="{ y: 0, opacity: 1, transition: { delay: 3500 } }">

[Learn More](https://sli.dev/guide/animations.html#motion)

</div>

---

# LaTeX

LaTeX is supported out-of-box powered by [KaTeX](https://katex.org/).

<br>

Inline $\sqrt{3x-1}+(1+x)^2$

Block
$$ {1|3|all}
\begin{array}{c}

\nabla \times \vec{\mathbf{B}} -\, \frac1c\, \frac{\partial\vec{\mathbf{E}}}{\partial t} &
= \frac{4\pi}{c}\vec{\mathbf{j}}    \nabla \cdot \vec{\mathbf{E}} & = 4 \pi \rho \\

\nabla \times \vec{\mathbf{E}}\, +\, \frac1c\, \frac{\partial\vec{\mathbf{B}}}{\partial t} & = \vec{\mathbf{0}} \\

\nabla \cdot \vec{\mathbf{B}} & = 0

\end{array}
$$

<br>

[Learn more](https://sli.dev/guide/syntax#latex)

---

# Diagrams

You can create diagrams / graphs from textual descriptions, directly in your Markdown.

<div class="grid grid-cols-4 gap-5 pt-4 -mb-6">

```mermaid {scale: 0.5, alt: 'A simple sequence diagram'}
sequenceDiagram
    Alice->John: Hello John, how are you?
    Note over Alice,John: A typical interaction
```

```mermaid {theme: 'neutral', scale: 0.8}
graph TD
B[Text] --> C{Decision}
C -->|One| D[Result 1]
C -->|Two| E[Result 2]
```

```mermaid
mindmap
  root((mindmap))
    Origins
      Long history
      ::icon(fa fa-book)
      Popularisation
        British popular psychology author Tony Buzan
    Research
      On effectivness<br/>and features
      On Automatic creation
        Uses
            Creative techniques
            Strategic planning
            Argument mapping
    Tools
      Pen and paper
      Mermaid
```

```plantuml {scale: 0.7}
@startuml

package "Some Group" {
  HTTP - [First Component]
  [Another Component]
}

node "Other Groups" {
  FTP - [Second Component]
  [First Component] --> FTP
}

cloud {
  [Example 1]
}


database "MySql" {
  folder "This is my folder" {
    [Folder 3]
  }
  frame "Foo" {
    [Frame 4]
  }
}


[Another Component] --> [Example 1]
[Example 1] --> [Folder 3]
[Folder 3] --> [Frame 4]

@enduml
```

</div>

[Learn More](https://sli.dev/guide/syntax.html#diagrams)

---
src: ./pages/multiple-entries.md
hide: false
---

---
layout: center
class: text-center
---

# Learn More

[Documentations](https://sli.dev) · [GitHub](https://github.com/slidevjs/slidev) · [Showcases](https://sli.dev/showcases.html)
