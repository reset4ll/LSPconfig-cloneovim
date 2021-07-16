### Borders

Floating windows with borders are built-into the neovim core handler. The borders can be styled by passing in a character and highlight group. Highlight groups must be set with an autocmd to avoid being overwritten by your colorscheme.

```lua
vim.cmd [[autocmd ColorScheme * highlight NormalFloat guibg=#1f2335]]
vim.cmd [[autocmd ColorScheme * highlight FloatBorder guifg=white guibg=#1f2335]]

local border = {
      {"🭽", "FloatBorder"},
      {"▔", "FloatBorder"},
      {"🭾", "FloatBorder"},
      {"▕", "FloatBorder"},
      {"🭿", "FloatBorder"},
      {"▁", "FloatBorder"},
      {"🭼", "FloatBorder"},
      {"▏", "FloatBorder"},
}

-- LSP settings
local on_attach = function(client, bufnr)
  vim.lsp.handlers["textDocument/hover"] =  vim.lsp.with(vim.lsp.handlers.hover, {border = border})
  vim.lsp.handlers["textDocument/signatureHelp"] =  vim.lsp.with(vim.lsp.handlers.hover, {border = border})
end

-- Do not forget to use the on_attach function
require 'lspconfig'.myserver.setup { on_attach = on_attach }
```

### Completion kinds

```lua
local M = {}

M.icons = {
  Class = " ",
  Color = " ",
  Constant = " ",
  Constructor = " ",
  Enum = "了 ",
  EnumMember = " ",
  Field = " ",
  File = " ",
  Folder = " ",
  Function = " ",
  Interface = "ﰮ ",
  Keyword = " ",
  Method = "ƒ ",
  Module = " ",
  Property = " ",
  Snippet = "﬌ ",
  Struct = " ",
  Text = " ",
  Unit = " ",
  Value = " ",
  Variable = " ",
}

function M.setup()
  local kinds = vim.lsp.protocol.CompletionItemKind
  for i, kind in ipairs(kinds) do
    kinds[i] = M.icons[kind] or kind
  end
end

return M
```
### Change diagnostic symbols in the sign column (gutter)
```lua
local signs = { Error = " ", Warning = " ", Hint = " ", Information = " " }

for type, icon in pairs(signs) do
  local hl = "LspDiagnosticsSign" .. type
  vim.fn.sign_define(hl, { text = icon, texthl = hl, numhl = "" })
end
```

### Print diagnostics in status line

```lua
function PrintDiagnostics(opts, bufnr, line_nr, client_id)
  opts = opts or {}

  bufnr = bufnr or 0
  line_nr = line_nr or (vim.api.nvim_win_get_cursor(0)[1] - 1)

  local line_diagnostics = vim.lsp.diagnostic.get_line_diagnostics(bufnr, line_nr, opts, client_id)
  if vim.tbl_isempty(line_diagnostics) then return end

  local diagnostic_message = ""
  for i, diagnostic in ipairs(line_diagnostics) do
    diagnostic_message = diagnostic_message .. string.format("%d: %s", i, diagnostic.message or "")
    print(diagnostic_message)
    if i ~= #line_diagnostics then
      diagnostic_message = diagnostic_message .. "\n"
    end
  end
  vim.api.nvim_echo({{diagnostic_message, "Normal"}}, false, {})
end

vim.cmd [[ autocmd CursorHold * lua PrintDiagnostics() ]]
```
### Show line diagnostics automatically in hover window

```lua 
autocmd CursorHold,CursorHoldI * lua vim.lsp.diagnostic.show_line_diagnostics({focusable=false})
```

### Filter by severity in signcolum
See https://www.reddit.com/r/neovim/comments/mvhfw7/can_built_in_lsp_diagnostics_be_limited_to_show_a/


### Show source in diagnostics

This is useful when you're running multiple language servers

**Result:**
- Virtual Text
```typescript
const foo = "bar;    ■ eslint: Parsing error: Unterminated string constant
asdf + 2    ■ typescript: Cannot find name 'asdf'
```
- Floating Window
```
1. typescript: Unterminated string literal.
2. eslint: Parsing error: Unterminated string constant
```

**Code:**
```lua
vim.lsp.handlers["textDocument/publishDiagnostics"] =
  function(_, _, params, client_id, _)
    local config = { -- your config
      underline = true,
      virtual_text = {
        prefix = "■ ",
        spacing = 4,
      },
      signs = true,
      update_in_insert = false,
    }
    local uri = params.uri
    local bufnr = vim.uri_to_bufnr(uri)

    if not bufnr then
      return
    end

    local diagnostics = params.diagnostics

    for i, v in ipairs(diagnostics) do
      diagnostics[i].message = string.format("%s: %s", v.source, v.message)
    end

    vim.lsp.diagnostic.save(diagnostics, bufnr, client_id)

    if not vim.api.nvim_buf_is_loaded(bufnr) then
      return
    end

    vim.lsp.diagnostic.display(diagnostics, bufnr, client_id, config)
  end
```