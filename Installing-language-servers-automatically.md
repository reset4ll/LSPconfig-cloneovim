nvim-lspconfig provides default configuration for language servers (only). We previously had an installation feature which was:

* Hard to maintain
* The source of many bug reports
* Doubled the size of the codebase

Installing language servers manually is very easy. The installation instructions for each server are in [CONFIG.md](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md). For the vast majority of servers, this is a one-time, one-line installation ('npm i -g pyright', 'dnf install clang-tools-extra', etc.). It is preferred to use your system package manager as this will handle automatic updates for you.

If you wish to have a neovim plugin manage your server installations, there are several options:

* [williamboman/nvim-lsp-installer](https://github.com/williamboman/nvim-lsp-installer)
* [kabouzeid/nvim-lspinstall](https://github.com/kabouzeid/nvim-lspinstall)
* [nvim-lspupdate](https://github.com/alexaandru/nvim-lspupdate)

