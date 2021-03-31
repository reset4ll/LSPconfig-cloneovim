Nvim-lspconfig is meant to implement basic configuration and settings for most language servers. The beauty of the language server protocol, is most language servers should expose their entire functionality without any special per-server treatment.

Some servers send requests outside of the language server protocol specification to extend the functionality of the protocol. Eclipse JDTLS is one such example. An ecosystem of per language plugins is forming around neovim's core implementation to provide an enhanced experience compared to nvim-lspconfig. We recommend you install these plugins if you are using the language in question. Here are some recommendations:

* [nvim-jdtls](https://github.com/mfussenegger/nvim-jdtls) 
* [nvim-metals](https://github.com/scalameta/nvim-metals)
* [flutter-tools.nvim](https://github.com/akinsho/flutter-tools.nvim)