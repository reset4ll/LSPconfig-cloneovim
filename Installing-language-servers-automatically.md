### Can nvim-lspconfig install language servers for me? Didn't it used to have this functionality?

nvim-lspconfig is as it sounds, a plugin that provides default configuration for language servers (only). The installation feature was removed, and will not be restored, because it was:

* Hard to maintain
* The source of many bug reports
* Doubled the size of the codebase

Installing language servers manually is very easy, and if not, there are additional plugins that handle this for you:

* [kabouzeid/nvim-lspinstall](https://github.com/kabouzeid/nvim-lspinstall)
* [anott03/nvim-lspinstall](https://github.com/anott03/nvim-lspinstall)
* [nvim-lspupdate](https://github.com/alexaandru/nvim-lspupdate)
* [williamboman/nvim-lsp-installer](https://github.com/williamboman/nvim-lsp-installer)