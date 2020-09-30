# VSCode と .NET Core で WebAPI プロジェクトを作成
以下の流れで、WebAPI の手順を説明していく。

- ソリューションの作成
- WebAPIプロジェクトの作成
- デバッグ動作の確認

## ソリューションの作成
プロジェクトを格納するフォルダを作成する。
```console
mkdir ./first.netcore.api
```

作成したフォルダの中に入る。
```console
cd ./first.netcore.api
```

ソリューションを作成する。
```console
dotnet new sln
```

フォルダ名と同名のslnファイルが作成される。

## WebAPI プロジェクトの作成
webapiフォルダを作成する
```console
mkdir ./api.test
```

作成したフォルダの中に入る。
```console
cd ./api.test
```

WebAPI プロジェクトを作る。
```console
dotnet new webapi
```

## sln ファイルへのプロジェクトの追加
これでプロジェクトの作成はできたが、これだけではソリューションにこのプロジェクトが追加されない。dotnet sln コマンドで sln ファイルに作成したプロジェクトを追加する。

プロジェクトの追加:
```console
dotnet sln ../first.netcore.api.sln add ./api.test.csproj
```

sln ファイル内のコードを見ると、プロジェクトが追加されたのが分かる。

c.sharp.test.sln:
```
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "api.test", "api.test\api.test.csproj", 
    "{CF76536E-59B7-4EAA-8095-6D26762D3380}"
```

cd ../ でslnがあるフォルダに移動してdotnet buildを実行してみます。

```console
dotnet build
```

sln に追加されているプロジェクトが一括でビルドできる。プロジェクト 1 つだけではあまり意味はないが、
クラスライブラリなどを追加していると、ビルドが楽になる。

## タスクの設定
Ctr + Shift + D でデバッグペインを開く。

メニューの [Run] - [Add Configuration...] を選択。デバッグ可能な種類の一覧から[.NET Core]を選択。

### launch.json.program の値の修正
launch.json の内容を確認する。configurations の program プロパティの値に使われている 2 つのタグ、<insert-target-framework-here> と <insert-project-name-here> を解釈できないので、以下のように値を書き換える。値は、実際のフォルダ構成を見て dll の場所を指定する。

Berore:
```
"program": "${workspaceFolder}/bin/Debug/<insert-target-framework-here>/<insert-project-name-here>.dll"
```

After（例）:
```
"program": "${workspaceFolder}/api.test/bin/Debug/netcoreapp3.1/api.test.dll",
```

## デバッグ動作の確認
WeatherForecastController.cs ファイルの HttpGet の return 位置にブレークポイントを張り、デバッグペインの RUN ボタンを押してデバッグを開始する。

エラーなくローカルサーバが起動したら、以下のアドレスにブラウザでアクセスする。

ローカル WebAPI のアドレス:
```
https://localhost:5001/WeatherForecast
```

ブレークポイントで処理が止まっていることを確認する。確認出来たら、Continue で処理を続ける。

Continue するたびに、ローカル変数の index が 1 から 5 までカウントアップしていくのを確認する。

さらに Continue することでブレークから解放され、デバッグが待ちの状態となる。Get レスポンスが返り、JSON データが表示されていることを確認する。

ブラウザではなく、Postman などのツールでレスポンスを見ると以下の通りとなる。

```json
[
    {
        "date": "2020-10-01T17:15:51.1800714+09:00",
        "temperatureC": 49,
        "temperatureF": 120,
        "summary": "Warm"
    },
    {
        "date": "2020-10-02T17:15:51.5907028+09:00",
        "temperatureC": 43,
        "temperatureF": 109,
        "summary": "Freezing"
    },
    {
        "date": "2020-10-03T17:15:51.9924982+09:00",
        "temperatureC": 24,
        "temperatureF": 75,
        "summary": "Bracing"
    },
    {
        "date": "2020-10-04T17:15:52.4619807+09:00",
        "temperatureC": 1,
        "temperatureF": 33,
        "summary": "Cool"
    },
    {
        "date": "2020-10-05T17:15:52.9820952+09:00",
        "temperatureC": 54,
        "temperatureF": 129,
        "summary": "Mild"
    }
]
```
