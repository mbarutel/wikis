# Setting up tree-sitter-blade for Astronvim

Essentially, this process is required due to `tree-sitter-blade` not being a supported parser by `tree-sitter`, yet.

The process is going through adding a custom parser for `tree-sitter`.

Ultimately, this guide is for implementing this config for `Astronvim`.

Let's get started!

1. Add/Amend this config snippet into you `/lua/plugins/treesitter.lua` file.

```
return {
  {
    "nvim-treesitter/nvim-treesitter",
    opts = {
      ensure_installed = {
        "html",
        "php_only",
        "php",
        "bash",
        "blade"
      },
    },
    config = function(_, opts)
      local parser_config = require("nvim-treesitter.parsers").get_parser_configs()

      parser_config.blade = {
        install_info = {
          url = "https://github.com/EmranMR/tree-sitter-blade",
          files = { "src/parser.c" },
          branch = "main",
        },
        filetype = "blade",
      }

      vim.filetype.add({
        pattern = {
          [".*%.blade%.php"] = "blade",
        },
      })

      require("nvim-treesitter.configs").setup(opts)
    end,
  },
}
```

2. Execute this command within neovim.

```
:TSInstall blade
```

3. Create the query files for highlighting. There are 2 files we need to create & they are created by running the commands listed below. Additionally, I will add the snippets that are required to be populated within the files.

---

Command to create the file:
```
:TSEditQuery highlights blade
```

Snippet for the file created: `~/.config/nvim/after/queries/blade/highlights.scm`
```
(directive) @function
(directive_start) @function
(directive_end) @function
(comment) @comment
((parameter) @include (#set! "priority" 110)) 
((php_only) @include (#set! "priority" 110)) 
((bracket_start) @function (#set! "priority" 120)) 
((bracket_end) @function (#set! "priority" 120)) 
(keyword) @function
```

---

Command to create the file:
```
:TSEditQuery injections blade
```

Snippet for the file created: `~/.config/nvim/after/queries/blade/injections.scm`
```
((text) @injection.content
    (#not-has-ancestor? @injection.content "envoy")
    (#set! injection.combined)
    (#set! injection.language php))
```

---

That should be it!

Sources:
- [tree-sitter-blade repository](https://github.com/EmranMR/tree-sitter-blade)
- [discussion on nvim setup steps](https://github.com/EmranMR/tree-sitter-blade/discussions/19)
- [astronvim](https://astronvim.com/)
