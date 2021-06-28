# sed命令

## 一些简单实用的使用方式

- 替换当前文件下所有匹配字符串
```bash
sed -i 's/Log("\[LOG_/\/\/ Log("\[LOG_XXX/g' `find . -name "*.cpp" -or "*.h"`
```
