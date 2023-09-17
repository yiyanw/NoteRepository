# Laravel

* project initialization
  * https://laravel.com/docs/10.x/installation#your-first-laravel-project
  * `composer create-project laravel/laravel example-app`
    * if you meet this problem: 
      * `Your requirements could not be resolved to an installable set of packages.`
    * you may need to add `--ignore-platform-req=ext-fileinfo` at the end 

* frontend tool - vite

* routing
  * server side 
  * web.php - api设置文件
    * only
    * except

* layout
  * persistent layout （inertia）

* database
  * https://laravel.com/docs/10.x/eloquent#generating-model-classes

* resource controller
  * 专门处理数据的接口类， 比如数据库的某个表

* reactive & ref
  * reactive用于obj
  * ref用于原始变量

* middleware
  * 在不同page之间share information
  * 在request return之前 增加额外的工作位置

* computed properties
  * 因为reactive中的成员抠出来就不相应变化了，
  * 所以套一层computed
  * 也可以用于进一步加工数据

* 校验没太看懂
  * 没看明白怎么在校验的同时create new entry

* update
  * 和create基本一致

* delete
  * 需要用到delete request可以在Link Tag上定義method

* 使用laravel中方法的名字代替曾删改查的url
  * 安装ziggy `composer require tightenco/ziggy`
  * 减少错误率

* commands

  * 项目启动需要的指令

    * npm run dev
    * php artisan serve
    * docker compose up

  * php artisan command

    * basic
      * serve
      * ide-helper:generate
      * route:list

    * API
      * make:controller \<Name>
      * make:controller --resource \<Name> - resource controller
      * inertia:middleware

    * database

      * db:show

      * make:model \<TableName> -m - 创建table映射类和对应的migration类

      * make:migration \<name> - 创建migration类

      * migrate:rollback

      * migrate:refresh

      * migrate:status

      * make:factory \<FactoryName> - 创建工厂类

      * db:seed

      * migrate:refresh --seed

      * tinker - 用于display database operations

        * namespace App\Models - 解决找不到的问题

        * \<TableName>::all() - select * from \<TableName>

        * \<TableName>::find(id) - 基于id寻找

        * \<TableName>::where('\<col>', '><=', val)

          * 这是个obj，需要->get()获得结果
          * where可以连着调用

        * new \<TableName> - 创建一个新obj

        * obj=>save() - 保存至数据库

        * \<TableName>::create(['col' => val]) - insert一个新的object

          * 需要在对应的php文件中添加 fillable变量

            ```php
            <?php
            
            namespace App\Models;
            
            use Illuminate\Database\Eloquent\Factories\HasFactory;
            use Illuminate\Database\Eloquent\Model;
            
            class Listing extends Model
            {
                use HasFactory;
            
                protected $fillable = [
                    'beds', 'baths', 'area', 'city', 'code', 
                    'street', 'street_nr', 'price'
                ];
            }
            ```

* Tailwind CSS
  * 需要安装taiwindcss, autoprefixer, postcss
  * postcss.config.cjs , 不能是postcss.config.js
  * named slot
  * loading bar
    * inertia progress 