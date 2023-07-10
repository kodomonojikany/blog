# 使用方式
- git clone https://github.com/kodomonojikany/blog.git

- 安装node

- npm install hexo-cli -g 

- cd blog

- npm install  (博客根目录执行，之后都是)

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

- npm install --save git://github.com/howiefh/hexo-generator-calendar.git

- npm i hexo-butterfly-article-double-row --save

- npm install hexo-bilibili-bangumi --save

- npm install hexo-steam-games --save

- 修改博客根目录下`node_modules\hexo-steam-games` 的`index.js`文件（因为最近游戏列表失效了）

  ```javascript
  const fs = require('hexo-fs');
  const path = require('path');
  const log = require('hexo-log')({
      debug: false,
      silent: false
  });
  const axios = require('axios-https-proxy-fix');
  const cheerio = require('cheerio');
  
  let options = {
      options: [
          { name: '-u, --update', desc: 'Update steam games data' },
          { name: '-d, --delete', desc: 'Delete steam games data' }
      ]
  };
  
  hexo.extend.generator.register('steamgames', function (locals) {
      if (!this.config.steam || !this.config.steam.enable) {
          return;
      }
      return require('./lib/steam-games-generator').call(this, locals);
  });
  hexo.extend.console.register('steam', 'Update steam games data', options, function (args) {
      if (args.d) {
          if (fs.existsSync(path.join(__dirname, "/data/"))) {
              fs.rmdirSync(path.join(__dirname, "/data/"));
              log.info('Steam games data has been deleted');
          }
      } else if (args.u) {
          if (!this.config.steam || !this.config.steam.enable) {
              log.info("Please add config to _config.yml");
              return;
          }
          if (!this.config.steam.steamId && !this.config.steam.steamInfos) {
              log.info("Please add steamId/steamInfos to _config.yml");
              return;
          }
          if (!this.config.steam.apiKey) {
              log.info("Please add apiKey to _config.yml");
              return;
          }
          if (this.config.steam.steamId) {
              updateSteamGames(this.config.steam.steamId, this.config.steam.apiKey, this.config.steam.tab, this.config.steam.length, this.config.steam.proxy, this.config.steam.freeGames);
          } else if (this.config.steam.steamInfos) {
              this.config.steam.steamInfos.forEach(steamInfo => {
  
                  updateSteamGames(steamInfo.id, this.config.steam.apiKey, steamInfo.tab || this.config.steam.tab, steamInfo.length || this.config.steam.length, steamInfo.proxy || this.config.steam.proxy, steamInfo.freeGames || this.config.steam.freeGames);
              });
          }
      } else {
          console.error("Unknown command, please use \"hexo bangumi -h\" to see the available commands")
      }
  });
  
  function sortJ(a, b) {
  	return b.rtime_last_played - a.rtime_last_played;
  }
  
  function updateSteamGames(steamId, apiKey, tab = "recent", length = 1000, proxy = false, freeGames = false) {
      log.info(`Getting steam(${steamId}) games, please wait...`);
      let options = {
          method: "GET",
          url: `http://api.steampowered.com/IPlayerService/GetOwnedGames/v0001/?key=${apiKey}&steamid=${steamId}&format=json&include_appinfo=true${freeGames ? '&include_played_free_games=true' : ''}`,
          timeout: 30 * 60 * 1000,
          responseType: "json",
          headers: {
              "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.87 Safari/537.36"
          }
      };
      if (proxy && proxy.host && proxy.port) {
          options.proxy = {
              host: proxy.host,
              port: proxy.port
          };
      }
      axios(options).then(response => {
          if (response.status === 200) {
              const games = response.data.response.games;
              if (games.length === 0) {
                  log.error('No game data obtained.')
                  return;
              }
              if (!fs.existsSync(path.join(__dirname, "/data/"))) {
                  fs.mkdirsSync(path.join(__dirname, "/data/"));
              }
              /* 主要是 这里往下三行改了一下数据的排序*/
              let gameData = games.slice(0, length);
              var data = gameData.sort(sortJ);
              fs.writeFile(path.join(__dirname, `/data/${steamId}.json`), JSON.stringify(data), err => {
                  if (err) {
                      log.info(`Failed to write data to ${steamId}.json`);
                      console.log(err);
                  } else {
                      log.info(`${gameData.length} games(${steamId}) data are saved.`);
                  }
              });
          } else {
              console.error('ERROR: ' + response.status)
          }
      }).catch(error => {
          console.log(error);
      });
  }
  
  ```

  
# 部署顺序
1. hexo clean
2. hexo bangumi -u     # 更新追番数据
3. hexo cinema -u  # 更新影剧数据
4. hexo steam -u  # 更新游戏数据
5. hexo g
6. gulp
7. hexo d 或hexo server

