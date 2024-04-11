[https://dev.to/hayden/my-2024-pde-neovim-14e5](https://dev.to/hayden/my-2024-pde-neovim-14e5)

  

[Cover image for My 2024 PDE: NeoVim](https://media.dev.to/cdn-cgi/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fxtm3vuqwpobkjxd44734.png)

[![](https://media.dev.to/cdn-cgi/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fxtm3vuqwpobkjxd44734.png)](https://media.dev.to/cdn-cgi/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fxtm3vuqwpobkjxd44734.png)

## Introduction

It's around that time of year where everyone's taking a look around and wishing they had better tools, fixing those small things that have been bugging them for months, and trying to set themselves up best for the year ahead.

[![](https://i.giphy.com/media/k0fBbRe4ySKZwmfXRj/giphy.gif)](https://i.giphy.com/media/k0fBbRe4ySKZwmfXRj/giphy.gif)

Theres one lingering TODO I'd been putting off for far too long: "DOTFILES OVERHAUL".

Your [personalised development environment™](https://www.youtube.com/watch?v=QMVIJhC9Veg) deserves some love every now and then, and what better time than the start of a new year to give it a visit with a fresh perspective, open up 20 tabs with different Colorschemes and finally upgrade your plugin manager.

It always takes a bit of convincing myself, at the end of the day - [this is my configuration, there are many like it - but this ones mine](https://www.youtube.com/watch?v=Hgd2F2QNfEE). Eventually, however, I'll take a big breath and begin the process.

On that note, sponsored by my several friends who wouldn't stop bragging about their configs, I figured I'd share what made this move worthwhile.

## Context

For those of you unfamiliar with the Vim world, [Neovim](https://neovim.io/) is a Vim fork which in recent years has become the de facto for new Vim developers. NeoVim has all the bells and whistles you want from Vim, but with a bunch of extras, too. If you want a community more passionate about contributing to the ecosystem and a lot more options when it comes to customising your PDE, it's a no brainer.

One of the largest draws to NeoVim for most is that you don't have to interface with VimScript any more.

Lua is fully supported in NeoVim, meaning you can forget all those plugins you've gotten (or not gotten) working in VimScript and embrace a language which is already widely used, even outside of the NeoVim sphere. This is great for a myriad of reasons, but for me so far, it's mostly because if you need an answer, it's likely easy to find.

## Why?

My old dotfiles (specifically the Vim configuration) have done me well, but something I've been putting off for a while now is addressing the myriad of feature requests (requested by yours truly) I've amassed on various different TODO platforms.

When I last put together [my five favourite plugins](https://dev.to/hayden/the-only-5-vim-plugins-i-need-4b7h) ([some of which](https://dev.to/hayden/optimizing-your-workflow-with-fzf-ripgrep-2eai) I'm still using) and rewrote my `.vimrc`, I was already aware I should've moved properly to NeoVim with a Lua configuration, but I decided that was a problem for future me. And that was over 18 months ago now!

[![](https://i.giphy.com/media/PhZqDgXlml04RU1Mkl/giphy.gif)](https://i.giphy.com/media/PhZqDgXlml04RU1Mkl/giphy.gif)

Yes, I'd made small tweaks here and there. I wasn't going to put up with things just not working - but there are newer and better plugins in the NeoVim ecosystem I wanted to try out.

Not only that, but I was also noticing a lack of enthusiasm for the ones I currently had. The community contributions were becoming less frequent, and when I was searching for solutions to any problems, Google would present me with a slap in the face by linking me to [threads asking why the plugin I'm referencing isn't even being used anymore](https://www.reddit.com/r/neovim/comments/14pvyo4/why_is_nobody_using_coc_anymore/).

When that's happening, you know it's time.

## My Favourite Additions

Here are a few things that personally attracted me the most to take this on:

**Treesitter** is a syntax parser that'll build a tree-like structure to enable anything from excellent syntax highlighting through to [complex refactoring](https://github.com/ThePrimeagen/refactoring.nvim). There are so many creative ways you can use Treesitter, from [jumping around text objects](https://github.com/nvim-lua/kickstart.nvim/blob/f6d67b69c3/init.lua#L330-L363) to [commenting sections of code](https://github.com/numToStr/Comment.nvim), it's a must-have in my books.

## [nvim-treesitter](https://github.com/nvim-treesitter) / [nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter)

### Nvim Treesitter configurations and abstraction layer

**Telescope.nvim** brings a revolution to file searching, project navigation, and more. Its intuitive fuzzy finding capabilities, coupled with Lua scripting, make it an essential part of my modern Neovim setup.

Telescope's fuzzy finding has been a breeze for me. With very minimal set-up to integrate fzf as an extension or to use ripgrep for project wide searches I use it for buffer searches, file searching, project searching & more!

## [nvim-telescope](https://github.com/nvim-telescope) / [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)

### Find, Filter, Preview, Pick. All lua, all the time.

**lsp-zero** helps with configuring NeoVim's native LSP. It uses [mason] to manage the installation of different language servers and it was so easy to set up. Previously I was using coc.nvim, and whilst it served me great for a while, NeoVim's native LSP not only feels faster but is being actively maintained and seems to have a much brighter future. Removing coc.nvim's JS dependency was only a bonus :)

## [VonHeikemen](https://github.com/VonHeikemen) / [lsp-zero.nvim](https://github.com/VonHeikemen/lsp-zero.nvim)

### A starting point to setup some lsp related features in neovim.

Mold your workflow to use **Harpoon** - I guarantee you won't go back. Harpoon is the best way to manage working in a large project. You can save the files and locations where you're actively working, adding and removing at a keystroke. For me, the biggest win here was stopping my terrible tab habit, where I'd have multiple tabs for different files, swapping between them with `gt` and `gT`. I can't imagine doing that any more.

## [ThePrimeagen](https://github.com/ThePrimeagen) / [harpoon](https://github.com/ThePrimeagen/harpoon)

Lastly, **alpha.nvim**. I mean, who doesn't love a cool looking dashboard when you first open up your editor?

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--ZE5vZLcv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://i.imgur.com/qVCCpbT.jpeg)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZE5vZLcv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://i.imgur.com/qVCCpbT.jpeg)

## [goolord](https://github.com/goolord) / [alpha-nvim](https://github.com/goolord/alpha-nvim)

### a lua powered greeter like vim-startify / dashboard-nvim

### Yabai & Skhd: Elevating Window Management

Moving beyond NeoVim, the inclusion of Yabai and Skhd has given me the closest to an i3 feel for MacOS. With a combination of some system settings allowing for window movement using `<CMD>+1` through `8`, Yabai stretching out my windows where I need them and SKHD allowing me to move them all with a couple keys, everything just feels naturally in place.

## Going Forward

Now I've raved about NeoVim for a while, has it got you excited? Are you going to move to NeoVim from Vim or even better stop using Sublime?

Probably not, but hey, I can still sit in my ivory castle and enjoy the forbidden fruits of what is a better PDE _for me_.

I hope if nothing else, this has got you thinking about what your personal development environment could look like, or how it could change.

Feel free to check out my dotfiles, or even give them a go!

## [haydenrou](https://github.com/haydenrou) / [dotfiles](https://github.com/haydenrou/dotfiles)

### My Personalised Development Environment

So, what does your PDE look like?

P.S. is it too obvious that the header image is generated using DALL-E?