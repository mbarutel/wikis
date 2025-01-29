# How to set up debugging PHP in NEOVIM ~ Astro NVIM

- [] Install `php-debug-adapter` in Mason
- [] Install `xdebug` via this link (https://xdebug.org/wizard)
- [] Configure `php.ini` or create a config file for `xdebug` specifically in the `conf.d` file
- [] Configure `nvim-dap` in your `nvim` config

```
    dap.adapters.php = {
      type = 'executable',
      command = 'node',
      args = { os.getenv('HOME') .. '/.local/share/nvim/mason/packages/php-debug-adapter/extension/out/phpDebug.js' }
    }

    dap.configurations.php = {
      {
        type = 'php',
        request = 'launch',
        name = 'Listen for Xdebug',
        port = 9003
      },
      {
        name = "Launch currently open script",
        type = "php",
        request = "launch",
        program = "${file}",
        cwd = "${fileDirname}",
        port = 9003
      },
    }
```
NOTE: I don't even think that the first config option is necessary as I believe that that's already a default option
