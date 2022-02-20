# Integrating linting from non-language server sources with `nvim-lint`

Many languages do not have available language server support. As of Neovim 0.6, diagnostics from language servers are only one source for the generic diagnostic module. Diagnostics can be populated from other sources, just as dedicated linting programs which do not adhere to the language server protocol via small, adapter plugins or language servers.

The recommended solution is to use [nvim-lint](https://github.com/mfussenegger/nvim-lint), which is an extremely small (approximately 300 lines of code) plugin that adapts dedicated linter output format to the `vim.diagnostic` module.

## Alternatives:
### Adapter plugins

* [null-ls](https://github.com/jose-elias-alvarez/null-ls.nvim)

### Adapter language servers

* [efm-langserver](https://github.com/mattn/efm-langserver/issues)
* [diagnostic-languageserver](https://github.com/iamcco/diagnostic-languageserver)
