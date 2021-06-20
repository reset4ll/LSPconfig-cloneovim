We list here some snippets to customize the LSP experience. These are too complex/arbitrary to be enabled by default.

# Range Formatting With a Motion

If your language server supports formatting a range of text (as opposed to a whole file), you can use this snippet to map a key to formatting a range with a motion.  For example, with this code, `gmip` in normal mode will format a paragraph.

```lua
function format_range_operator()
  local old_func = vim.go.operatorfunc
  _G.op_func_formatting = function()
    local start = vim.api.nvim_buf_get_mark(0, '[')
    local finish = vim.api.nvim_buf_get_mark(0, ']')
    vim.lsp.buf.range_formatting({}, start, finish)
    vim.go.operatorfunc = old_func
    _G.op_func_formatting = nil
  end
  vim.go.operatorfunc = 'v:lua.op_func_formatting'
  vim.api.nvim_feedkeys('g@', 'n', false)
end
vim.api.nvim_set_keymap("n", "gm", "<cmd>lua format_range_operator()<CR>", {noremap = true})
```

See https://github.com/neovim/neovim/issues/14680 for context.


# Peek Definition

To open the target of a `textDocument/definition` request in a floating window (as in VS Code's "Peek Definition"), you can use the following snippet:

```lua
local function preview_location_callback(_, _, result)
  if result == nil or vim.tbl_isempty(result) then
    return nil
  end
  vim.lsp.util.preview_location(result[1])
end

function PeekDefinition()
  local params = vim.lsp.util.make_position_params()
  return vim.lsp.buf_request(0, 'textDocument/definition', params, preview_location_callback)
end
```

If the server supports [`LocationLink`](https://microsoft.github.io/language-server-protocol/specification#locationLink), this will show the full target range (e.g., function body) of the definition. Other requests like `textDocument/declaration` can be "peeked" analogously.


# Clangd

## SwitchSourceHeader

nvim-lspconfig offers the ClangdSwitchSourceHeader command by default. It simply replaces the current buffer with the corresponding file. If you'd like to open the corresponding file in a new split/vsplit, you can add such commands easily:

```lua
local function switch_source_header_splitcmd(bufnr, splitcmd)
    bufnr = require'lspconfig'.util.validate_bufnr(bufnr)
    local params = { uri = vim.uri_from_bufnr(bufnr) }
    vim.lsp.buf_request(bufnr, 'textDocument/switchSourceHeader', params, function(err, _, result)
        if err then error(tostring(err)) end
        if not result then print ("Corresponding file can’t be determined") return end
        vim.api.nvim_command(splitcmd..' '..vim.uri_to_fname(result))
    end)
end

require'lspconfig'.clangd.setup {

   -----snip------

    commands = {
    	ClangdSwitchSourceHeader = {
    		function() switch_source_header_splitcmd(0, "edit") end;
    		description = "Open source/header in current buffer";
    	},
    	ClangdSwitchSourceHeaderVSplit = {
    		function() switch_source_header_splitcmd(0, "vsplit") end;
    		description = "Open source/header in a new vsplit";
    	},
    	ClangdSwitchSourceHeaderSplit = {
    		function() switch_source_header_splitcmd(0, "split") end;
    		description = "Open source/header in a new split";
    	}
    }
}
```

The commands ClangdSwitchSourceHeader* are available like normal vim commands, that is:
```
nnoremap <leader>h :ClangdSwitchSourceHeaderVSplit<CR>
```

# diagnosticls
[Auto generated config link](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#diagnosticls)

## eslint
The following is an example of integrating with eslint, it uses the **.eslinrc.js** file to identify the project root, and then uses the instance of eslint available in **node_modules**:

```lua
  local util = require "lspconfig".util

  require'lspconfig'.diagnosticls.setup{
    filetypes = {"javascript", "typescript"},
    root_dir = function(fname)
      return util.root_pattern("tsconfig.json")(fname) or
      util.root_pattern(".eslintrc.js")(fname);
    end,
    init_options = {
      linters = {
        eslint = {
          command = "./node_modules/.bin/eslint",
          rootPatterns = {".eslintrc.js", ".git"},
          debounce = 100,
          args = {
            "--stdin",
            "--stdin-filename",
            "%filepath",
            "--format",
            "json"
          },
          sourceName = "eslint",
          parseJson = {
            errorsRoot = "[0].messages",
            line = "line",
            column = "column",
            endLine = "endLine",
            endColumn = "endColumn",
            message = "[eslint] ${message} [${ruleId}]",
            security = "severity"
          },
          securities = {
            [2] = "error",
            [1] = "warning"
          }
        },
      },
      filetypes = {
        javascript = "eslint",
        typescript = "eslint"
      }
    }
  }
```

# EFM
[Auto generated config link](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#efm)

## eslint
The following is an example of integrating with eslint, it uses the **.eslinrc.js** file to identify the project root, and then uses the instance of eslint available in **node_modules**:

```lua
  local eslint = {
    lintCommand = "./node_modules/.bin/eslint -f unix --stdin --stdin-filename ${INPUT}",
    lintIgnoreExitCode = true,
    lintStdin = true
  }
  local util = require "lspconfig".util
  
  require "lspconfig".efm.setup {
    --cmd = {"efm-langserver",},
    init_options = {documentFormatting = true},
    filetypes = {"javascript", "typescript"},
    root_dir = function(fname)
      return util.root_pattern("tsconfig.json")(fname) or
      util.root_pattern(".eslintrc.js", ".git")(fname);
    end,
    settings = {
      rootMarkers = {".eslintrc.js", ".git/"},
      languages = {
        typescript = {eslint}
      }
    }
  }
```


## eslint_d
This example uses eslint_d, which is much faster. Read more [here](https://www.npmjs.com/package/eslint_d)

```lua
local eslint = {
  lintCommand = "eslint_d -f unix --stdin --stdin-filename ${INPUT}",
  lintStdin = true,
  lintFormats = {"%f:%l:%c: %m"},
  lintIgnoreExitCode = true,
  formatCommand = "eslint_d --fix-to-stdout --stdin --stdin-filename=${INPUT}",
  formatStdin = true
}

require "lspconfig".efm.setup {
  init_options = {documentFormatting = true},
  filetypes = {"javascript", "typescript"},
  root_dir = function(fname)
    return util.root_pattern("tsconfig.json")(fname) or
    util.root_pattern(".eslintrc.js", ".git")(fname);
  end,
  settings = {
    rootMarkers = {".eslintrc.js", ".git/"},
    languages = {
      javascript = {eslint},
      typescript = {eslint}
    }
  }
}
```