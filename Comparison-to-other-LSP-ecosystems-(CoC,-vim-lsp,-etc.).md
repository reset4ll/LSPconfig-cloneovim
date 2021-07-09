# What is included in the built-in client?

The built-in language server client implements the language server protocol as the [specification](https://microsoft.github.io/language-server-protocol/specifications/specification-3-17/#textDocument_signatureHelp) intends. The client is extensible, so plugins are free (and encouraged) to implement support for custom language.

Client-side functionality such as auto-completion and auto-pairs are not part of the LSP specification, and are not built into neovim. Snippets are provided by certain language servers, and the built-in omnifunc has primitive snippet support. These are generic client capabilities that are broader than the language server protocol, but often can use language servers as an additional source. The following plugins provide good integration with the built-in client:

* [nvim-compe](https://github.com/hrsh7th/nvim-compe): auto-completion
* [LuaSnip](https://github.com/L3MON4D3/LuaSnip): snippets support
* [nvim-autopairs](https://github.com/windwp/nvim-autopairs): auto-pairs


# Should I use CoC.nvim, vim-lsc, vim-lsp, or neovim's built-in language server client?

All clients are terrific and have amazing communities of contributors. 

**Reasons you may choose the neovim's built-in language server client:**

* It's very extensible
  * all UI elements and behavior can be styled to your liking by overriding a handler in your init.vim/init.lua
* It's built into neovim
* Lua is a nice language to work in
* LuaJIT is an extremely fast tracing interpreter
* The client leverages libuv's event loop for fast asynchronous communication
* You can mix and match snippets, auto-pairs, and auto-completion plugins
* There are no dependencies for running the built-in client
  * many servers are written in javasript and will require node.js
  
**Reasons you might prefer CoC.nvim:**

* CoC.nvim manages it's own plugins (snippets, fuzzy searchers, lists)
  * the built-in client does not need to be installed
  * users will typically install lspconfig + an autocomplete extension + a snippets extension via their choice of package manager
* CoC.nvim is an older, more established plugin
* CoC.nvim uses a fork of vscode's tsserver extension for typescript/javascript
  * tsserver does not implement the LSP specification yet (there are plans to do so)
  * the vscode extension uses it's own custom interface to tsserver
  * nvim-lspconfig provides support for the theia-IDE [https://github.com/theia-ide/typescript-language-server] wrapper around tsserver
  * For now, CoC.nvim will provide a closer experience to vscode's typescript extension than nvim-lspconfig + theia + null-ls
* CoC provides functionality that is unrelated to language server support (coc-explorer, coc-pairs, coc-lists)
* CoC.nvim has a dedicated plugin (and maintainer) per language
  * nvim-lspconfig supports a large number of language servers, but doesn't implement off-spec functionality like CoC does for each one
  * there are an increasing number of plugins built around the built-in LSC (nvim-jdtls, nvim-metals, flutter-tools.nvim)

### Does nvim-lspconfig provide a language server client? Do I need it to use neovim's LSP?

No, nvim-lspconfig include *none* of the language server client implementation. It only includes:

* launching a language server when a matching filetype is detected
* Sending the correct initialization options and settings (these are two separate things in the LSP specification) during launch
* attaching new buffers you open to the currently active language server

You can use the built-in language server client *without* nvim-lspconfig, you'll just have to write out the server configuration and start/attach clients to buffers manually (see `:help lsp`).

### Why do I have to install nvim-jdtls/nvim-metals if I have nvim-lspconfig installed already? (or vice versa)

nvim-lspconfig (and neovim core) do not provide any support for custom extensions to the LSP specification. Many servers go "off-spec" and add their own functionality, which requires custom handlers. Language server specific plugins like nvim-jdtls and nvim-metals can be safely installed alongside nvim-lspconfig, and you should prefer those for their respective servers.
