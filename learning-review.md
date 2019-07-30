#  學期三自學回顧

在過去兩個月當中，我想遭遇的最大挑戰，就是將 Node.js 專案從 MySQL 轉到 PostgreSQL 然後部署到 Heroku 上了吧

起因是，在作業 **A19: To-do List using Sequelize** 的題目中，提到

> *由於用 Sequelize 上傳至 Heroku 之前，會需要做 PostgreSQL 的設定，和之前用 Mongoose 有點不同*

因此我就想說：應該沒有很難，來自己試試看吧！以下分享我的心路歷程
****
## What I'm going to achieve

先確立我要達成的目標是：

1. 將資料庫從 MySQL 轉換到 PostgreSQL
2. 將專案部署到 Heroku 上
***
## Where I am now

在要前往目標之前，先回顧一下這個階段我知道了些什麼。根據過去學習使用 MySQL 的經驗，知道設定資料庫有以下幾個步驟：

> 1. 透過 MySQL Workbench 建立一個 todo-sequelize 的 MySQL 資料庫
> 2. 安裝 mysql2、sequelize 與 sequelize-cli
> 3. 初始化 Sequelize 並設定資料庫的名字與密碼
> 4. 新增 todo 資料模型 (model)
> 5. 執行 db:migrate 新增資料表
> 6. 新增 user model 並執行 db:migrate
> 7. 在資料表新增外鍵，建立關聯
> 8. 在 model 建立關聯
> 9. 把 model 載入 app.js

另外在部署到 Heroku 上有以下幾個步驟

> 1. 修改專案
> 2. 在 Heroku 上建立專案
> 3. 將專案推上 Heroku
> 4. 在 Heroku 安裝資料庫
***
## What I need to do

回顧完自己的背景知識之後，就要開始評估自己到完成目標還差多遠：
* 有哪些 to-dos 要做
* 有哪些知識需要補齊

### 1. 轉換資料庫
如果要轉換到 PostgreSQL 資料庫的話，對比上面的步驟，推測有以下幾件事情要做

1. 在本地端安裝 PostgreSQL，建立一個跟先前專案使用的資料庫同名的 PostgreSQL 資料庫
2. 安裝 pg (sequelize 與 sequelize-cli 有支援 PostgreSQL 所以直接沿用)
3. 初始化 Sequelize 並在專案中設定資料庫 (/config/config.json)

至於 4. 到 8. 的部分，因為當初的 migration 檔案都在，因此只要最後跑
```
$ npx sequelize db:migrate 
```
就可以直接搞定

9. 把 model 載入 app.js ，這邊推測也不會有所變動
```
const db = require('./models')
```
### 2. 部署至 Heroku

步驟應該會跟先前差不多，但是會需要知道

1. 如何修改專案以部署至 Heroku
2. 如何在 Heroku 上安裝 PostgreSQL 資料庫

***
## Challenges

在轉換資料庫的時候，發現原本在 MySQL 可以正常運作的 query，在 PostgreSQL 卻無法運作，後來才知道 PostgreSQL 規定比較嚴謹，譬如在 PostgreSQL 命名上建議使用小寫的，如果有使用大寫，需要使用 double quote，舉例來說：

原本在 MySQL 的程式碼為
```
let rawRecords = await db.sequelize.query(`SELECT Records.id,Records.date,Records.name,Records.amount,Records.CategoryId, Categories.categoryName,Categories.icon FROM Records JOIN Categories ON Records.CategoryId = Categories.id ${queryUser} ORDER BY "Records".date DESC`)
```

在 PostgreSQL 中需要改成
```
let rawRecords = await db.sequelize.query(`SELECT "Records".id,"Records".date,"Records".name,"Records".amount,"Records"."CategoryId", "Categories"."categoryName","Categories".icon FROM "Records" JOIN "Categories" ON "Records"."CategoryId" = "Categories".id ${queryUser} ORDER BY "Records".date DESC`)
```

另外，在發現 app 無法運作的時候，就不斷的用 console.log 去查看程式碼停在什麼地方，另外為了更瞭解非同步可能產生的問題，在 console.log 中不僅放入變數，同時也放入了時間戳印 ( `new Date()` ) 確認執行時序。

最後，雖然網路上的資源很多，但是挑戰在於
1. 要能夠找到最貼近自己目前狀態（儲備知識）、同時又能夠幫助自己達成目標的文件
2. 要能夠交叉比對不同文件，然後判斷合適可行的做法

***

程式這個領域真的是無邊無際，教材內容也沒辦法包山包海，不過 AC 幫我們開了一道門之後，讓我們越來越有有能力自己去探索這個程式世界。

每週的學習進度安排真的讓人感覺到有壓力，不過沒有這個推力，我應該也沒辦法在八週之內學習這麼多。另外也謝謝助教們的指導，以及同學們的討論，讓我在線上課程的環境中，沒有一人孤軍奮戰的感覺。期待下一個八週的旅程！