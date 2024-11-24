+++
title = 'nvim配置文件'
date = '2024-11-24T18:32:16+08:00'
draft = false
categories = ["教程"]
tags = ["iterm2", "nvim"]
author ='Yuu'
+++
# nvim配置文件备份

> .config/nvim

```markdown
.
├── nvim
│   ├── init.lua
│   ├── lua
│   │   ├── keymaps.lua
│   │   ├── config
│   │   │   └── nvim-cmp.lua
│   │   ├── plugins.lua
│   │   ├── lsp.lua
│   │   ├── colorscheme.lua
│   │   └── options.lua
│   └── lazy-lock.json
```

### init.lua

```sh
require('options')        
require('keymaps')
require('plugins')
require('colorscheme')
require('lsp')
```

### lua/keymaps.lua

```sh
-- define common options
local opts = {
    noremap = true,      -- non-recursive
    silent = true,       -- do not show message
}

-----------------
-- Normal mode --
-----------------

-- Hint: see `:h vim.map.set()`
-- Better window navigation
vim.keymap.set('n', '<C-h>', '<C-w>h', opts)
vim.keymap.set('n', '<C-j>', '<C-w>j', opts)
vim.keymap.set('n', '<C-k>', '<C-w>k', opts)
vim.keymap.set('n', '<C-l>', '<C-w>l', opts)

-- Resize with arrows
-- delta: 2 lines
--vim.keymap.set('n', '<C-Up>', ':resize -2<CR>', opts)
--vim.keymap.set('n', '<C-Down>', ':resize +2<CR>', opts)
--vim.keymap.set('n', '<C-Left>', ':vertical resize -2<CR>', opts)
--vim.keymap.set('n', '<C-Right>', ':vertical resize +2<CR>', opts)

-----------------
-- Visual mode --
-----------------

-- Hint: start visual mode with the same area as the previous area and the same mode
vim.keymap.set('v', '<', '<gv', opts)

vim.keymap.set('v', '>', '>gv', opts)

-- 设置 timeoutlen 为 300 毫秒
vim.o.timeoutlen = 300

-- 绑定 jk 在插入模式下切换到正常模式
vim.api.nvim_set_keymap('i', 'jk', '<Esc>', { noremap = true, silent = true })


-- 绑定 Ctrl+W 保存并退出
vim.api.nvim_set_keymap('n', '<C-w>', ':wq<CR>', { noremap = true, silent = true })
vim.api.nvim_set_keymap('i', '<C-w>', '<Esc>:wq<CR>', { noremap = true, silent = true })
vim.api.nvim_set_keymap('v', '<C-w>', '<Esc>:wq<CR>', { noremap = true, silent = true })


-- 绑定 Shift+J 向下滚动自定义长度
vim.api.nvim_set_keymap('n', '<S-J>', ':normal! 5j<CR>', { noremap = true, silent = true })
-- 绑定 Shift+K 向下滚动自定义长度
vim.api.nvim_set_keymap('n', '<S-K>', ':normal! 5k<CR>', { noremap = true, silent = true })
-- 绑定 Command+Z (Ctrl+Z) 撤销功能
vim.api.nvim_set_keymap('n', '<C-z>', ':undo<CR>', { noremap = true, silent = true })
vim.api.nvim_set_keymap('i', '<C-z>', '<Esc>:undo<CR>', { noremap = true, silent = true })
vim.api.nvim_set_keymap('v', '<C-z>', '<Esc>:undo<CR>', { noremap = true, silent = true })

-- 绑定 Shift+小键盘上下左右键进行滚动
vim.api.nvim_set_keymap('n', '<S-Up>', ':normal! 5k<CR>', { noremap = true, silent = true })  -- 向上滚动 3 行
vim.api.nvim_set_keymap('n', '<S-Down>', ':normal! 5j<CR>', { noremap = true, silent = true })  -- 向下滚动 3 行
vim.api.nvim_set_keymap('n', '<S-Left>', ':normal! 5h<CR>', { noremap = true, silent = true })  -- 向左滚动 3 列
vim.api.nvim_set_keymap('n', '<S-Right>', ':normal! 5l<CR>', { noremap = true, silent = true })  -- 向右滚动 3 列
-- 绑定 Shift+小键盘上下左右键进行滚动
vim.api.nvim_set_keymap('i', '<S-Up>', '<Esc>:normal! 5k<CR>', { noremap = true, silent = true })  -- 向上滚动 3 行
vim.api.nvim_set_keymap('i', '<S-Down>', '<Esc>:normal! 5j<CR>', { noremap = true, silent = true })  -- 向下滚动 3 行
vim.api.nvim_set_keymap('i', '<S-Left>', '<Esc>:normal! 5h<CR>', { noremap = true, silent = true })  -- 向左滚动 3 列
vim.api.nvim_set_keymap('i', '<S-Right>', '<Esc>:normal! 5l<CR>', { noremap = true, silent = true })  -- 向右滚动 3 列

-- 设置 Option+小键盘方向键映射
vim.api.nvim_set_keymap('n', '<A-Right>', '<C-w>l', { noremap = true, silent = true })
vim.api.nvim_set_keymap('n', '<A-Left>', '<C-w>h', { noremap = true, silent = true })
vim.api.nvim_set_keymap('n', '<A-Down>', '<C-w>j', { noremap = true, silent = true })
vim.api.nvim_set_keymap('n', '<A-Up>', '<C-w>k', { noremap = true, silent = true })

-- 设置 Option+小键盘方向键映射
vim.api.nvim_set_keymap('i', '<A-Right>', '<Esc><C-w>l', { noremap = true, silent = true })
vim.api.nvim_set_keymap('i', '<A-Left>', '<Esc><C-w>h', { noremap = true, silent = true })
vim.api.nvim_set_keymap('i', '<A-Down>', '<Esc><C-w>j', { noremap = true, silent = true })
vim.api.nvim_set_keymap('i', '<A-Up>', '<Esc><C-w>k', { noremap = true, silent = true })

-- 使用 pbcopy 将文本复制到系统剪贴板
vim.api.nvim_set_keymap('v', '<leader>y', ':"+y', { noremap = true, silent = true })
vim.api.nvim_set_keymap('n', '<leader>y', ':"+y', { noremap = true, silent = true })

-- 确保 Neovim 使用系统剪贴板进行复制和粘贴
vim.opt.clipboard:append { 'unnamedplus' }






```



### lua/config/nvim-cmp.lua

```sh
local has_words_before = function()
    unpack = unpack or table.unpack
    local line, col = unpack(vim.api.nvim_win_get_cursor(0))
    return col ~= 0 and vim.api.nvim_buf_get_lines(0, line - 1, line, true)[1]:sub(col, col):match("%s") == nil
end

local luasnip = require("luasnip")
local cmp = require("cmp")

cmp.setup({
    snippet = {
        -- REQUIRED - you must specify a snippet engine
        expand = function(args)
            require('luasnip').lsp_expand(args.body) -- For `luasnip` users.
        end,
    },
    mapping = cmp.mapping.preset.insert({
        -- Use <C-b/f> to scroll the docs
        ['<C-b>'] = cmp.mapping.scroll_docs( -4),
        ['<C-f>'] = cmp.mapping.scroll_docs(4),
        -- Use <C-k/j> to switch in items
        ['<C-k>'] = cmp.mapping.select_prev_item(),
        ['<C-j>'] = cmp.mapping.select_next_item(),
        -- Use <CR>(Enter) to confirm selection
        -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
        ['<CR>'] = cmp.mapping.confirm({ select = true }),

        -- A super tab
        -- sourc: https://github.com/hrsh7th/nvim-cmp/wiki/Example-mappings#luasnip
        ["<Tab>"] = cmp.mapping(function(fallback)
            -- Hint: if the completion menu is visible select next one
            if cmp.visible() then
                cmp.select_next_item()
            elseif has_words_before() then
                cmp.complete()
            else
                fallback()
            end
        end, { "i", "s" }), -- i - insert mode; s - select mode
        ["<S-Tab>"] = cmp.mapping(function(fallback)
            if cmp.visible() then
                cmp.select_prev_item()
            elseif luasnip.jumpable( -1) then
                luasnip.jump( -1)
            else
                fallback()
            end
        end, { "i", "s" }),
    }),

  -- Let's configure the item's appearance
  -- source: https://github.com/hrsh7th/nvim-cmp/wiki/Menu-Appearance
  formatting = {
      -- Set order from left to right
      -- kind: single letter indicating the type of completion
      -- abbr: abbreviation of "word"; when not empty it is used in the menu instead of "word"
      -- menu: extra text for the popup menu, displayed after "word" or "abbr"
      fields = { 'abbr', 'menu' },

      -- customize the appearance of the completion menu
      format = function(entry, vim_item)
          vim_item.menu = ({
              nvim_lsp = '[Lsp]',
              luasnip = '[Luasnip]',
              buffer = '[File]',
              path = '[Path]',
          })[entry.source.name]
          return vim_item
      end,
  },

  -- Set source precedence
  sources = cmp.config.sources({
    { name = 'nvim_lsp' },    -- For nvim-lsp
    { name = 'luasnip' },     -- For luasnip user
    { name = 'buffer' },      -- For buffer word completion
    { name = 'path' },        -- For path completion
  })
})


```



### lua/plugins.lua

```sh
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

require("lazy").setup({
    "tanvirtin/monokai.nvim",
    -- LSP manager
	"williamboman/mason.nvim",
	"williamboman/mason-lspconfig.nvim",
	"neovim/nvim-lspconfig",
	-- Vscode-like pictograms
	{
		"onsails/lspkind.nvim",
		event = { "VimEnter" },
	},
	-- Auto-completion engine
	{
		"hrsh7th/nvim-cmp",
		dependencies = {
			"lspkind.nvim",
			"hrsh7th/cmp-nvim-lsp", -- lsp auto-completion
			"hrsh7th/cmp-buffer", -- buffer auto-completion
			"hrsh7th/cmp-path", -- path auto-completion
			"hrsh7th/cmp-cmdline", -- cmdline auto-completion
		},
		config = function()
			require("config.nvim-cmp")
		end,
	},
	-- Code snippet engine
	{
		"L3MON4D3/LuaSnip",
		version = "v2.*",
	},

  -- 添加 nvim-tree 插件
  {
    'nvim-tree/nvim-tree.lua',
    dependencies = {
      'nvim-tree/nvim-web-devicons', -- 可选, 为文件图标提供支持
    },
    config = function()
      require('nvim-tree').setup {
        -- 你的配置选项
        update_cwd = true, -- 更新当前工作目录
        view = {
          width = 30,
          side = 'left',
          -- auto_resize 已弃用，使用新的配置选项
        },
        actions = {
          open_file = {
            resize_window = true, -- 在打开文件时自动调整窗口大小
          },
        },
      }

      -- 绑定快捷键
      vim.api.nvim_set_keymap('n', '<C-n>', ':NvimTreeToggle<CR>', { noremap = true, silent = true })
    end
  }, 
    -- 添加 nvim-scrollview 插件
  {
    'dstein64/nvim-scrollview',
    config = function()
      require('scrollview').setup {
        width = 20, -- 设置滚动视图的宽度
        height = 20, -- 设置滚动视图的高度
        hide_cursor = true, -- 隐藏光标
        show_cursorline = true, -- 高亮当前行
      }
    end
  },
    -- 安装 nvim-lualine
  {
    'nvim-lualine/lualine.nvim',
    dependencies = { 'nvim-tree/nvim-web-devicons' },
    config = function ()
      require('lualine').setup {
        options = {
        icons_enabled = true,
        theme = 'auto',
        component_separators = { left = '', right = ''},
        section_separators = { left = '', right = ''},
        disabled_filetypes = {
        statusline = {},
        winbar = {},
    },
    ignore_focus = {},
    always_divide_middle = true,
    globalstatus = false,
    refresh = {
      statusline = 1000,
      tabline = 1000,
      winbar = 1000,
    }
  },
  sections = {
    lualine_a = {'mode'},
    lualine_b = {'branch', 'diff', 'diagnostics'},
    lualine_c = {'filename'},
    lualine_x = {'encoding', 'fileformat', 'filetype'},
    lualine_y = {'progress'},
    lualine_z = {'location'}
  },
  inactive_sections = {
    lualine_a = {},
    lualine_b = {},
    lualine_c = {'filename'},
    lualine_x = {'location'},
    lualine_y = {},
    lualine_z = {}
  },
  tabline = {},
  winbar = {},
  inactive_winbar = {},
  extensions = {}
}
    
  end
 },
 -- ends
 

})



```



### lua/lsp.lua

```sh
require('mason').setup({
    ui = {
        icons = {
            package_installed = "✓",
            package_pending = "➜",
            package_uninstalled = "✗"
        }
    }
})

require('mason-lspconfig').setup({
    -- A list of servers to automatically install if they're not already installed
    ensure_installed = { 'pylsp', 'lua_ls', 'rust_analyzer','clangd' },
})

-- Set different settings for different languages' LSP
-- LSP list: https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md
-- How to use setup({}): https://github.com/neovim/nvim-lspconfig/wiki/Understanding-setup-%7B%7D
--     - the settings table is sent to the LSP
--     - on_attach: a lua callback function to run after LSP attaches to a given buffer
local lspconfig = require("lspconfig")

-- Customized on_attach function
-- See `:help vim.diagnostic.*` for documentation on any of the below functions
local opts = { noremap = true, silent = true }
vim.keymap.set("n", "<space>e", vim.diagnostic.open_float, opts)
vim.keymap.set("n", "[d", vim.diagnostic.goto_prev, opts)
vim.keymap.set("n", "]d", vim.diagnostic.goto_next, opts)
vim.keymap.set("n", "<space>q", vim.diagnostic.setloclist, opts)

-- Use an on_attach function to only map the following keys
-- after the language server attaches to the current buffer
local on_attach = function(client, bufnr)
	-- Enable completion triggered by <c-x><c-o>
	vim.api.nvim_buf_set_option(bufnr, "omnifunc", "v:lua.vim.lsp.omnifunc")

	if client.name == "rust_analyzer" then
		-- This requires Neovim 0.10 or later
		vim.lsp.inlay_hint.enable()
	end

	-- See `:help vim.lsp.*` for documentation on any of the below functions
	local bufopts = { noremap = true, silent = true, buffer = bufnr }
	vim.keymap.set("n", "gD", vim.lsp.buf.declaration, bufopts)
	vim.keymap.set("n", "gd", vim.lsp.buf.definition, bufopts)
	vim.keymap.set("n", "K", vim.lsp.buf.hover, bufopts)
	vim.keymap.set("n", "gi", vim.lsp.buf.implementation, bufopts)
	vim.keymap.set("n", "<C-k>", vim.lsp.buf.signature_help, bufopts)
	vim.keymap.set("n", "<space>wa", vim.lsp.buf.add_workspace_folder, bufopts)
	vim.keymap.set("n", "<space>wr", vim.lsp.buf.remove_workspace_folder, bufopts)
	vim.keymap.set("n", "<space>wl", function()
		print(vim.inspect(vim.lsp.buf.list_workspace_folders()))
	end, bufopts)
	vim.keymap.set("n", "<space>D", vim.lsp.buf.type_definition, bufopts)
	vim.keymap.set("n", "<space>rn", vim.lsp.buf.rename, bufopts)
	vim.keymap.set("n", "<space>ca", vim.lsp.buf.code_action, bufopts)
	vim.keymap.set("n", "gr", vim.lsp.buf.references, bufopts)
	vim.keymap.set("n", "<space>f", function()
		vim.lsp.buf.format({
			async = true,
			-- Only request null-ls for formatting
			filter = function(client)
				return client.name == "null-ls"
			end,
		})
	end, bufopts)
end

-- How to add a LSP for a specific language?
-- 1. Use `:Mason` to install the corresponding LSP.
-- 2. Add configuration below.
lspconfig.pylsp.setup({
	on_attach = on_attach,
})

lspconfig.gopls.setup({
	on_attach = on_attach,
})

lspconfig.lua_ls.setup({
	on_attach = on_attach,
	settings = {
		Lua = {
			runtime = {
				-- Tell the language server which version of Lua you're using (most likely LuaJIT in the case of Neovim)
				version = "LuaJIT",
			},
			diagnostics = {
				-- Get the language server to recognize the `vim` global
				globals = { "vim" },
			},
			workspace = {
				-- Make the server aware of Neovim runtime files
				library = vim.api.nvim_get_runtime_file("", true),
			},
			-- Do not send telemetry data containing a randomized but unique identifier
			telemetry = {
				enable = false,
			},
		},
	},
})

lspconfig.bashls.setup({})

-- source: https://rust-analyzer.github.io/manual.html#nvim-lsp
lspconfig.rust_analyzer.setup({
	on_attach = on_attach,
})

lspconfig.clangd.setup({
	on_attach = on_attach,
})

lspconfig.ocamllsp.setup({
	on_attach = on_attach,
})

```



### lua/colorscheme.lua

```sh
-- define your colorscheme here
local colorscheme = 'monokai_pro'

local is_ok, _ = pcall(vim.cmd, "colorscheme " .. colorscheme)
if not is_ok then
    vim.notify('colorscheme ' .. colorscheme .. ' not found!')
    return
end


```



### lua/options.lua

```sh
-- Hint: use `:h <option>` to figure out the meaning if needed vim.opt.clipboard = 'unnamedplus' -- use system clipboard
vim.opt.completeopt = { 'menu', 'menuone', 'noselect' }
-- 设置鼠标滚轮滚动长度
vim.o.mouse = 'a' -- 启用鼠标支持
-- 设置鼠标滚轮滚动长度为 5 行
-- vim.g.lazy_mouse_resize = true -- 启用鼠标支持
-- vim.g.lazy_mouse_resize_amount = 5 -- 设置鼠标滚轮滚动长度为 5 行
-- Tab
vim.opt.tabstop = 4 -- number of visual spaces per TAB
vim.opt.softtabstop = 4 -- number of spacesin tab when editing
vim.opt.shiftwidth = 4 -- insert 4 spaces on a tab
vim.opt.expandtab = true -- tabs are spaces, mainly because of python

-- UI config
vim.opt.number = true -- show absolute number
vim.opt.relativenumber = true -- add numbers to each line on the left side
vim.opt.cursorline = true -- highlight cursor line underneath the cursor horizontally
vim.opt.splitbelow = true -- open new vertical split bottom
vim.opt.splitright = true -- open new horizontal splits right
-- vim.opt.termguicolors = true        -- enabl 24-bit RGB color in the TUI
vim.opt.showmode = false -- we are experienced, wo don't need the "-- INSERT --" mode hint

-- Searching
vim.opt.incsearch = true -- search as characters are entered
vim.opt.hlsearch = false -- do not highlight matches
vim.opt.ignorecase = true -- ignore case in searches by default
vim.opt.smartcase = true -- but make it case sensitive if an uppercase is entered


vim.cmd('highlight Normal guibg=NONE ctermbg=NONE')  -- 设置背景为透明



```

