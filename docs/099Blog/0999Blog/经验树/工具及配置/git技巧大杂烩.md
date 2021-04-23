## 一切都是为了易用性

子模块更新
```
git clone --recurse-submodules[=<pathspec>] 
```
同理还有`git checkout`.
但是如何不用输这么长的命令？

git alias失败，不能同名遮盖的样子啊
shell上动手

```
function do_git {
  cmd=$1
  shift
  extra=""
  if [ "$cmd" == "dummy" ]; then
    extra=""
  elif [ "$cmd" == "checkout" ]; then
    extra="--recurse-submodules"
  fi
  # git="$(which git)"
  # try this
  git = "$(type -P vim)"
  ex="$git $cmd $extra $@"
  ${ex}
}
alias  git='do_git'
```

##
