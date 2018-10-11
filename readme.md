## 资料

[symfony 官网](https://symfony.com/doc/current/index.html#gsc.tab=0)

正式环境更新包

```
composer install --no-dev
composer update --no-dev
```

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


编辑 composer.json, 修改自动加载机制, 支持`nwidart/laravel-modules`模块化开发，所有模块代码保存在项目根目录`Modules`下, 通过`composer autoload`更新项目自动加载代码

```
"autoload": {
    ...
        "psr-4": {
            ...
            "Modules\\": "Modules/"
        }
    },
```

编辑 composer.json, 如有需要设置composer中国镜像源

* https://packagist.phpcomposer.com
* https://packagist.laravel-china.org/

```
"repositories": {
    "packagist": {
        "type": "composer",
        "url": "https://packagist.phpcomposer.com"
    }
}
```

#### 代码自动完成提示

添加编辑器（`phpstorm`）代码`自动完成`支持

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


到此手动初始化到此为止, 下述内容为克隆本项目的操作

## 本项目意义

无论是手动创建还是克隆本项目,不影响下述使用, 因为核心思想是模块组装, 比如用`Core`模块, 类似于扩张包开发的模式,让你的项目更灵活, 这里的灵魂思想是[nwidart/laravel-modules](https://nwidart.com).

基于模块化思想,结合Git就可以快速组合一些应用, 更新模块, 比起扩张包开发似乎更容易

#### env

手动初始化可以忽略本步骤

如果克隆本项目,需要初始化`.env`文件

```
cp .env.example .env
```

生成APP密钥

```
php artisan key:generate
```

#### 添加核心模块

这一步是必须的, 后续框架会更新此文件, 提供基本的功能

```
mkdir Modules
cd Modules
git clone git@github.com:sawyes/Core.git
```

## 技术愿景

给一个规范，约束团队成员。

根据微服务思想，要使你的应用高内聚，松耦合，可横向分布式扩展。

* 模块化（[nwidart/laravel-modules](https://nwidart.com)）开发明确边界, 严禁不同模块之间的交叉调用（功能性`Core`模块除外，它是共享代码库）,保证模块的独立性，如果模块不能独立部署而是依赖其他模块，那么表示架构失败了
* 边界之间需要共享代码库(Core 模块)
* 修改一个模块不应该影响另一个模块的正常使用
* 每一个微服务有信在两个星期内重构
* 架构师应该更关注边界之间的交互，团队成员关注领域（domain）内业务事件，可以高度自治
* 横向分布式架构服务，随时都可以拉取模块代码立刻提供服务

架构师职责：类似于城市规划师，什么地方建居民区，什么地方建工厂，应该有个大方向的指导，区域内建立什么样式楼层不应该过度干预，团队成员有高度自治的能力。但是当团队走向错误方向，比如居民区没有化粪池，这个时候需要做出正确指导。一个居民区迁移到另一个居民区等规划都是架构师需要考虑的问题。

### 什么是高内聚，松耦合？

所谓**高内聚**是指一个软件模块是由相关性很强的代码组成，只负责一项任务，也就是常说的单一责任原则。通常地因为相同的原因而聚在一起

耦合：模块之间联系越紧密，其耦合性就越强，模块的独立性则越差。

若在程序中出现下列情况之一，则说明两个模块之间发生了内容耦合：
1. 一个模块直接访问另一个模块的内部数据。
2. 一个模块不通过正常入口而直接转入到另一个模块的内部。
3. 两个模块有一部分代码重叠（该部分代码具有一定的独立功能）。
4. 一个模块有多个入口。



