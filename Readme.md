# npm需要安装的内容
- npm install hexo-cli -g 
- npm install  (博客`cd blog`根目录执行，之后都是)
- git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
- npm install hexo-renderer-pug hexo-renderer-stylus --save
- npm install hexo-generator-searchdb
- npm install hexo-generator-search --save
- npm install hexo-wordcount --save
- npm install hexo-offline --save
- npm install hexo-generator-feed --save
- npm install hexo-deployer-git --save
- npm install --global gulp-cli  
- npm install gulp --save
- npm install gulp-htmlclean --save-dev  
- npm install gulp-html-minifier-terser --save-dev  
- npm install gulp-clean-css --save-dev
- npm install gulp-terser --save-dev
- npm install gulp-fontmin --save-dev
- npm install hexo-generator-sitemap --save
- npm install hexo-abbrlink --save
- npm i hexo-filter-nofollow --save
# 部署顺序
1. hexo clean
2. hexo g
3. gulp
4. hexo d

