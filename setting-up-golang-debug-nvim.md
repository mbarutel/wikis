# Setting Up Golang Debug For Nvim (Astronvim)

## This is still not proven to work

- [ ] Install this packages through Mason (Ths method is running delve directly)
  - delve
- [ ] Create a `mason-nvim-dap.lua` file and add this config

```
return {
  "jay-babu/mason-nvim-dap.nvim",
  opts = {
    handlers = {
      go = function()
        local dap = require "dap"
        dap.adapters.delve = function(callback, config)
          if config.mode == "remote" and config.request == "attach" then
            callback {
              type = "server",
              host = config.host or "127.0.0.1",
              port = config.port or "38697",
            }
          else
            print "Thir happesn"

            callback {
              type = "server",
              port = "${port}",
              executable = {
                command = "dlv",
                args = { "dap", "-l", "127.0.0.1:${port}", "--log", "--log-output=dap" },
                detached = vim.fn.has "win32" == 0,
              },
            }
          end
        end

        -- https://github.com/go-delve/delve/blob/master/Documentation/usage/dlv_dap.md
        dap.configurations.go = {
          {
            type = "delve",
            name = "Debug",
            request = "launch",
            program = "${file}",
          },
          {
            type = "delve",
            name = "Debug test", -- configuration for debugging test files
            request = "launch",
            mode = "test",
            program = "${file}",
          },
          -- works with go.mod packages and sub packages
          {
            type = "delve",
            name = "Debug test (go.mod)",
            request = "launch",
            mode = "test",
            program = "./${relativeFileDirname}",
          },
        }
      end,
    },
  },
}
```
