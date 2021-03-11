We list here some snippets to customize the LSP experience. These are too complex/arbitrary to be enabled by default.

# Peek Definition

To open the target of a `textDocument/definition` request in a floating window (as in VS Code's "Peek Definition"), you can use the following snippet:

```lua
local function preview_location_callback(_, _, result)                                     
  if result == nil or vim.tbl_isempty(result) then                                         
    return nil                                                                             
  end                                                                                        
  vim.lsp.util.preview_location(result[1])                                                 
end                                                                                        
                                                                                           
function PeekDefinition()                                                                  
  local params = vim.lsp.util.make_position_params()                                       
  return vim.lsp.buf_request(0, 'textDocument/definition', params, preview_location_callback)
end
```

If the server supports [`LocationLink`](https://microsoft.github.io/language-server-protocol/specification#locationLink), this will show the full target range (e.g., function body) of the definition. Other requests like `textDocument/declaration` can be "peeked" analogously.


# Clangd

## SwitchSourceHeader

nvim-lspconfig offers the ClangdSwitchSourceHeader command by default. It simply replaces the current buffer with the corresponding file. If you'd like to open the corresponding file in a new split/vsplit, you can add such commands easily:

```
local function switch_source_header_splitcmd(bufnr, splitcmd)
    bufnr = require'lspconfig'.util.validate_bufnr(bufnr)
    local params = { uri = vim.uri_from_bufnr(bufnr) }
    vim.lsp.buf_request(bufnr, 'textDocument/switchSourceHeader', params, function(err, _, result)
        if err then error(tostring(err)) end
        if not result then print ("Corresponding file can’t be determined") return end
        vim.api.nvim_command(splitcmd..' '..vim.uri_to_fname(result))
    end)
end

require'lspconfig'.clangd.setup {

   -----snip------

    commands = {
    	ClangdSwitchSourceHeader = {
    		function() switch_source_header_splitcmd(0, "edit") end;
    		description = "Open source/header in current buffer";
    	},
    	ClangdSwitchSourceHeaderVSplit = {
    		function() switch_source_header_splitcmd(0, "vsplit") end;
    		description = "Open source/header in a new vsplit";
    	},
    	ClangdSwitchSourceHeaderSplit = {
    		function() switch_source_header_splitcmd(0, "split") end;
    		description = "Open source/header in a new split";
    	}
    }
}
```

The commands ClangdSwitchSourceHeader* are available like normal vim commands, that is:
```
nnoremap <leader>h :ClangdSwitchSourceHeaderVSplit<CR>
```
