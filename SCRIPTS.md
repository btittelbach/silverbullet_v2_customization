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
      local indent, bullet, outercheck, checkmark, content = line:match("^(%s*)([%-%+%*])(%s%[([xX ])%])%s+(.*)$")

      if not outercheck then
        -- try without checkmark '?' test
        indent, bullet, content = line:match("^(%s*)([%-%+%*])%s+(.*)$")
      end

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
      -- Check if line is empty (contains only whitespace) and no selection
      local isEmptyLine = line.text:match("^%s*$")

      if isEmptyLine then
        -- Add todo item with bullet and space
        local newLine = "- [ ] "
        editor.replaceRange(line.from, line.to, newLine)
        -- Move cursor to the end of the line
        editor.setSelection(line.from + #newLine, line.from + #newLine)
      else
        local newLine = toggleLine(line.text)
        if newLine ~= line.text then
          editor.replaceRange(line.from, line.to, newLine)
        end
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

    local function removeCheckboxFromLine(linetext)
      --local indent, bullet, outercheck, content = linetext:match("^(%s*)([%-%+%*])(%s%[[xX ]%])?%s+(.*)$")
      local indent, bullet, outercheck, content = linetext:match("(%s*)([%-%+%*])(%s%[[xX ]%])(.*)")
      if not outercheck then return linetext end
      return indent .. bullet .. content
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
        table.insert(lines, removeCheckboxFromLine(line))
      end
      local newText = table.concat(lines, "\n")
      editor.replaceRange(expandedFrom, expandedTo, newText)
    else
      local line = editor.getCurrentLine()
      local newLine = removeCheckboxFromLine(line.text)
      if newLine ~= line.text then
        editor.replaceRange(line.from, line.to, newLine)
      end
    end
  end
}

```

## create page

### improved New Page - moves selected text

```space-lua


helper_make_frontmatter_today = function(name)
    -- create frontmatter
    local fmatter = "---\ndate: ".. os.date("%Y-%m-%d") .. "\ntags:\n---\n\n"
    return fmatter
end
      
helper_move_selected_text_to_page_and_nav = function(name)

    -- Check for selected text
    local sel = editor.getSelection()
    -- if sel and sel.from ~= sel.to then -- this does not insert link when no text is selected
    if sel then -- this always inserts link at cursor
      local fullText = editor.getText()
      local selectedText = string.sub(fullText, sel.from + 1, sel.to)
      local link = "[[" .. name .. "]]"

      -- Write selected text to the new page
      space.writePage(name, helper_make_frontmatter_today() .. selectedText)

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

```


### improved New Page - moves selected text
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

    helper_move_selected_text_to_page_and_nav(name)
  end
}

```
### Create Subpage
```space-lua

command.define {
  name = "Page: Create Subpage",
  run = function()
    local current = editor.getCurrentPage()
    local name = editor.prompt("Enter name of new subpage", current .. "/")
    if not name then
      return
    end
    helper_move_selected_text_to_page_and_nav(name)
  end
}
```

  ### Create Subpage with Date

  ```space-lua

command.define {
  name = "Page: Create Todays Subpage",
  run = function()
    local current = editor.getCurrentPage()
    local name = editor.prompt("Enter name of new subpage", current .. "/" .. os.date("%Y-%m-%d") )
    if not name then
      return
    end
    helper_move_selected_text_to_page_and_nav(name)
  end
}
```


## Insert Frontmatter into current Page

  ```space-lua

command.define {
  name = "Insert Frontmatter",
  run = function()

    -- read the current page text
    local text = editor.getText()
  
    -- a frontmatter block must start at the very first character with "---"
    if string.startsWith(text, "---") then
      -- already has frontmatter; do nothing
      return
    end
  
    -- update the editor (preserve cursor) and save
    editor.setText(helper_make_frontmatter_today() .. text, false)
    editor.save()
    
  end
}
```


## duplicate line

```space-lua
command.define {
  name = "Editor: Duplicate Line",
  key = "Shift-Ctrl-d",
  run = function()
    local line = editor.getCurrentLine()
    -- Insert a newline + copy of the current line right after the line ends
    editor.insertAtPos("\n" .. line.text, line.to)
    -- Move cursor to the end of the newly duplicated line
    editor.moveCursor(line.to + 1 + #line.text, false)
  end
}
```

## sort topics in reverse alphabetical order

```space-lua
-- Sort Topics: Reverse Alphabetical Order
-- Recursively sorts headings (and their content) at every nesting level
-- in reverse alphabetical order (Z→A, case-insensitive).
-- Works on the full page, or only on selected text if a selection exists.

--- Returns the heading level (number of leading &#x27;#&#x27;), or 0 if not a heading.
local function getHeadingLevel(line)
  local hashes = string.match(line, "^(#+)%s")
  if hashes then
    return #hashes
  end
  return 0
end

--- Finds the minimum heading level present in a list of lines (0 if none).
local function minHeadingLevel(lines)
  local min = 9999999999
  for _, line in ipairs(lines) do
    local lvl = getHeadingLevel(line)
    if lvl > 0 and lvl < min then
      min = lvl
    end
  end
  if min == 9999999999 then return 0 end
  return min
end

--- Recursively sort topics in `lines` (a table of strings) in reverse
--- alphabetical order. Topics are defined by headings at `level` depth.
--- Content under each heading (including deeper sub-headings) travels
--- with its parent heading and is itself recursively sorted.
local function sortTopicsLines(lines)
  local baseLvl = minHeadingLevel(lines)
  if baseLvl == 0 then
    -- No headings found — nothing to sort
    return lines
  end

  -- Split lines into:
  --   preamble  : lines before the first heading at baseLvl
  --   sections  : list of { heading=string, body=lines[] }
  local preamble = {}
  local sections = {}
  local currentSection = nil
  local inPreamble = true

  for _, line in ipairs(lines) do
    local lvl = getHeadingLevel(line)
    if lvl == baseLvl then
      -- Start a new top-level section
      inPreamble = false
      if currentSection then
        table.insert(sections, currentSection)
      end
      currentSection = { heading = line, body = {} }
    elseif inPreamble then
      table.insert(preamble, line)
    else
      -- Belongs to the current section&#x27;s body
      table.insert(currentSection.body, line)
    end
  end
  -- Don&#x27;t forget the last section
  if currentSection then
    table.insert(sections, currentSection)
  end

  -- Recursively sort each section&#x27;s body
  for _, sec in ipairs(sections) do
    sec.body = sortTopicsLines(sec.body)
  end

  -- Sort sections in reverse alphabetical order by heading title
  -- Strip leading &#x27;#&#x27; and whitespace for comparison
  table.sort(sections, function(a, b)
    local rawA = string.match(a.heading, "^#+%s*(.*)")
    local rawB = string.match(b.heading, "^#+%s*(.*)")
    local titleA = string.lower(rawA or a.heading)
    local titleB = string.lower(rawB or b.heading)
    return titleA > titleB
  end)

  -- Reassemble
  local result = {}
  for _, line in ipairs(preamble) do
    table.insert(result, line)
  end
  for _, sec in ipairs(sections) do
    table.insert(result, sec.heading)
    for _, bodyLine in ipairs(sec.body) do
      table.insert(result, bodyLine)
    end
  end

  return result
end

--- Split a string into a table of lines, preserving empty lines.
--- Handles both \n and \r\n line endings.
local function splitLines(text)
  local lines = {}
  -- Normalize CRLF → LF
  text = string.gsub(text, "\r\n", "\n")
  -- Remove a single trailing newline so we don&#x27;t create a spurious blank entry
  text = string.gsub(text, "\n$", "")
  for line in string.gmatch(text .. "\n", "([^\n]*)\n") do
    table.insert(lines, line)
  end
  return lines
end

--- Join a table of lines back into a string (with trailing newline).
local function joinLines(lines)
  return table.concat(lines, "\n") .. "\n"
end

--- Main command
command.define {
  name = "Topics: Sort Reverse Alphabetical",
  run = function()
    local sel      = editor.getSelection()
    local fullText = editor.getText()
    local hasSelection = sel.from ~= sel.to

    local inputText, startPos, endPos

    if hasSelection then
      -- Lua string.sub is 1-indexed; sel.from/to are 0-indexed byte offsets
      inputText = string.sub(fullText, sel.from + 1, sel.to)
      startPos  = sel.from
      endPos    = sel.to
    else
      inputText = fullText
      startPos  = 0
      endPos    = #fullText
    end

    local lines  = splitLines(inputText)
    local sorted = sortTopicsLines(lines)
    local result = joinLines(sorted)

    if result ~= inputText then
      editor.replaceRange(startPos, endPos, result)
      editor.flashNotification("Topics sorted (reverse A–Z) ✓")
    else
      editor.flashNotification("Topics already in reverse order — nothing changed.")
    end
  end
}
```

