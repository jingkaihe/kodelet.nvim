# kodelet.nvim

A Neovim plugin for deep integration with [Kodelet](https://github.com/jingkaihe/kodelet) - enabling seamless context sharing, code selections, LSP diagnostics, and feedback messaging between Neovim and Kodelet sessions.

## Features

- **Easy Attachment** - Tab completion to browse and attach to Kodelet conversations
- **Context Sharing** - Automatically share open files and project structure
- **Diagnostics Integration** - Share LSP diagnostics (errors, warnings, hints)
- **Code Selection** - Send specific code snippets for focused discussion
- **Feedback System** - Send messages to running Kodelet sessions
- **Auto-Updates** - Context updates automatically on buffer changes

## Prerequisites

- Neovim ≥ 0.8.0
- [Kodelet](https://github.com/jingkaihe/kodelet) installed and available in PATH
- LSP configured (optional, for diagnostics integration)

## Installation

### Using [lazy.nvim](https://github.com/folke/lazy.nvim) (Recommended)

**Basic setup:**

```lua
{
  "jingkaihe/kodelet.nvim",
  event = "VeryLazy",
  config = function()
    require("kodelet").setup({
      -- Add any custom configuration here
    })
  end,
}
```

**With keymappings and which-key integration:**

```lua
{
  "jingkaihe/kodelet.nvim",
  event = "VeryLazy",
  keys = {
    { "<leader>ka", "<cmd>KodeletAttach<cr>", desc = "Attach to conversation" },
    { "<leader>kp", "<cmd>KodeletAttachSelect<cr>", desc = "Attach with picker" },
    { "<leader>kf", "<cmd>KodeletFeedback<cr>", desc = "Send feedback" },
    { "<leader>kd", "<cmd>KodeletDetach<cr>", desc = "Detach" },
    { "<leader>kc", "<cmd>KodeletClearContext<cr>", desc = "Clear context" },
    { "<leader>kx", "<cmd>KodeletClearSelection<cr>", desc = "Clear selection" },
    { "<leader>kt", "<cmd>KodeletStatus<cr>", desc = "Show status" },
    -- Visual mode mapping for selection
    { "<leader>ks", ":'<,'>KodeletSendSelection<cr>", mode = "v", desc = "Send selection" },
  },
  init = function()
    -- Optional: Add which-key group
    require("which-key").add({
      { "<leader>k", group = "kodelet", icon = "🤖" },
    })
  end,
  config = function()
    require("kodelet").setup({
      -- Add any custom configuration here
    })
  end,
}
```

### Using [packer.nvim](https://github.com/wbthomason/packer.nvim)

```lua
use {
  'jingkaihe/kodelet.nvim',
  config = function()
    require('kodelet').setup()
  end
}
```

### Using [vim-plug](https://github.com/junegunn/vim-plug)

```vim
Plug 'jingkaihe/kodelet.nvim'

lua << EOF
require('kodelet').setup()
EOF
```

## Usage

### Quick Start Workflow

1. **Start Kodelet in your terminal**:
   ```bash
   kodelet chat
   ```

2. **In Neovim, attach to the conversation**:
   
   Press `<space>k` to see all kodelet commands, then:
   - `<space>ka` - Attach to most recent conversation
   - `<space>kp` - Pick from conversation list
   
   Or use commands directly:
   ```vim
   :KodeletAttach <Tab>  " Tab completion shows available conversations
   :KodeletAttach        " Attach to most recent
   ```

3. **Context is automatically shared**:
   - Open files are tracked
   - LSP diagnostics are included
   - Updates on buffer changes (debounced)

4. **Send feedback/messages**:
   - `<space>kf` - Opens prompt to send message
   - Or: `:KodeletFeedback Please refactor this function`

5. **Send code selections**:
   - Select code in visual mode (`V`)
   - Press `<space>ks` to send selection to Kodelet

### Default Keybindings

| Key | Mode | Description |
|-----|------|-------------|
| `<space>ka` | Normal | Attach to conversation |
| `<space>kp` | Normal | Attach with picker |
| `<space>kf` | Normal | Send feedback |
| `<space>kd` | Normal | Detach from conversation |
| `<space>kc` | Normal | Clear context |
| `<space>kx` | Normal | Clear selection |
| `<space>kt` | Normal | Show status |
| `<space>ks` | Visual | Send selection |

### Available Commands

| Command | Description |
|---------|-------------|
| `:KodeletAttach [conversation-id]` | Attach to Kodelet conversation (tab completion available) |
| `:KodeletAttachSelect` | Interactive conversation picker |
| `:KodeletFeedback [message]` | Send feedback/message to Kodelet |
| `:KodeletSendSelection` | Send visual selection to Kodelet (visual mode) |
| `:KodeletStatus` | Show current connection status |
| `:KodeletClearContext` | Manually clear context |
| `:KodeletClearSelection` | Clear current code selection |
| `:KodeletDetach` | Detach from conversation |

### Pro Tips

**Auto-attach on startup:**

Set `KODELET_CONVERSATION_ID` environment variable to auto-attach when Neovim starts:

```bash
export KODELET_CONVERSATION_ID=20241201T120000-a1b2c3d4e5f67890
nvim
```

**Workflow tip:** Keep Kodelet running in a tmux/terminal split for seamless context sharing!

## How It Works

The plugin uses file-based communication with Kodelet:

**IDE Context** (`~/.kodelet/ide/context-{conversation_id}.json`):
- Contains open files, selections, and diagnostics
- Updated automatically on buffer changes (debounced)
- Read and cleared by Kodelet on each turn

**Feedback** (via `kodelet feedback` CLI):
- Messages are appended to feedback queue
- Processed by Kodelet on next turn

## Configuration

The plugin works out-of-the-box with sensible defaults. Currently, no additional configuration is required.

Future versions may add options for:
- Debounce delay for context updates
- Diagnostics filtering
- Custom context filters

## Development

**Local development setup:**

```lua
{
  dir = vim.fn.expand("~/path/to/kodelet.nvim"),
  name = "kodelet-dev",
  config = function()
    require("kodelet").setup()
  end,
}
```

**Quick reload during development:**

```vim
:lua package.loaded['kodelet'] = nil
:lua package.loaded['kodelet.writer'] = nil
:lua package.loaded['kodelet.context'] = nil
:lua package.loaded['kodelet.commands'] = nil
:lua require("kodelet").setup()
```

## Troubleshooting

**Plugin not loading:**
```vim
:lua print(vim.inspect(package.loaded['kodelet']))
```

**Context file not created:**
```bash
ls -la ~/.kodelet/ide/
cat ~/.kodelet/ide/context-*.json | jq .
```

**Kodelet not receiving context:**
- Ensure Kodelet CLI is in PATH: `which kodelet`
- Check conversation list works: `kodelet conversation list --json`
- Verify context file exists after attaching
- Check Neovim messages: `:messages`

## Contributing

Contributions welcome! Please open issues or PRs on the [kodelet.nvim repository](https://github.com/jingkaihe/kodelet.nvim).

## License

MIT License - see [LICENSE](../LICENSE) file for details.
