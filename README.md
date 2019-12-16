#安装
1. 安装Hexo
	```
	npm install -g hexo-cli
	```

1. Live2d插件 
    ```
    npm install --save hexo-helper-live2d
    ```
1. Gitalk评论插件 
    ```
    npm i --save gitalk
    ```
1. Sitemap站点地图 
	```
	npm install hexo-generator-sitemap --save
	npm install hexo-generator-baidu-sitemap --save
	```
1. 站内搜索
    ```
    npm install hexo-generator-searchdb --save
    ```
1. 图片懒加载
    ```
    npm install hexo-lazyload-image --save
    ```
    站点配置文件：
    ```
    # 图片懒加载
    lazyload:
    enable: true
    # 是否仅文章中的图片做懒加载
    onlypost: false
    loadingImg: /images/xxx.png
    ```