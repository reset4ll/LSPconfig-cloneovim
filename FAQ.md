### Should I use CoC.nvim, vim-lsc, vim-lsp, or neovim's built-in language server client? Which is best?

All clients are great. Pick whichever makes you happy. Reasons you may choose the neovim's built-in language server client:

* It's very extensible, all handlers can be overridden to customize functionality in your init.vim/lua
* Lua is a nice language to work in
* It's built into neovim

### Does nvim-lspconfig provide a language server client? Do I need it to use neovim's LSP?

No, nvim-lspconfig include *none* of the language server client implementation. It only includes:

* launching a language server when a matching filetype is detected
* Sending the correct initialization options and settings (these are two separate things in LSP parlance) during launch
* attaching new buffers you open to the currently active language server

### Why do I have to install nvim-jdtls/nvim-metals if I have nvim-lspconfig installed already? (or vice versa)

nvim-lspconfig (and neovim core) do not provide any support for custom extensions to the LSP specification. Many servers go "off-spec" and add their own functionality, which requires custom handlers. Language server specific plugins like nvim-jdtls and nvim-metals can be safely installed alongside nvim-lspconfig, and you should prefer those for their respective servers.

### Can nvim-lspconfig install language servers for me? Didn't it used to have this functionality?

Not anymore. This feature was removed, and will not be restored, because it was:

* Hard to maintain
* The source of many bug reports
* Doubled the size of the codebase

Installing language servers manually is very easy, and if not, there are additional plugins that handle this for you. See our wiki.

