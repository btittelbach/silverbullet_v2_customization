---
tags: meta
---


This is where you configure SilverBullet to your liking. See [[^Library/Std/Config]] for a full list of configuration options.

see also [[SCRIPTS]]

### load plugs

```space-lua
config.set {
  plugs = {
    "ghr:MrMugame/silversearch",
    "github:silverbulletmd/silverbullet-katex/katex.plug.js",
    "github:logeshg5/silverbullet-drawio/drawio.plug.js",
    "github:mroovers/silverbullet-text-transform/textTransform.plug.js",
    "github:joekrill/silverbullet-treeview/treeview.plug.js",
    -- Add your plugs here (https://silverbullet.md/Plugs)
    -- Then run the `Plugs: Update` command to update them
  },
  mobileMenuStyle = 'bottom-bar' -- or 'hamburger'
}
```

### configure silversearch

```space-lua
config.set {
  silversearch = {
    -- Weighs specific fields more
    weights = {
      basename = 15
      -- Also available: tags, aliases, directory, displayName, content
    },
    -- Weighs pages with specific attributes set through frontmatter more if that attribute is included in the search
    weightCustomProperties = {
      books = 10
    },
    -- Files that have been edited more recently than, will be weighed more. Options are "day", "week", "month" or "disabled"
    recencyBoost = "week",
    -- Rank specific folders down
    downrankedFoldersFilters = {"Library/"},
    -- Normalize diatrics in queries and search terms. Words like "brûlée" or "žluťoučký" will be indexed as "brulee" and "zlutoucky".
    ignoreDiacritics = true,
    -- Similar to `ignoreDiacritics` but for arabic diatritics
    ignoreArabicDiacritics = false,
    -- Breaks urls down into searchable words
    tokenizeUrls = true,
    -- Breaks words seperated with camel case into searchable words
    splitCamelCase = true,
    -- Increases the fuzziness of the full-text search, options are "0", "1", "2"
    fuzziness = "1",
    -- Puts newlines into the excerpts as opposed to rendering it as one continous string
    renderLineReturnInExcerpts = true
  }
}
```

### Make Cursor non blinking for epaper so it drains less battery

```space-style
.cm-cursor, .cm-dropCursor {
    --editor-caret-color: #eb75b1aa;
    border-left: 0.5ch solid var(--editor-caret-color) !important;
}
.cm-cursorLayer { animation-duration: 0ms !important; }
```


### mobile toolbar


```space-lua
-- Defines a new button that forces your UI into read-only mode
actionButton.define {
  icon = "sidebar",
  description = "Toggle Tree View",
  mobile = false,
  run = function()
    editor.invokeCommand("Tree View: Toggle")
  end
}

actionButton.define {
  icon = 'file-plus',
  description = 'Create Page',
  run = function()
    editor.invokeCommand("Page: Create Page")
  end
}
actionButton.define {
  icon = "md-format-bold",
  description = "Bold",
  run = function()
    editor.invokeCommand("Text: Bold")
  end
}
actionButton.define {
  icon = "md-format-italic",
  description = "Italic",
  run = function()
    editor.invokeCommand("Text: Italic")
  end
}
actionButton.define {
  icon = "md-format-strikethrough",
  description = "Strikethrough",
  run = function()
    editor.invokeCommand("Text: Strikethrough")
  end
}
actionButton.define {
  icon = "md-format-list-bulleted",
  description = "List",
  run = function()
    editor.invokeCommand("Text: Listify Selection")
  end
}
actionButton.define {
  icon = "md-format-list-numbered",
  description = "List",
  run = function()
    editor.invokeCommand("Text: Number Listify Selection")
  end
}
actionButton.define {
  icon = "check-square",
  description = "Task",
  run = function()
    editor.invokeCommand("Toggle Todo Checkbox")
  end
}
actionButton.define {
  icon = "md-format-indent-decrease",
  description = "Dedent",
  run = function()
    editor.invokeCommand("Outline: Move Left")
  end
}
actionButton.define {
  icon = "md-format-indent-increase",
  description = "Indent",
  run = function()
    editor.invokeCommand("Outline: Move Right")
  end
}
actionButton.define {
  icon = "md-link",
  description = "Link",
  run = function()
    editor.invokeCommand("Text: Link Selection")
  end
}
actionButton.define {
  icon = "rotate-ccw",
  description = "Undo",
  run = function()
    editor.invokeCommand("Editor: Undo")
  end
}

```

### make mobile toolbar into a bottom bar on mobile and make it scrollable if there are too many buttons to fit

```space-style
@media only screen and (max-width: 768px) {
  /* Style the menu as a bottom bar */
  #sb-top .sb-actions.bottom-bar {
    position: fixed;
    bottom: 0;
    left: 0;
    padding: 10px 0;
    background: var(--top-background-color);
    width: 100vw;
    box-shadow: 0px 4px 8px black;
    justify-content: flex-start;
    overflow-x:scroll;
    cursor:grab;
    scrollbar-width:none;
    flex-wrap: nowrap;
    height:1.4rem;
    white-space: nowrap;
    display: flex;
    overflow-y: hidden;
    -webkit-overflow-scrolling: touch; /* smooth momentum scrolling on iOS */
    scrollbar-width: none;            /* Firefox */
    -ms-overflow-style: none;
  }
  #sb-top .sb-actions.bottom-bar button {
    padding: 1.1ex;
    margin: 0;
    height: unset;
    width: unset;
  }
  #sb-top .sb-actions.bottom-bar button svg {
    margin-bottom: -0.2rem;
    height: 1.3rem;
  }
}
```




