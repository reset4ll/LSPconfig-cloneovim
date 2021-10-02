# Autocompletion (not built-in) vs. completion (built-in)
Neovim does not support built-in autocompletion. As mentioned in the readme, you can bind the completion results to omnifunc for on-demand completion. To use autocompletion, please use an external plugin. We recommend [nvim-cmp](https://github.com/hrsh7th/nvim-cmp/), the successor/rewrite of [nvim-compe](https://github.com/hrsh7th/nvim-compe).

Please note, **autocompletion is expensive**. In order to provide completion, text must be synchronized on each completion request, and autocompletion plugins often send multiple completion requests per second to the language server as you type. If you notice slowdowns, the most likely candidate is your autocompletion plugin, or the language server which is bottlenecking it.

## nvim-cmp

For installing nvim-cmp, with autocompletion support for snippets/LSP, you can follow the below snippet. Note, this does not include your server configuration:

```lua
local use = require('packer').use
require('packer').startup(function()
  use 'neovim/nvim-lspconfig' -- Collection of configurations for built-in LSP client
  use 'hrsh7th/nvim-cmp' -- Autocompletion plugin
  use 'hrsh7th/cmp-nvim-lsp' -- LSP source for nvim-cmp
  use 'saadparwaiz1/cmp_luasnip' -- Snippets source for nvim-cmp
  use 'L3MON4D3/LuaSnip' -- Snippets plugin
end)

-- Set completeopt to have a better completion experience
vim.o.completeopt = 'menuone,noselect'

-- luasnip setup
local luasnip = require 'luasnip'

-- nvim-cmp setup
local cmp = require 'cmp'
cmp.setup {
  snippet = {
    expand = function(args)
      require('luasnip').lsp_expand(args.body)
    end,
  },
  mapping = {
    ['<C-p>'] = cmp.mapping.select_prev_item(),
    ['<C-n>'] = cmp.mapping.select_next_item(),
    ['<C-d>'] = cmp.mapping.scroll_docs(-4),
    ['<C-f>'] = cmp.mapping.scroll_docs(4),
    ['<C-Space>'] = cmp.mapping.complete(),
    ['<C-e>'] = cmp.mapping.close(),
    ['<CR>'] = cmp.mapping.confirm {
      behavior = cmp.ConfirmBehavior.Replace,
      select = true,
    },
    ['<Tab>'] = function(core, fallback)
      if vim.fn.pumvisible() == 1 then
        vim.fn.feedkeys(vim.api.nvim_replace_termcodes('<C-n>', true, true, true), 'n')
      elseif luasnip.expand_or_jumpable() then
        vim.fn.feedkeys(vim.api.nvim_replace_termcodes('<Plug>luasnip-expand-or-jump', true, true, true), '')
      else
        cmp.mapping.complete()(core, fallback)
      end
    end,
    ['<S-Tab>'] = function(fallback)
      if vim.fn.pumvisible() == 1 then
        vim.fn.feedkeys(vim.api.nvim_replace_termcodes('<C-p>', true, true, true), 'n')
      elseif luasnip.jumpable(-1) then
        vim.fn.feedkeys(vim.api.nvim_replace_termcodes('<Plug>luasnip-jump-prev', true, true, true), '')
      else
        fallback()
      end
    end,
  },
  sources = {
    { name = 'nvim_lsp' },
    { name = 'luasnip' },
  },
}
```

## Auto-import

The above snippet maps the necessary confirm on enter mapping to use auto-import. An example to show how this works:

1. Make sure tsserver is installed according to the lspconfig wiki, and if you want, use our [autocompletion example init.lua](https://github.com/mjlbach/defaults.nvim/blob/master/init.lua).
2. `mkdir test && cd test && npm init`
3. Follow the prompts to create a project
3. `npm install lodash --save`
4. `npm install @types/lodash --save-dev`
5. `echo import { } from "lodash" >> index.js`
6. `nvim index.js`
7. Check the language server is running with `:LspInfo`
8. type `debounce` on line 2 and hit `enter` (`<CR>`)

Note: This currently does not work with typescript server (theia) on Windows due to an upstream bug in theia, see https://github.com/theia-ide/typescript-language-server/issues/135