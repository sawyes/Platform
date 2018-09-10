#### 创建项目


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

编辑 composer.json, 添加如下包, 其中`nwidart/laravel-modules`是必须的

- 官方文档[nwidart/laravel-modules](https://nwidart.com)
- GitHub[Nicolas Widart](https://github.com/nwidart)
- 官方文档[phpoffice/phpspreadsheet](https://phpspreadsheet.readthedocs.io/en/develop/)
- github[phpoffice/phpspreadsheet](https://github.com/PHPOffice/PhpSpreadsheet)


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