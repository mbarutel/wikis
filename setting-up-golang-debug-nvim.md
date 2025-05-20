# Setting Up Golang Debug For Nvim (Astronvim)

## This is still not proven to work

- [ ] Install these 2 packages through Mason
  - delve
  - go-debug-adapter
- [ ] Create a `dap.lua` file and add this config

```
return {
  "jay-babu/mason-nvim-dap.nvim",
  opts = function(_, opts)
    -- Ensure delve is installed
    opts.ensure_installed = opts.ensure_installed or {}
    vim.list_extend(opts.ensure_installed, { "delve" })

    -- Add custom dap configs for Go
    local dap = require "dap"
    dap.configurations.go = {
      {
        type = "delve",
        name = "Debug file",
        request = "launch",
        program = "${file}",
      },
      {
        type = "delve",
        name = "Debug package",
        request = "launch",
        program = "${fileDirname}",
      },
      {
        type = "delve",
        name = "Attach",
        request = "attach",
        processId = require("dap.utils").pick_process,
        mode = "local",
      },
    }
  end,
}
```
