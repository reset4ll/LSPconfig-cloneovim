# Further Integration Examples

The integration examples listed [here](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#) don't gives specific examples of configuring, as some of the language servers are used for general linting / prettier integration this page aims to gives some examples of for these.

# diagnosticls
[Auto generated config link](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#diagnosticls)

## eslint
The following is an example of integrating with eslint, it uses the **.eslinrc.js** file to identify the project root, and then uses the instance of eslint available in **node_modules**:

```
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

```
  local eslint = {
    lintCommand = "./node_modules/.bin/eslint -f unix --stdin --stdin-filename ${INPUT}",
    lintIgnoreExitCode = true,
    lintStdin = true
  }
  
  require "lspconfig".efm.setup {
    --cmd = {"efm-langserver",},
    init_options = {documentFormatting = true},
    filetypes = {"javascript", "typescript"},
    root_dir = function(fname)
      return util.root_pattern("tsconfig.json")(fname) or
      util.root_pattern(".eslintrc.js", ".git")(fname);
    end,
    init_options = {documentFormatting = true},
    settings = {
      rootMarkers = {".eslintrc.js", ".git/"},
      languages = {
        typescript = {eslint}
      }
    }
  }
```