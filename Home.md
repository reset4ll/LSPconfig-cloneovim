### What is nvim-lspconfig? Does nvim-lspconfig provide a language server client? Do I need it to use neovim's LSP?

No, nvim-lspconfig include *none* of the language server client implementation. It only includes:

* launching a language server when a matching filetype is detected
* Sending the correct initialization options and settings (these are two separate things in the LSP specification) during launch
* attaching new buffers you open to the currently active language server

You can use the built-in language server client *without* nvim-lspconfig, you'll just have to write out the server configuration and start/attach clients to buffers manually (see `:help lsp`).

### Why do I have to install nvim-jdtls/nvim-metals if I have nvim-lspconfig installed already? (or vice versa)

nvim-lspconfig (and neovim core) do not provide any support for custom extensions to the LSP specification. Many servers go "off-spec" and add their own functionality, which requires custom handlers. Language server specific plugins like nvim-jdtls and nvim-metals can be safely installed alongside nvim-lspconfig, and you should prefer those for their respective servers.