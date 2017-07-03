# vim-jsx-eslint

为了在 vim 中支持 JSX 语法的高亮显示, 需要安装一些插件, vim 安装插件有两种方法：

- [Pathogen](https://github.com/tpope/vim-pathogen) 可以特别方便的安装插件, 按照文档安装即可, 插件放在 `~/.vim/bundle` 目录之下
- [Vundle](https://github.com/VundleVim/Vundle.vim) 专门用于插件的管理

> 推荐使用 Pathogen

1. JSX 高亮

	需要安装插件:
	 - [vim-jsx](https://github.com/mxw/vim-jsx) 用于 JSX 语法高亮 
	 - [vim-javascript](https://github.com/pangloss/vim-javascript) 用于 JS 语法高亮

2. eslint 支持

	需要安装插件：
	- [syntastic](https://github.com/vim-syntastic/syntastic)  可以调用外部命令行工具来进行代码风格检查
	- [vim-autoformat](https://github.com/Chiel92/vim-autoformat) 可以调用外部命令行工具来格式化代码
	
	相关配置如下：
	
	```
	execute pathogen#infect()
	syntax on
	filetype plugin indent on
	
	let g:jsx_ext_required = 0
	let g:syntastic_javascript_checkers = ['eslint']
	
	"set statusline+=%#warningmsg#
	"set statusline+=%{SyntasticStatuslineFlag()}
	"set statusline+=%*
	"let g:syntastic_always_populate_loc_list = 1
	"let g:syntastic_auto_loc_list = 1
	let g:syntastic_check_on_open = 1
	let g:syntastic_check_on_wq = 1
	
	"let g:syntastic_error_symbol = '❌'
	"let g:syntastic_warning_symbol = '⚠️'
	
	" 当前目录 eslint
	if executable('node_modules/.bin/eslint')
	  let b:syntastic_javascript_eslint_exec = 'node_modules/.bin/eslint'
	endif
		
	```
	说明：
	
	- 没有配置 vim-autoformat，如有需要自行配置
	

3. 参考文档
  	- http://harttle.com/2017/03/12/vim-eslint.html
