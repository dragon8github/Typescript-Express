## Typescript + Express 实战后端服务

以『提醒事项』为例构建一套提供 API Server 的后端服务

**0️⃣ git init（略）** 

**1️⃣ 安装依赖：** `$ yarn init &&  yarn add -D ts-node typescript tslint husky`

**2️⃣ 生成 tsconfig.json：**` $ yarn tsc -init `

**3️⃣ 生成 tslint.json：**` $ yarn tslint --init `

**4️⃣ 创建 src/index.ts：**`$ mkdir src && echo "console.log('Hello Typescript')" > src/index.ts`

**5️⃣ 执行 .ts 文件：**` $ yarn ts-node src/index.ts `

**6️⃣ 打开 package.json，添加 husky 的 hook：** 每次提交代码前，都会执行一次TSLint的检查命令。
```json
{
    "husky": {
        "pre-commit": "yarn tslint -c tslint.json 'src/**/*.ts'"
    }
}
```

**7️⃣ 我们选用 SQLite3 作为数据库** 

下载安装：http://www.sqlite.org/download.html

第三方安装教程：https://blog.csdn.net/weixin_41656968/article/details/80338626

**8️⃣ 安装 typeorm**

我们需要一个『ORM』中间件来完成程序和数据库的连接。担当此重任的就是 『typeorm』，它完美地支持 Typescript，同时还能提供非常友好的代码操作接口。

 `$ yarn add -D typeorm sqlite3 @types/node reflect-metadata`

安装依赖是根据文档描述来的：https://github.com/typeorm/typeorm#installation

在根目录下创建配置文件：`ormconfig.json`

```json
{
    "type": "sqlite",
    "database": "db.sqlite3",
    "autoSchemaSync": true,
    "synchronize": true,
    "logging": true,
    "entities": [
        "src/entity/*.js"
    ],
    "migrations": [
        "src/migration/*.js"
    ],
    "subscribers": [
        "src/subscriber/*.js"
    ],
    "cli": {
        "entitiesDir": "src/entity",
        "migrationsDir": "src/migration",
        "subscribersDir": "src/subscriber"
    }
}
```

配置文档：https://typeorm.io/#/connection-options/

- entity：指数据模型，或者称为数据示例。
- migration：指数据库发生变化时历代的迁移文件。
- subscriber：指数据层的监听器，做一下前后置操作。

**9️⃣ 定义数据模型**

在 `src` 下创建文件夹 `entity`，再创建文件 `workItem.ts`：

```Typescript
import {
    Column,
    CreateDateColumn,
    Entity,
    PrimaryGeneratedColumn,
    UpdateDateColumn,
} from 'typeorm'

@Entity
export class WorkItem {
    @PrimaryGeneratedColumn()
    public id: number;

    @Column( { length: 100, } )
    public text: string;

    @Column( { default: false } )
    public isChecked: boolean;

    @CreateDateColumn( { type: 'timestamp' } )
    public createAt: Date;

    @UpdateDateColumn( { type: 'timestamp' } )
    public updatedAt: Date;
}
```

**1️⃣0️⃣ 再回到 index.ts 中测试连接**

```Typescript
import { createConnection } from "typeorm"
import "reflect-metadata"

createConnection()
```

正常的话会输出以下信息：

```sql
query: BEGIN TRANSACTION
query: SELECT * FROM "sqlite_master" WHERE "type" = 'table' AND "name" = 'typeorm_metadata'
query: COMMIT
```