---
sidebar_position: 2
---

# Keyboard centric VScode

Exchange 'click-click' with 'click-clack'

I've come across many ways of using Vim within Vscode but they seem to rely on a deeper understanding of Vim and a lot of muscle memory and which also rules out a lot of Vscode functionality in favour of Vim's. This is great and all but I feel like there needs to be an interface between the two that allows one to choose which they would like to use... and this is where VSpace Code comes in and integrates them together nicely.

## Create your first Doc

Create a Markdown file at `docs/hello.md`:

```md title="docs/hello.md"
# Hello

This is my **first Docusaurus document**!
```

A new document is now available at [http://localhost:3000/docs/hello](http://localhost:3000/docs/hello).

## Configure the Sidebar

Docusaurus automatically **creates a sidebar** from the `docs` folder.

Add metadata to customize the sidebar label and position:

```md title="docs/hello.md" {1-4}
---
sidebar_label: "Hi!"
sidebar_position: 3
---

# Hello

This is my **first Docusaurus document**!
```

It is also possible to create your sidebar explicitly in `sidebars.js`:

```js title="sidebars.js"
module.exports = {
  tutorialSidebar: [
    "intro",
    // highlight-next-line
    "hello",
    {
      type: "category",
      label: "Tutorial",
      items: ["tutorial-basics/create-a-document"],
    },
  ],
};
```
