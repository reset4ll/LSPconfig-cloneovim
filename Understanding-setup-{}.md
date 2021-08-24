The principal way of enabling a language server with lspconfig is the `setup {}` call. Setup has two primary functions:
* Pass configuration options that are cached for a given language server (overriding the defaults)
* Watch for buffers of a filetype that match a configured language server, launch, and attach a language server to said buffer (using a FileType autocommand)

`setup` should only be invoked once per language server. It takes the same `{config}` table as `:help vim.lsp.start_client()`, as it's main role is to cache and pass this table to `start_client()`. In addition to the keys shared with `start_client`, it takes the following keys:
* `root_dir`
* `name`
* `filetypes`
* `autostart`
* `on_new_config`

The main keys users will (optionally) pass into `setup` to override the defaults are `settings` `on_init`, and `on_attach`.

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

### on_attach

The purpose of the on_attach callback is to run a lua callback after the language server succesfully attaches to a given buffer. This lets you conditionally map keybindings, enable autocompletion, set buffer options, etc. based on whether or not the language server is active in your buffer. Users typically use this to map keybindings to the client as seen in the [example keybindings](https://github.com/neovim/nvim-lspconfig#keybindings-and-completion) in the readme.




