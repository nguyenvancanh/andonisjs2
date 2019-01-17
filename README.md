# Adonisjs2

Trong phần một, tôi đã giới thiệu tới mọi người các định nghĩa cơ bản của **AdonisJs** như: Controller, View, Request, ... chi tiết bài viết phần một mọi người có thêm xem lại tại link [AdonisJS P.1](https://github.com/nguyenvancanh/Adonisjs/blob/master/README.md). Tiếp tục series về Adonis, trong bài này, tôi xin tiếp tục giới thiệu với mọi người về **Database** trong AndonisJs. 

Cũng hoàn toàn tưowng tự như Laravel, **Adonisjs** cũng hỗ trợ tối đa và mạnh mẽ thao tác với database bằng các tính năng **Query Builder, Lucid ORM, Migrations, Factories** và **Seeds**.

# Supported Databases

AdonisJs support được hầu hết các cơ sở dữ liệu hiện tại:

| Database   |      NPM Driver      |
|----------|:-------------|
| MariaDB |  npm i mariasql |
| MSSQL |    npm i mssql   |
| MySQL | npm i mysql or npm i mysql2 |
| Oracle | npm i oracledb or npm i strong-oracle |
| PostgreSQL | npm i pg |
| SQLite3 | npm i sqlite3 |

# Setup

Nếu **Database Provider** không được cài đặt thì hãy chạy lệnh sau:

```
adonis install @adonisjs/lucid
```

Tiếp theo, cần khai báo provider trong file _start/app.js_

```
const providers = [
  '@adonisjs/lucid/providers/LucidProvider'
]

const aceProviders = [
  '@adonisjs/lucid/providers/MigrationsProvider'
]
```

**Database Provider** sử dụng sqlite làm conection mặc định. Chúng ta có thể tùy chỉnh điều này bằng cách:

```
module.exports = {
  connection: 'mysql',
}
``` 

trong file _config/database.js_. Tất cả những config option của Knex đều được hỗ trợ như trong Laravel.

# Basic Example

```
const Database = use('Database')

Route.get('/', async () => {
  return await Database.table('users').select('*')
})
```
Đây là ví dụ cơ bản với câu query để select tất cả các trường của bảng user.

## Điều kiện where

Để thêm điều kiện where vào trong câu truy vấn, thì chúng ta xử dụng method where()

```
Database
  .table('users')
  .where('age', '>', 18)
```

hay muốn thêm một mệnh đề where khác, chúng ta có method orWhere()

```
Database
  .table('users')
  .where('age', '>', 18)
  .orWhere('vip', true)
```

## Multiple Connections

AdonisJs sẽ mặc định xử dụng giá trị conection được khai báo trong file _config/database.js_ khi tạo ra câu truy vấn. Chúng ta có thể tùy ý lự chọn một connections trong file config, khi bắt đầu khơi tạo câu truy vấn

```
Database
  .connection('mysql')
  .table('users')
```

Để đóng một connection, sử dụng phương thức close() đi kèm với tên của connection bạn muốn đóng

```
const users = await Database
  .connection('mysql')
  .table('users')

// later close the connection
Database.close(['mysql'])
```

## Table Prefixing

Chúng ta có thể đặt prefix cho tables trong file config như sau:

```
module.exports = {
  connection: 'sqlite',

  sqlite: {
    client: 'sqlite3',
    prefix: 'my_'
  }
}
```

Sau khi đặt prefix, thì tất cả các câu querry trong connection sqlite sẽ có prefix đi đầu tên mỗi table là _my__ Đơn giản như sau:

```
await Database
  .table('users')
  .select('*')
```

Output query chúng ta có là:

```
select * from `my_users`


# Query Builder

Bây giờ, chúng ta sẽ đi vào phần cốt lõi, cách để tạo lên được một câu lệnh truy vấn trong AdonisJs

## Basic Example

```
const Database = use('Database')

class UserController {

  async index (request, response) {
    return await Database
      .table('users')
      .where('username', 'john')
      .first()
  }

}

## Selects

Hàm selects, giúp chúng ta lấy ra những trường cần thiết từ trong database, nếu muốn lấy tòan bột các trường của một bảng thì chúng ta xử dụng ksy tự '*'

```
await Database.select('id', 'username').from('users')
// or
await Database.select('*').from('users')
```

Việc đặt alias cho một trường cũng vô cùng đơn giản, xử dụng từ khóa 'as'

```
await Database.select('username as uname')
```

## Where Clauses

