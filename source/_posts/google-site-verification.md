---
title: hexo 驗證 google-site-verification
date: 2018-09-27 22:08:24
tags: 
- google
- hexo
---

1. 先安裝 sitemap
    ```console
    $ npm install hexo-generator-feed
    ```
2. 查看根目錄下是否新增 /.deploy_git/sitemap.xml
<!--more-->
3. 根目錄下的 /_config.yml
    ```yml
    url: https://iloveleon19.github.io/
    ```

4. 根目錄下的 /themes/next/_config.yml
    ```yml
    menu:
      home: / || home
      # about: /about/ || user
      tags: /tags/ || tags
      categories: /categories/ || th
      archives: /archives/ || archive
      #schedule: /schedule/ || calendar
      sitemap: /sitemap.xml || sitemap
      #commonweal: /404/ || heartbeat
    ```

5. 獲取 google site verification code
    1. 登錄 Google Webmaster，點擊至驗證方法，並選擇 HTML Tag。將會獲取到一段代碼：
        ```html
        <meta name="google-site-verification" content="XXXXXXXXXXXXXXXXXXXXXXX" />
        ```
        將 content 裡面的 `XXXXXXXXXXXXXXXXXXXXXXX` 複製出來

6. 根目錄下的 /themes/next/_config.yml
    ```yml
    google_site_verification: XXXXXXXXXXXXXXXXXXXXXXX
    ```

7. 發佈到你的網站
```console
 $ hexo d -g
```

8. 在 Google Webmaster 提交 sitemap 網站地圖 https://iloveleon19.github.io/`sitemap.xml`