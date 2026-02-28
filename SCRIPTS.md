---
tags: meta
---

## Toggle TODO item or convert item to todo item
```space-lua
command.define {
  name = "Toggle Todo Checkbox",
  key = "Ctrl-ö",
  run = function()
    local sel = editor.getSelection()
    local hasSelection = sel and sel.from ~= sel.to

    local function toggleLine(line)
      local indent, bullet, outercheck, checkmark, content =
        line:match("^(%s*)([%-%+%*])(%s%[([xX ])%])?%s+(.*)$")
      if not bullet then return line end

      local newLine
      if outercheck then
        if checkmark:find("[xX]") then
          newLine = indent .. bullet .. " [ ] " .. content
        else
          newLine = indent .. bullet .. " [x] " .. content
        end
      else
        newLine = indent .. bullet .. " [ ] " .. content
      end
      return newLine
    end

    if hasSelection then
      local pageText = space.readPage(editor.getCurrentPage())

      -- Expand sel.from back to the start of the first line
      local expandedFrom = sel.from
      while expandedFrom > 0 and string.sub(pageText, expandedFrom, expandedFrom) ~= "\n" do
        expandedFrom = expandedFrom - 1
      end

      -- Expand sel.to forward to the end of the last line
      local expandedTo = sel.to
      local pageLen = #pageText
      while expandedTo <= pageLen and string.sub(pageText, expandedTo, expandedTo) ~= "\n" do
        expandedTo = expandedTo + 1
      end

      local selectedText = string.sub(pageText, expandedFrom + 1, expandedTo)
      local lines = {}
      for line in (selectedText .. "\n"):gmatch("([^\n]*)\n") do
        table.insert(lines, toggleLine(line))
      end
      local newText = table.concat(lines, "\n")
      editor.replaceRange(expandedFrom, expandedTo, newText)
    else
      local line = editor.getCurrentLine()
      local newLine = toggleLine(line.text)
      if newLine ~= line.text then
        editor.replaceRange(line.from, line.to, newLine)
      end
    end
  end
}

```

### convert from TODO item to normal item
```space-lua
command.define {
  name = "Remove Todo Checkbox",
  run = function()
    local sel = editor.getSelection()
    local hasSelection = sel and sel.from ~= sel.to

    local function removeLine(line)
      local indent, bullet, outercheck, content =
        line:match("^(%s*)([%-%+%*])(%s%[[xX ]%])?%s+(.*)$")
      if not outercheck then return line end
      return indent .. bullet .. " " .. content
    end

    if hasSelection then
      local pageText = space.readPage(editor.getCurrentPage())

      -- Expand sel.from back to the start of the first line
      local expandedFrom = sel.from
      while expandedFrom > 0 and string.sub(pageText, expandedFrom, expandedFrom) ~= "\n" do
        expandedFrom = expandedFrom - 1
      end

      -- Expand sel.to forward to the end of the last line
      local expandedTo = sel.to
      local pageLen = #pageText
      while expandedTo <= pageLen and string.sub(pageText, expandedTo, expandedTo) ~= "\n" do
        expandedTo = expandedTo + 1
      end

      local selectedText = string.sub(pageText, expandedFrom + 1, expandedTo)
      local lines = {}
      for line in (selectedText .. "\n"):gmatch("([^\n]*)\n") do
        table.insert(lines, removeLine(line))
      end
      local newText = table.concat(lines, "\n")
      editor.replaceRange(expandedFrom, expandedTo, newText)
    else
      local line = editor.getCurrentLine()
      local newLine = removeLine(line.text)
      if newLine ~= line.text then
        editor.replaceRange(line.from, line.to, newLine)
      end
    end
  end
}

```

## create page

### improved Create Page - moves selected text
```space-lua
command.define {
  name = "Page: Create Page",
  run = function()
    local current = editor.getCurrentPage()
    local path = string.gsub(current, "/*[^/]+$", "")
    if path ~= "" then
      path = path .. "/"
    end
    local name = editor.prompt("Enter name of new page", path)
    if not name then
      return
    end

    -- Check for selected text
    local sel = editor.getSelection()
    if sel and sel.from ~= sel.to then
      local fullText = editor.getText()
      local selectedText = string.sub(fullText, sel.from + 1, sel.to)
      local link = "[[" .. name .. "]]"

      -- Write selected text to the new page
      space.writePage(name, selectedText)

      -- Replace selection with wiki link by splicing the full text
      local newText = string.sub(fullText, 1, sel.from)
                   .. link
                   .. string.sub(fullText, sel.to + 1)
      editor.setText(newText)

      -- Reposition cursor to end of inserted link
      editor.setSelection(sel.from + #link, sel.from + #link)
    end

    editor.navigate(name)
  end
}

```

### Create Sub-Page

```space-lua
command.define {
  name = "Page: Create Subpage",
  run = function()
    local current = editor.getCurrentPage()
    local name = editor.prompt("Enter name of new subpage", current .. "/")
    if not name then
      return
    else
      editor.navigate(name)
    end
  end
}
```
  