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