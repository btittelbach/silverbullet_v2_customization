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
## search style sheet

```space-style
/* ------Theme Color Variables-----*/

/* Dark theme colors */
html[data-theme="dark"] {
  --editor-panels-bottom-color: #eee;
  --editor-panels-border-color: #eee6;
  --editor-panels-bottom-background-color: #333c;
  --editor-panels-bottom-textfield-background: #777;
}

/* Light theme colors */
html[data-theme="light"] {
  --editor-panels-bottom-color: #000;
  --editor-panels-border-color: #333a;
  --editor-panels-bottom-background-color: #ccca;
  --editor-panels-bottom-textfield-background: #eee;
}


/*------ Editor Container Styling------*/

/* Main editor height */
#sb-main .cm-editor {
  height: 100%;
}

#sb-main .cm-editor .cm-scroller {
  padding-bottom: 15em;
}

/* Panels container */
#sb-editor .cm-panels {
  max-width: 730px;
  width: 90%;
  border-radius: 15px;
  justify-content: center;
  height: auto ;
  padding-bottom: 0 ;
  border: 1px solid var(--editor-panels-border-color);
  backdrop-filter: blur(10px);
  background-color: var(--editor-panels-bottom-background-color);
  z-index: 10;
}


/*--------Bottom Panel Elements-------*/

#sb-editor .cm-panels-bottom {
  position: absolute ;
  bottom: 15px !important;
  left: 15px ;
  top: auto ;
  right: auto ; 

}

/*------ Search Panel Styling ------*/

/* Close button */
.ͼ1 .cm-panel.cm-search [name="close"] {
  top: 3px;
  right: 3px;
  padding-left: 7px;
  padding-right: 7px;
  border-radius: 100%;
  background-color: var(--editor-panels-bottom-background-color);
  border: 1px solid var(--editor-panels-border-color);
}
/* Close button hover */
.ͼ1 .cm-panel.cm-search [name="close"]:hover {
  background-color: #777;
}

/*  Buttons and Textfields (.ͼ2) */

/* Checkbox labels */
.ͼ1 .cm-panel.cm-search label {
  font-size: 50% ;
}

/* Buttons */
.ͼ2 .cm-button {
  font-size: 80%;
  font-weight: 600;
  border-radius: 7px;
  color: var(--editor-panels-bottom-color);
  background-image: none;
  border: 1px solid var(--editor-panels-border-color);
  background-color: var(--editor-panels-bottom-textfield-background);
}

.ͼ2 .cm-button:hover {background-color: var(--editor-panels-bottom-background-color);
}


/* Textfields */
.ͼ2 .cm-textfield {
  font-size: 80%;
  color: var(--editor-panels-bottom-color);
  background-color: var(--editor-panels-bottom-textfield-background);
  border-radius: 10px;
  border: 1px solid var(--editor-panels-border-color);
}


/*--------- Mobile Styling --------*/
@media (max-width: 600px) {
  #sb-editor .cm-panels-bottom {
    width: 95% ;
    position: absolute ;
    left: 0 ;
    right: 0 ;
    bottom: 10px ;
    margin: 0 auto ;
    display: flex !important;
    justify-content: center ;
    align-items: flex-start ;
  }

  .cm-panel {
    position: relative ;
    margin: 0 auto ;
    padding: 10px !important;
    display: grid;
    width: 100%;
    grid-template-columns: 1fr 1fr 1fr ;
    gap: 2px ;
    grid-template-areas:
      "search search search"
      "ch_case ch_re ch_word"
      "b_all b_prev b_next"
      "replace replace replace"
      "b_repl b_repl b_replA";
    align-items: center ;
    justify-content: center ;
    box-sizing: border-box !important;
  }

/* Grid area assignments for form elements */
  .cm-panels input[name="search"] { grid-area: search; }
  .cm-panels input[name="replace"] { grid-area: replace; }
  
  label:has(input[name="case"]) { grid-area: ch_case; }
  label:has(input[name="re"]) { grid-area: ch_re; }
  label:has(input[name="word"]) { grid-area: ch_word; }

  .cm-panels .cm-button[name="next"] { grid-area: b_next; }
  .cm-panels .cm-button[name="prev"] { grid-area: b_prev; }
  .cm-panels .cm-button[name="select"] { grid-area: b_all; }
  
  .cm-panels .cm-button[name="replace"] { grid-area: b_repl; }
  .cm-panels .cm-button[name="replaceAll"] { grid-area: b_replA; }

/* Close button adjustments for mobile */
  .cm-panel button[name="close"] {
    position: absolute !important;
    top: -30px !important;
    right: -5px !important;
    font-size: 15px !important;
    cursor: pointer !important;
  }
}
```



