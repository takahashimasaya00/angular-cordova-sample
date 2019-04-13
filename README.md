# AngularCordovaSample

Angular + Cordovaのサンプル

## プロジェクトの作り方

Angular CLIとApache Cordovaをグローバルインストールする。自分はYarnを用いているのでYarnは事前にインストールしておく。

```bash
yarn global add @angular/cli cordova
ng config -g cli.packageManager yarn
```

Cordova CLIでプロジェクトを作成する。
```bash
cordova create <path>
```

対象のプラットフォームを追加する。
```bash
cd <path>
cordova platform add <platform name>
```

Angularのプロジェクトとマージするため、以下を実行する。

```bash
# 上書きされないよう退避
mv package.json package.json
# Yarnで再インストールするため、一旦削除する。
rm -rf node_modules
rm package-lock.json
```

Angular CLIで同一フォルダにプロジェクトを作成する。

```bash
cd ../
ng new <path> --skip-install --style=scss --routing
# 色々警告出るが気にしない
```

`angular.json`のビルド出力パスを`www`に変更する。

```json
{
  "projects": {
    "angular-cordova-sample": {
      "architect": {
        "build": {
          "options": {
            "outputPath": "www",
          }
        }
      }
    }
  }
}
```

退避しておいた`package.json.bk`を`package.json`にマージする。
`dependencies`, `devDependencies`, `cordova`さえマージしておけば良いと思う。

```json
{
  "dependencies": {
    "@angular/animations": "~7.2.0",
    "@angular/common": "~7.2.0",
    "@angular/compiler": "~7.2.0",
    ...
    "cordova-android": "^8.0.0",
    "cordova-electron": "^1.0.2",
    "cordova-ios": "^5.0.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.13.0",
    "@angular/cli": "~7.3.8",
    "@angular/compiler-cli": "~7.2.0",
    ...
    "cordova-plugin-whitelist": "^1.3.3"
  },
  "cordova": {
    "plugins": {
      "cordova-plugin-whitelist": {}
    },
    "platforms": [
      "electron",
      "ios",
      "android"
    ]
  }
}
```

`src/index.html`のbaseタグを変更する。

```html
<base href="./">
```

Yarnで再インストールする。

```bash
cd <path>
yarn install
```

## Angular開発

Cordova上ではdev serverにアクセスできないので、通常のAngularと同様に`ng serve`で開発する。

```bash
ng serve
```

## Cordova開発

Angularをビルドし、Cordovaでそこにアクセスする。

```bash
ng build
cordova run <platform name>
```

npm-scriptsに設定しておくと楽。

```json
{
  "scripts":{
    "run-electron": "ng build && cordova run electron"
  }
}
```

## ビルド

```bash
ng build
cordova build <platform name>
```

## Git

`platforms`, `plugins`は`config.xml`から再取得可能なので、`.gitignore`に設定してGitの対象から外す。

```
# Cordova
/platforms
/plugins
/www
```

以下のコマンドで再取得する。

```bash
cordova prepare
```
