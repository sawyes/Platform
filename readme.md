## 初始化

当前仓库基于laravel 5.6，你可以直接克隆这个仓库，免去手动初始化的步骤。但是你应该意识到laravel版本升级迭代，我建议每一个大版本你都应该保存一个类似的仓库，以便快速初始化模板，同时明确`nwidart/laravel-modules`的版本，实践中版本升级还是会有不稳定性

### 手动初始化项目


手动引用laravel最新版本，而不是克隆本项目

```
composer-create project laravel/laravel Platform
```

编辑`.gitignore`,替换如下：

```
/node_modules
/public/hot
/public/storage
/storage/*.key
/storage/debugbar/*
/vendor
/.idea
/.vscode
/.vagrant
Homestead.json
Homestead.yaml
npm-debug.log
yarn-error.log
.env

composer.lock
_ide_helper.php
_ide_helper_models.php
/.idea
Modules/*
```

#### 添加基础扩展包


编辑 composer.json, 添加如下包, 其中`nwidart/laravel-modules`是必须的

- 官方文档[nwidart/laravel-modules](https://nwidart.com)
- GitHub[Nicolas Widart](https://github.com/nwidart)
- 官方文档[phpoffice/phpspreadsheet](https://phpspreadsheet.readthedocs.io/en/develop/)
- github [phpoffice/phpspreadsheet](https://github.com/PHPOffice/PhpSpreadsheet)
- github [barryvdh/laravel-ide-helper](https://github.com/barryvdh/laravel-ide-helper)

```
composer require -o nwidart/laravel-modules 
composer require -o predis/predis
composer require -o guzzlehttp/guzzle
composer require -o phpoffice/phpspreadsheet
composer require -o barryvdh/laravel-ide-helper --dev

```


修改自动加载机制, 支持`nwidart/laravel-modules`模块化开发，所有模块代码保存在项目根目录`Modules`下, 通过`composer autoload`更新项目自动加载代码

```
"autoload": {
    ...
        "psr-4": {
            ...
            "Modules\\": "Modules/"
        }
    },
```

#### 自动完成

添加编辑器代码`自动完成`支持

```
php artisan ide-helper:generate
```

并且修改composer.json, 以便每次更新`composer update`可以重新生成自动完成。
```
"scripts":{
    "post-update-cmd": [
        "Illuminate\\Foundation\\ComposerScripts::postUpdate",
        "php artisan ide-helper:generate",
        "php artisan ide-helper:meta"
    ]
},
```

设置生产环境不生成代码提示
```

public function register()
{
    if ($this->app->environment() !== 'production') {
        $this->app->register(\Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class);
    }
    // ...
}
```

> 注意：运行生成提示前应先清除编译文件`php artisan clear-compiled`.

## 技术愿景

给一个规范，约束团队成员。

根据微服务思想，要使你的应用高内聚，松耦合，可横向分布式扩展。

* 模块化（[nwidart/laravel-modules](https://nwidart.com)）开发明确边界
* 边界之间需要共享代码库(Core 模块)
* 修改一个模块不应该影响另一个模块的正常使用
* 每一个微服务有信在两个星期内重构
* 架构师应该更关注边界之间的交互，团队成员关注领域（domain）内业务事件，可以高度自治
* 横向分布式架构服务，随时都可以拉取模块代码立刻提供服务

### 什么是高内聚，松耦合？
