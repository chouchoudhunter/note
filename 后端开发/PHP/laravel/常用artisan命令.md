创建控制器

php artisan make:controller OrderController
创建Rest风格资源控制器（带有index、create、store、edit、update、destroy、show方法）
![[Pasted image 20230505132337.png]]
php artisan make:controller OrderController --resource
创建模型

php artisan make:model Student
创建新建表的迁移和修改表的迁移

php artisan make:migration create_orders_table --create=orders //创建订单表orders
php artisan make:migration add_tags_to_orders_table --table=orders//给orders表增加tags字段

执行迁移
php artisan migrate

创建模型的时候同时生成新建表的迁移+控制器+路由
php artisan make:model Order -m -c -r
回滚上一次的迁移

php artisan migrate:rollback
回滚所有迁移

php artisan migrate:reset
创建填充

php artisan make:seeder OrderTableSeeder
执行单个填充

php artisan db:seed --class=OrderTableSeeder
执行所有填充

php artisan db:seed
创建中间件（app/Http/Middleware 下）

php artisan make:middleware Activity
创建队列（数据库）的表迁移（需要执行迁移才生效）

php artisan queue:table
创建队列类（app/jobs下）：

php artisan make:job SendEmail
创建请求类（app/Http/Requests下）

php artisan make:request CreateArticleRequest