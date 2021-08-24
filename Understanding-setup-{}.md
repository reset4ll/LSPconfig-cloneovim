The principal way of enabling a language server with lspconfig is the `setup {}` call. Setup has two primary functions:
* Pass configuration options that are cached for a given language server (overriding the defaults)
* Watch for buffers of a filetype that match a configured language server, launch, and attach a language server to said buffer (using a FileType autocommand)

`setup` should only be invoked once per language server.

The main keys users will typically pass into `setup` are `settings` `on_init`, and `on_attach`.

### settings

The `settings` table is sent to the language server via a `workspace/didChangeConfiguration`notification from the client. `lspconfig` sends this notification for you automatically during initialization of the language server using the cached `settings` table you pass to `setup{}`.

In most cases, the default settings are correct. If you would like to override the settings you can see available options in [CONFIG.md](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md), these are auto-generated from the `package.json` used by the vscode plugin leveraging the language server, if available. These are the corresponding entries of the nested settings table that you must put in settings to override that option).

For [pyright](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#pyright) as an example, the `pyright.disableLanguageServices` option listed in the drop down settings menu in [CONFIG.md](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#pyright) can be sent as follows:

```lua
require('lspconfig').pyright.setup {
  settings = {
    pyright = {
      disableLanguageServices = true,
    },
  },
}
```





