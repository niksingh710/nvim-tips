# Tips for nvim native users.

- [Lua](#lua)
- [Vim Api](#vim-api)
- [Telescope](#telescope-tips)
- [Cmdline](#cmdline)
- [Tmux](#tmux)
- [Mappings](#mapping)
- [Telescope and NvimTree](#telescope-and-nvimtree)
- [Plugins](#plugins)

## Lua

Lua is the primary configuration language of Neovim, so some basic knowledge is a prerequisite. A good place to start is the guide from the [NvChad Docs](https://nvchad.com/docs/quickstart/learn-lua).

## Vim Api

Understanding some of the internal functions and variables in Vim can be quite helpful for enhancing your productivity. One particularly powerful function is `vim.inspect`.

This function allows you to neatly display Lua tables in a well-formatted manner.

This makes it significantly easier to comprehend the contents and values within the table, aiding you in gaining a clearer understanding of the data structure.

```lua

local tbl = {
    name = "sid",
    age = 20,
    isAlive = true,
    colors = { "red", "green", "blue" },
}
vim.inspect(tbl)
-- output
--[[
{
  age = 20,
  colors = {
    1 = "red",
    2 = "green",
    3 = "blue",
  },
  isAlive = true,
  name = "sid",
}
]]--
```

## Telescope Tips

Let's make our telescope look like a bit nicer (NvChad and Nyoom do this)
here we will do it for our nvim config.

| Before                                             | After                                            |
| -------------------------------------------------- | ------------------------------------------------ |
| ![before-telescope](./assets/telescope-before.png) | ![after-telescope](./assets/telescope-after.png) |

This has been done by changing nvim highlight groups.
I love using `tokyodark.nvim` theme you can find it [here](https://github.com/tiagovla/tokyodark.nvim)

I will be using this theme to change the highlight groups.

```lua
-- I used vim.inspect to get color palette from tokyodark.nvim
-- vim.inspect(require("tokyodark.palette"))
local colors = require("tokyodark.palette")
local TelescopeColor = {
    TelescopeMatching = { fg = colors.orange },
    TelescopeSelection = { fg = colors.fg, bg = colors.bg1, bold = true },
    TelescopePromptPrefix = { bg = colors.bg1 },
    TelescopePromptNormal = { bg = colors.bg1 },
    TelescopeResultsNormal = { bg = colors.bg0 },
    TelescopePreviewNormal = { bg = colors.bg0 },
    TelescopePromptBorder = { bg = colors.bg1, fg = colors.bg1 },
    TelescopeResultsBorder = { bg = colors.bg0, fg = colors.bg0 },
    TelescopePreviewBorder = { bg = colors.bg0, fg = colors.bg0 },
    TelescopePromptTitle = { bg = colors.purple, fg = colors.bg0 },
    TelescopeResultsTitle = { fg = colors.bg0 },
    TelescopePreviewTitle = { bg = colors.green, fg = colors.bg0 },
}

for hl, col in pairs(TelescopeColor) do
    vim.api.nvim_set_hl(0, hl, col)
end
```

Putting this much in your config will change your telescope look completly.

If you want this to be a theme independent you can define your own colors and use them.

```lua
local colors = {
    bg = "#282c34",
    fg = "#abb2bf",
    yellow = "#e0af68",
    cyan = "#56b6c2",
    darkblue = "#081633",
    green = "#98c379",
    orange = "#d19a66",
    violet = "#a9a1e1",
    magenta = "#c678dd",
    blue = "#61afef",
    red = "#e86671",
}

local TelescopeColor = {
    TelescopeMatching = { fg = colors.orange },
    TelescopeSelection = { fg = colors.fg, bg = colors.bg, bold = true },
    TelescopePromptPrefix = { bg = colors.bg },
    TelescopePromptNormal = { bg = colors.bg },
    TelescopeResultsNormal = { bg = colors.bg },
    TelescopePreviewNormal = { bg = colors.bg },
    TelescopePromptBorder = { bg = colors.bg, fg = colors.bg },
    TelescopeResultsBorder = { bg = colors.bg, fg = colors.bg },
    TelescopePreviewBorder = { bg = colors.bg, fg = colors.bg },
    TelescopePromptTitle = { bg = colors.purple, fg = colors.bg },
    TelescopeResultsTitle = { fg = colors.bg },
    TelescopePreviewTitle = { bg = colors.green, fg = colors.bg },
}
for hl, col in pairs(TelescopeColor) do
    vim.api.nvim_set_hl(0, hl, col)
end
```

This is from an comment on reddit [post](https://www.reddit.com/r/neovim/comments/xcsatv/how_can_i_configure_telescope_to_look_like_this/)

## Cmdline

You can use [noice.nvim](https://github.com/folke/noice.nvim) from folke.
But if you want a minimal feel with full vim like editing in cmdline.

try `q:` this will open a buffer with all your history and you can edit it like a normal buffer.
and pressing `<cr>` on the line hovered will execute it.

if you like `q:` then below are a few mappings and options you may like

```lua
vim.opt.cmdwinheight = 1,
vim.opt.cmdheight = 0,

vim.opt.wildcharm = ("\t"):byte() -- so that tab key can be remapped

vim.keymap.set("c", ":", "q:", {})
vim.keymap.set("n", ":", "q:i", {})
vim.keymap.set("c", "<c-j>", "<Tab>", {})
vim.keymap.set("c", "<c-k>", "<s-Tab>", {})

```

## Tmux

You use tmux then there are a few tips that may enhance your experience.

[vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator) is a plugin that allows you to navigate between vim and tmux panes seamlessly.

Mappings in `tmux.conf`

```tmux

# christoomey Mappings {{{

# Smart pane switching with awareness of vim and fzf
forward_programs="view|n?vim?|fzf|lvim"

should_forward="ps -o state= -o comm= -t '#{pane_tty}' |\
  grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?($forward_programs)(diff)?$'"

bind -n C-h if-shell "$should_forward" "send-keys C-h" "select-pane -L"
bind -n C-j if-shell "$should_forward" "send-keys C-j" "select-pane -D"
bind -n C-k if-shell "$should_forward" "send-keys C-k" "select-pane -U"
bind -n C-l if-shell "$should_forward" "send-keys C-l" "select-pane -R"
bind -n C-\\ if-shell "$should_forward" "send-keys C-\\" "select-pane -l"
# }}}

```

Now you can switch focus between panes using `c-h,c-j,c-k,c-l`


## Mapping

```lua
-- This fn will allow you to map easily
map = function(mode, mappings, opts)
	-- enabled easy mapping syntax
	-- local mapping = {
	--  ["jk"] = "<esc>",
	--  ["<c-s>"] = { ":w<cr>", "Save" },
	-- }
	-- map( "n", mapping, {})

	for k, v in pairs(mappings) do
		opts = opts or {}
		local default = {
			noremap = true,
			silent = true,
		}
		opts = vim.tbl_deep_extend("force", default, opts)
		local value = v
		local desc = ""
		if type(v) == "table" then
			value, desc = v[1], v[2]
		end
		opts["desc"] = desc
		vim.keymap.set(mode, k, value, opts)
	end
end

-- Some Mappings you may find usefull.

local normal = {
	["<c-s>"] = { "<cmd>w<cr>", "Save the file" },
	["<c-h>"] = { "<c-w>h", "Focus on right split" },
	["<c-j>"] = { "<c-w>j", "Focus on below split" },
	["<c-k>"] = { "<c-w>k", "Focus on up split" },
	["<c-l>"] = { "<c-w>l", "Focus on left split" },
	["<c-a-j>"] = { ":resize -1<cr>", "Resize down" },
	["<c-a-k>"] = { ":resize +1<cr>", "Resize up" },
	["<c-a-l>"] = { ":vertical resize -1<cr>", "Resize right" },
	["<c-a-h>"] = { ":vertical resize +1<cr>", "Resize left" },
	["<c-a-=>"] = { "<C-a>", "Increase Number" }, ["<c-a-->"] = { "<C-x>", "Decrease Number" },

	["<a-j>"] = { ":m .+1<cr>==", "Move line Down" },
	["<a-k>"] = { ":m .-2<cr>==", "Move line up" },

	["<s-h>"] = { ":bprevious<cr>", "Buffer Previous" },
	["<s-l>"] = { ":bnext<cr>", "Buffer Next" },

	["<leader>q"] = { ":quit!<cr>", "Quit!" },
	-- ["<leader>c"] = { ":bd!<cr>", "Close!" },
	["<leader>c"] = { "<cmd>lua utils.close_buffer()<cr>", "Close!" }, -- Custom fn to ensure if this is the last buffer then quit nvim
	["<leader>h"] = { ":nohl<cr>", "No Highlight!" },
	["<leader>a"] = { "gg0vG$", "Select All" },
	["<leader>vv"] = { "<cmd>vsplit<cr>", "vertical split" },
	["<leader>vs"] = { "<cmd>split<cr>", "horizontal split" },
	["<leader>tj"] = { "<cmd>tabn<cr>", "Next Tab" },
	["<leader>tk"] = { "<cmd>tabp<cr>", "Previous Tab" },
	["<leader>tl"] = { "<cmd>tabn<cr>", "Next Tab" },
	["<leader>th"] = { "<cmd>tabp<cr>", "Previous Tab" },

	["<leader>tq"] = { "<cmd>tabclose<cr>", "Close Tab" },
	["<leader>tn"] = { "<cmd>tabnew<cr>", "New Tab" },

	---@diagnostic disable-next-line: undefined-global
	["<leader><leader>"] = { utils.filetypeSet, "Set Filetype" },

	-- highlight are in center
	["n"] = "nzzzv",
	["N"] = "Nzzzv",
	["x"] = '"_x',
}

local insert = { -- as these are insert mode mapping they don't need descriptions
	["jk"] = "<esc>",
	-- ["kj"] = "<esc>",
	["<esc>"] = "<esc>", -- Copilot unmaps esc in insert mode
	["<c-s>"] = "<esc>:w<cr>",
	["<a-j>"] = "<esc>:m .+1<cr>==gi",
	["<a-k>"] = "<esc>:m .-2<cr>==gi",
}

local visual = {
	["<c-s>"] = "<esc>:w<cr>",
	["<c-c>"] = "<esc>",

	["<a-j>"] = ":m '>+1<cr>gv-gv",
	["<a-k>"] = ":m '<lt>-2<CR>gv-gv",

	["<"] = "<gv",
	[">"] = ">gv",

	["<space>"] = "<Nop>",

	["x"] = '"_x',
	["p"] = '"_dP',
	["P"] = '"_dP',

	["<leader>y"] = '"+y',
	["<leader>d"] = '"+d',
	["<leader>Y"] = 'gg"+yG',
	["<leader>D"] = 'gg"+dG',
	["<leader>x"] = '"+x',
	["<leader>X"] = '"+',
}
map("n", normal)
map("i", insert)
map("x", visual)

```

## Telescope and Nvimtree
If you use these two plugins you may be finding it annoying when opening your file from home directory.
And then using `Telescope find_files` to find out it is searching in home dir instead of the buffer path.
The solution i came up with is a fn that will change the dir to the current buffer.
```lua
local set_currdir = function()
	local curdir = vim.fn.expand("%:p:h")
	vim.api.nvim_set_current_dir(curdir)
end

vim.keymap.set("<leader><cr>", set_currdir, {})

```
Now i just press `leader<cr>` and location of Telescope and nvim tree changes to current buffer

## Plugins

Not mentioning plugins like lsp, null-ls (rn Archieved), telescope, treesitter, etc. as you will eventully face them.

- [nvim-surround](https://github.com/kylechui/nvim-surround) At first will fea like a gimmick but once you get used to it you will love it.
- [firenvim](https://github.com/glacambre/firenvim) Brwoser and nvim integration. I use it for editing text in browser. Get your neovim config in browser text boxes.


- [vimium-c](https://github.com/gdh1995/vimium-c) Browser extension to use vim like keys in browser. 
