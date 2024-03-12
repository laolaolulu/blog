[Chirpy Jekyll Theme](https://github.com/cotes2020/jekyll-theme-chirpy)
ruby下载 https://rubyinstaller.org/
### 本地调试运行

```
bundle #安装依赖项
jekyll s
```

### 打包发布

```bash
JEKYLL_ENV=production # linux
$env:JEKYLL_ENV="production" # powershell
# 发布到github
jekyll b --destination=_github/docs
# 发布到gitee
jekyll b --destination=_gitee/docs --config _config_gitee.yml
```
