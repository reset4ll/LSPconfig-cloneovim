Certain language servers require broadcasting snippet support to provide completions (vscode-html-language server).

We recommend installing [snippets.nvim](https://github.com/norcalli/snippets.nvim), a lua native plugin for neovim.

To enable snippets support for a given server with snippets.nvim:

```lua
require'snippets'.use_suggested_mappings()

local nvim_lsp = require('lspconfig')
local capabilities = vim.lsp.protocol.make_client_capabilities()
capabilities.textDocument.completion.completionItem.snippetSupport = true;

-- requires snippet support to provide completion
nvim_lsp.html.setup {
    capabilities = capabilities;
    on_attach = on_attach,
}

-- does not require snippet support, but provides some snippet completion candidates out of the box
local sumneko_root_path = vim.fn.getenv("HOME").."/.local/bin/sumneko_lua"
nvim_lsp.sumneko_lua.setup {
  cmd = { sumneko_root_path .. "/bin/"..system_name.."/lua-language-server", "-E", sumneko_root_path .. "/main.lua"};
  on_attach = on_attach,
  capabilities = capabilities;
  settings = {
      Lua = {
          runtime = {
              -- Tell the language server which version of Lua you're using (LuaJIT in the case of Neovim)
              version = 'LuaJIT',
              -- Setup your lua path
              path = vim.split(package.path, ';'),
          },
          diagnostics = {
              -- Get the language server to recognize the `vim` global
              globals = {'vim'},
          },
          workspace = {
              -- Make the server aware of Neovim runtime files
              library = {
                  [vim.fn.expand('$VIMRUNTIME/lua')] = true,
                  [vim.fn.expand('$VIMRUNTIME/lua/vim/lsp')] = true,
              },
          },
      },
  },
}
```