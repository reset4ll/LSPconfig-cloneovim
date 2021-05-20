To configure a custom/private server, just 

1. load the lspconfig module: `local lspconfig = require('lspconfig')`,
2. Define the config: `lspconfig.configs.foo_lsp = { … }`
3. Call `setup()`: `lspconfig.foo_lsp.setup{}`

```lua
local lspconfig = require'lspconfig'
local configs = require'lspconfig/configs'
-- Check if it's already defined for when reloading this file.
if not lspconfig.foo_lsp then
  configs.foo_lsp = {
    default_config = {
      cmd = {'/home/ashkan/works/3rd/lua-language-server/run.sh'};
      filetypes = {'lua'};
      root_dir = function(fname)
        return lspconfig.util.find_git_ancestor(fname) or vim.loop.os_homedir()
      end;
      settings = {};
    };
  }
end
lspconfig.foo_lsp.setup{}
```
