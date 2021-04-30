# Vim安装配置&使用经验[CentOS]

## 安装
1. 干掉旧的版本（当然也可以想办法共存）
```bash
yum list installed | grep -i vim
yum remove vim*

基本会删除这几个
Removing:
 vim-common     x86_64  2:7.4.629-6.el7
 vim-enhanced   x86_64  2:7.4.629-6.el7
 vim-filesystem x86_64  2:7.4.629-6.el7
 vim-minimal    x86_64  2:7.4.629-6.el7
```
2. 选择心仪的版本
```bash
可以选择这里的发行,勇敢点试试最新的吧。
https://github.com/vim/vim/releases
```

3. 编译
```bash
# 先装python3 == yum install python3 # 当然有很多种办法处理啦
# with-features=huge 特性最多的版本，对应tiny等
# 安装失败，perl头文件缺失

yum install perl-ExtUtils-Embed

./configure --with-features=huge \
 --enable-python3interp --enable-pythoninterp \
 --with-python-config-dir=/usr/bin/ \
 --enable-rubyinterp --enable-luainterp \
 --enable-perlinterp \
 --enable-gui=no \
 --enable-multibyte --enable-cscope \
 --prefix=/usr/local/vim/
 
 make install
 
 # ADD PATH
 
```

4. 插件& 配置

利用vim 8的自我插件管理

```
[root@szvpwebenv00831 ~]# tree -L 4 ~/.vim
/root/.vim
├── colors
│   └── molokai.vim
└── pack
    └── plugin
        └── start
            ├── LeaderF-1.23
            ├── nerdtree-master
            └── vim-better-whitespace-master
```

```
" allow syntax                                                                 
syntax on                                                                      
                                                                               
" allow plugins                                                                
filetype plugin indent on                                                      
set backspace=2                                                                
set laststatus=2                                                               
set ruler                                                                      
set nu                                                                         
set colorcolumn=120                                                            
set hlsearch                                                                   
hi Search ctermbg=LightYellow                                                  
hi Search ctermfg=Red                                                          
let g:better_whitespace_enabled=1                                              
set cursorline                                                                 
hi CursorLine term=bold cterm=bold ctermbg=Red                                 
                                                                               
colorscheme molokai                                                            
set t_Co=256                                                                   
set background=dark                                                            
                                                                               
" file association set                                                         
autocmd FileType c,cpp,cc,h set shiftwidth=4 | set expandtab | set tabstop=4   
                                                                               
map <C-p> :Leaderf file<CR>                                                    
map <C-f> :Leaderf! rg "                                                       
map <C-n> :NERDTree<CR>                                                        
```
