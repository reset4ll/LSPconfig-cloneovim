

### Can nvim-lspconfig install language servers for me? Didn't it used to have this functionality?

Not anymore. This feature was removed, and will not be restored, because it was:

* Hard to maintain
* The source of many bug reports
* Doubled the size of the codebase

Installing language servers manually is very easy, and if not, there are additional plugins that handle this for you. See our wiki.

### Filter by severity in signcolum
See https://www.reddit.com/r/neovim/comments/mvhfw7/can_built_in_lsp_diagnostics_be_limited_to_show_a/