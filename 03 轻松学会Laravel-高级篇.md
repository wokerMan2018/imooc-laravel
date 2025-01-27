# 轻松学会Laravel-高级篇

### Composer快速入门

>Composer官网：[https://getcomposer.org/](https://getcomposer.org/)  
>Composer中文网：[http://www.phpcomposer.com](http://www.phpcomposer.com)

### 通过composer.phar安装Composer
局部安装：将composer.phar文件复制到任意目录（比如项目根目录下），然后通过`php composer.phar`指令即可以使用Composer了

全局安装：
composer下载：https://getcomposer.org/composer.phar
```bash
chmod u+x composer.phar
mv composer.phar /bin/composer
```

### Composer中国全量镜像

>http://pkg.phpcomposer.com/

查看当前的镜像地址
```bash
composer config -gl
```

Packagist 镜像用法：
全局配置
```bash
composer config -g repo.packagist composer https://packagist.phpcomposer.com
// 还原初始配置
composer config -g repo.packagist composer https?://packagist.org
```

单个项目配置
打开命令行窗口（windows用户）或控制台（Linux、Mac 用户），进入你的项目的根目录（也就是 composer.json 文件所在目录），执行如下命令：
```bash
composer config repo.packagist composer https://packagist.phpcomposer.com
```
注：如果没有composer.json文件，需要新建一个composer.json文件，还需要在里面写一对{}号，不然执行这个命令会报错

### 使用Composer

```bash
mkdir demo
cd demo
composer init
composer config repo.packagist composer https://packagist.phpcomposer.com
```

搜索（search）
```bash
composer search monolog
```

展示（show）
```bash
composer show --all monolog/monolog
```

申明依赖（require）
vi composer.json
```php
"require": {
    "monolog/monolog":"1.21.*",
    "symfony/http-foundation": "^3.2"
},
```

安装（install）
```bash
composer install
```

更新（update）
vi composer.json

```php
"require": {
    "monolog/monolog":"1.21.*"
},
```
composer update

### 使用Composer安装Laravel
通过Composer Create-Project 命令安装 Laravel

```bash
composer search laravel
composer show --all laravel/laravel
composer create-project laravel/laravel --prefer-dist blog
composer create-project laravel/laravel shop --prefer-dist "5.3.*"		// 安装某个具体版本
```

Laravel 安装器
```bash
// 使用 Composer 下载 Laravel 安装包
composer global require "laravel/installer"
// 再将 ~/.composer/vendor/bin 路径加到 PATH
echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc
// 重启一下
reboot
// 测试Laravel 安装器是否安装成功
laravel
// 安装laravel
laravel new laravel2
// 下载最新的开发版本
laravel new test --dev
```

### Artisan基本用法
查看所有可用的Artisan的命令（list）

```bash
php artisan
php artisan list
```

查看命令的帮助信息（help）
php artisan help make:controller

```bash
composer create-project laravel/laravel laravel53 --prefer-dist "5.3.*"
// 创建控制器
php artisan make:controller StudentController
// 创建模型
php artisan make:model Student
// 创建中间件
php artisan make:middleware Activity
```

### Laravel中的用户认证（Auth）

```bash
// 生成Auth所需文件
php artisan make:auth
// 执行迁移
php artisan migrate
```
通过访问 http://192.168.99.100:8080/home 就可以进行注册登录了，如果访问出现了样式问题，只需要将 resources/views/layouts/app.blade.php 文件中引入css和引入js的路径改为如下即可：
```php
{{ asset('css/app.css') }}
{{ asset('js/app.js') }}
```

### Laravel中的数据迁移
###### 新建迁移文件
通过 `php artisan make:migration create_students_table` 来新建迁移文件。--table和--create参数可以用来指定数据表名称，以及迁移文件是否要建立新的数据表

生成模型的同时生成迁移文件 `php artisan make:model Student -m`

###### 下面咱们以students表来新建一个迁移文件
表结构如下

```sql
create table if not exists students(
	id int auto_increment primary key,
    name varchar(255) not null default '' comment '姓名',
    age int unsigned not null default 0 comment '年龄',
    sex int unsigned not null default 10 comment '性别',
    created_at int not null default 0 comment '新增时间',
    updated_at int not null default 0 comment '修改时间'
)engine=innodb default charset utf8 auto_increment=1001 comment='学生表';
```

```bash
php artisan make:migration create_students_table --create=students
php artisan make:model Article -m
```

2017_02_03_033958_create_students_table.php
```php
// 编辑（自定义）迁移文件
public function up()
{
    Schema::create('students', function (Blueprint $table) {
        $table->increments('id');
        $table->string('name');
        $table->integer('age')->unsigned()->default(0);
        $table->integer('sex')->unsigned()->default(10);
        $table->integer('created_at')->default(0);
        $table->integer('updated_at')->default(0);
    });
}
```

生成数据表
```bash
php artisan migrate
```
![](image/screenshot_1486093918968.png)

### Laravel中的数据填充
创建一个填充文件，并完善填充文件

```bash
php artisan make:seeder StudentTableSeeder
```

执行单个填充文件
```bash
php artisan db:seed --class=StudentTableSeeder
```

批量执行填充文件
```bash
php artisan db:seed
```

数据填充实例
```bash
php artisan make:seeder StudentTableSeeder
```

database/seeds/StudentTableSeeder.php
```php
public function run()
{
    DB::table('students')->insert([
    	['name'=>'zhangsan', 'age'=>18],
    	['name'=>'lishi', 'age'=>20]
    ]);
}
```

```bash
php artisan db:seed --class=StudentTableSeeder
```

database/seeds/DatabaseSeeder.php
```php
public function run()
{
    // $this->call(UsersTableSeeder::class);
    // 批量执行填充，需要先引入一下
    $this->call(StudentTableSeeder::class);
}
```

```bash
php artisan db:seed
```

![](image/screenshot_1486652322614.png)

![](image/screenshot_1486652398063.png)

![](image/screenshot_1486652467473.png)

### Laravel中的文件上传
Laravel的文件系统是基于Frank de Jonge的Flysystem扩展包，提供了简单的接口，可以操作本地端空间、Amazon、S3、Rackspace Cloud Storage，可以非常简单的切换不同保存方式，但仍使用相同的API操作

配置文件位置：config/filesystems.php

文件上传实例
config/filesystems.php
```php
'disks' => [

    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
    ],

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'visibility' => 'public',
    ],

    'uploads' => [
        'driver' => 'local',
        'root' => storage_path('app/uploads')
    ],

    's3' => [
        'driver' => 's3',
        'key' => 'your-key',
        'secret' => 'your-secret',
        'region' => 'your-region',
        'bucket' => 'your-bucket',
    ],

],
```

routes/web.php
```php
Route::any('/upload', 'StudentController@upload');
```

app/Http/Controller/StudentController
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

class StudentController extends Controller
{
    public function upload(Request $request){
    	if($request->isMethod('POST')){
    		$file = $request->file('source');

    		// 判断文件是否上传成功
    		if($file->isValid()){
    			// 获取原文件名
    			$oriFileName = $file->getClientOriginalName();
    			// 获取文件的扩展名
    			$ext = $file->getClientOriginalExtension();
    			// 获取文件的mime类型
    			$type = $file->getClientMimeType();
    			// 临时文件的绝对路径
    			$realPath = $file->getRealPath();

    			$filename = date('Y-m-d H-i-s') . '-'. uniqid() . '.' . $ext;

    			$bool = Storage::disk('uploads')->put($filename, file_get_contents($realPath));

    			var_dump($bool);
    		}
            
			exit;
    	}

    	return view('student.upload');
    }
}
```

resources/views/student/upload.blade.php（复制的是 Laravel中的用户认证（Auth）小节生成的login.blade.php）
```html
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading">文件上传</div>
                <div class="panel-body">
                    <form class="form-horizontal" role="form" method="POST" action="" enctype="multipart/form-data">
                        {{ csrf_field() }}

                        <div class="form-group{{ $errors->has('password') ? ' has-error' : '' }}">
                            <label for="file" class="col-md-4 control-label">请选择文件</label>

                            <div class="col-md-6">
                                <input id="file" type="file" class="form-control" name="source" required>
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-8 col-md-offset-4">
                                <button type="submit" class="btn btn-primary">
                                    确认上传
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

![](image/screenshot_1486656049790.png)

### Laravel中的邮件发送
配置文件：config/mail.php
Mail::raw() 发送纯文本格式 			Mail::send() 发送html格式

第一种发送方式
.env
```php
MAIL_DRIVER=smtp
MAIL_HOST=smtp.163.com
MAIL_PORT=465
MAIL_USERNAME=jiezeal@163.com
MAIL_PASSWORD=Internet678
MAIL_ENCRYPTION=ssl
MAIL_FROM_ADDRESS=jiezeal@163.com
MAIL_FROM_NAME='jiezeal'
```

routes/web.php
```php
Route::any('/mail', 'StudentController@mail');
```

app/Http/Controller/StudentController
```php
use Mail;	

public function mail(){
	// 第一种发送方式 发送纯文本
	Mail::raw('邮件内容', function ($message){
		$message->from('jiezeal@163.com', 'jiezeal');
		$message->subject('邮件主题');
		$message->to('jiezeal@foxmail.com');
	});
}
```

![](image/screenshot_1486784167667.png)

第二种发送方式
app/Http/Controller/StudentController
```php
public function mail(){
	// 第二种发送方式 发送html
	Mail::send('student.mail', ['name' => 'jiezeal', 'age' => 18], function ($message){
		$message->subject('邮件主题');
		$message->to('jiezeal@foxmail.com');
	});
}
```

resources/views/student/mail.blade.php
```html
<h1>Hello {{ $name }} {{ $age }}</h1>
```

![](image/screenshot_1486784315481.png)

### Laravel中的缓存使用
配置文件位置：config/cache.php

routes/web.php
```php
Route::any('/cache1', 'StudentController@cache1');
Route::any('/cache2', 'StudentController@cache2');
```

app/Http/Controller/StudentController
```php
use Illuminate\Support\Facades\Cache;

public function cache1(){
	// put() 保存对象到缓存中
	Cache::put('key1', 'val1', 10);

	// add() 也是添加缓存 如果key1存在则添加失败，不存在则添加成功
	$bool = Cache::add('key1', 'val1', 10);
	var_dump($bool);

	$bool = Cache::add('key2', 'val2', 10);
	var_dump($bool);

	// forever() 永久的保存对象到缓存中
	Cache::forever('key3', 'val3');

	// has() 判断key是否存在
	if(Cache::has('key3')){
		$val = Cache::get('key3');
		var_dump($val);
	}else{
		echo 'No';
	}
}

public function cache2(){
	// get() 从缓存中获取对象
	$val = Cache::get('key1');
	var_dump($val);

	$val = Cache::get('key2');
	var_dump($val);

	$val = Cache::get('key3');
	var_dump($val);

	// 获取并删除缓存
	$val = Cache::pull('key2');
	var_dump($val);

	// forget() 从缓存中删除对象
	$bool = Cache::forget('key3');
	var_dump($bool);
}
```

### Laravel中的错误与日志
###### Debug模式
配置文件位置：config/app.php

>进行本地开发时，应该配置APP_DEBUG环境变量为true，在上线环境，这个值应该永远为false

HTTP异常
>有些异常描述来自服务器的HTTP错误码。例如，这可能是一个“页面未找到”错误（404），“认证失败错误”（401）亦或是程序出错造成的400错误

日志
>Laravel日志工具基于强大的Monolog库，提供了single、daily、syslog和errorlog日志模式，以及debug、info、notice、warning、error、critical和alert七个错误级别

routes/web.php
```php
Route::any('/error', 'StudentController@error');
```

app/Http/Controller/StudentController
```php
use Illuminate\Support\Facades\Log;

public function error(){
	$name = 'zhangsan';
	var_dump($name);

	return view('student.error');

	$student = null;
	if($student == null){
    	// 对应 resource/views/errors/503.blade.php
		abort('503');				
        // 对应 resource/views/errors/500.blade.php
		abort('500');
	}

	Log::info('这是一个info级别的日志');
	Log::warning('这是一个warning级别的日志');
	Log::error('这是一个error级别的日志', ['name' => 'zhangsan', 'age' => 18]);

}
```

![](image/screenshot_1486810130375.png)

![](image/screenshot_1486810097394.png)

对应的是 resource/views/errors/404.blade.php

![](image/screenshot_1486810167480.png)

### Laravel中的队列应用
Laravel队列服务为各种不同的后台队列提供了统一的API，允许推迟耗时任务（例如发送邮件）的执行，从而大幅提高web请求速度

配置文件位置：config/queue.php

主要步骤
迁移队列需要的数据表
```bash
php artisan queue:table
php artisan migrate
```

编写任务类
```bash
php artisan make:job SendEmail
```

app/SendEmail.php
```php
<?php

namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Mail;

class SendEmail implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $email;

    /**
     * Create a new job instance.
     *
     * @return void
     */
    public function __construct($email)
    {
        $this->email = $email;
    }

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        Mail::raw('队列测试', function ($message){
            $message->subject('队列测试');
            $message->to($this->email);
        });
    }
}
```

routes/web.php
```php
Route::any('/queue', 'StudentController@queue');
```

app/Http/Controller/StudentController
```php
public function queue(){
	dispatch(new SendEmail('jiezeal@foxmail.com'));
}
```

推送任务到队列
浏览器访问 http://192.168.99.100:8080/queue

运行队列监听器
```bash
php artisan queue:listen
```

处理失败任务
```bash
php artisan queue:failed-table
php artisan migrate
// 查看执行失败的任务
php artisan queue:failed
// 重新执行（ID为1）的那一条失败的任务
php artisan queue:retry 1
// 重新执行所有失败的任务
php artisan queue:retry all
// 删除（ID为4）的那一条失败的任务
php artisan queue:forget 4
// 删除所有执行失败的任务
php artisan queue:flush
```

![](image/screenshot_1486819898685.png)