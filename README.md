# springboot-webapp-template
> Spring Boot + Docker 環境で構築された Web アプリ開発用テンプレート

Spring Boot を使った Web アプリケーションの汎用テンプレートです。  
Web機能、セキュリティ、バリデーション、MyBatisによるDB操作、テンプレートエンジン（Thymeleaf）など、基本的な構成を備えています。  
Docker によるローカル開発環境の立ち上げもサポートしています。



## 📦 使用技術

- Java 21
- Spring Boot 3.4.6
- 開発支援:
  - Lombok
  - Spring Boot Devtools（ホットリロード）
- Spring Boot Modules:
  - spring-boot-starter-web
  - spring-boot-starter-security
  - spring-boot-starter-validation
- テンプレートエンジン:
  - Thymeleaf（spring-boot-starter-thymeleaf + Spring Security 拡張）
- ORマッパー:
  - MyBatis（`mybatis-spring-boot-starter` 3.0.4）
- データベース:
  - PostgreSQL（本番／開発）
  - H2（テスト／インメモリ開発用）
- テスト:
  - JUnit5（基本テスト）
  - Spring Security Test（認証・認可のテスト用）
  - MyBatis Test（Mapperの単体テスト用）
- 開発環境構築:
  - Docker
  - Docker Compose
  - .env による環境変数管理



## 📁 ディレクトリ構成
このテンプレートでは、ルートプロジェクトとバックエンドプロジェクト（example-backend）を分離し、Gradle の **Multi-Project Build** 機能を使って管理しています。これにより、以下のメリットがあります：

- サブプロジェクト（example-backend）単体のビルドや開発がしやすい
- 将来的にフロントエンド（例：**frontend**）など複数モジュールへの拡張がしやすい
- Docker ビルドなどで柔軟に構成できる

> 💡 今回のテンプレートは Thymeleaf と Spring Boot だけで完結するため、**frontend** ディレクトリは存在しません。
> しかし、拡張性が高いテンプレートにするために今回`backend`に分割する構成を採用しています。

```bash
.
├── .env.example                         # 環境変数のテンプレート（ダミーパスワードを記述）
├── .env                                 # 実際の環境変数（.gitignoreに含める）
├── springboot-webapp-template-backend/
│   ├── Dockerfile                           # Spring BootアプリのDockerイメージ
│   ├── build.gradle                         # backendモジュール用のGradle設定
│   └── src/
│       ├── main/
│       │   └── resources/
│       │       ├── application-dev.properties    # 開発環境用DB設定
│       │       └── application-prod.properties   # 本番環境用DB設定
│       └── test/
│           └── resources/
│               └── application-test.properties   # テスト環境用DB設定
├── build.gradle                             # ルートプロジェクトのGradle設定
├── compose.yaml                             # Docker Composeによる複数サービス連携設定
├── settings.gradle
├── Procfile                            # Heroku用の起動定義
├── system.properties                   # Heroku用Javaバージョン指定
```



## 🚀 セットアップ手順（Docker 使用）

> テンプレートをそのまま試したい人向けです。
> クローンしてそのまま動かしてみたいときに使います。アプリの構成や動作確認が目的です。


### 1. リポジトリをクローン

```bash
git clone https://github.com/ktr0203ozeken/springboot-webapp-template.git
cd springboot-webapp-template
```


### 2. `.env.example` をもとに `.env` を作成

```bash
cp .env.example .env
nano .env
```

`.env` ファイルには、使用するデータベース名・ユーザー名・パスワードなどを設定します。


### 3. Dockerで起動（開発用）

```bash
docker compose up
```

このコマンドで以下の2つのサービスが起動されます：

- **Spring Boot アプリケーション**：Webアプリ本体（http://localhost:8080）
- **PostgreSQL データベース**：アプリケーションの永続データ保存用（localhost:5432）


### 4. ブラウザで以下にアクセスして動作を確認

[http://localhost:8080](http://localhost:8080)



## ✅ テンプレートからプロジェクトを開始するには

> このテンプレートをベースに「自分のアプリ」を作りたい人向けです。
> Gitの履歴を切り離し、自分用の新しいリポジトリとして使いたい場合はこちら。


### 1. リポジトリをクローン
```bash
git clone https://github.com/ktr0203ozeken/springboot-webapp-template.git your-example-app
cd your-example-app
rm -rf .git  # テンプレートの Git 履歴を削除
git init     # 新たな Git リポジトリとして初期化
git remote add origin https://github.com/you/your-example-app.git
```


### 2. 設定ファイルを編集

**必ず以下のファイルを編集、作成してください**
> 💡 このテンプレートでは、バックエンドモジュールの実ディレクトリ名は `springboot-webapp-template-backend` ですが、以下では説明の都合上「`backend`」と略記しています。

- 環境・ビルド関連
  - `.env`（`.env.example`をコピーし、**新規作成**）
  - `Dockerfile` （`backend/Dockerfile`配下）
  - `compose.yaml`
  - `settings.gradle`
  - `build.gradle`
  - `backend/settings.gradle`
  - `backend/build.gradle`

- Spring Boot 設定 （`backend/src/main/resources` 配下）
  - `application.properties`（任意）
  - `application-dev.properties`
  - `application-test.properties`
  - `application-prod.properties`

- Heroku デプロイ関連
  - `Procfile`
  - `system.properties`（任意）


## 📄 設定ファイル記述例


### `.env`

以下は `.env` ファイルの記述例です。

PostgreSQL データベースの設定と、Spring Boot アプリケーションの接続設定を定義しています。
このファイルは **docker-compose** および **Spring Boot** によって読み込まれます。

```bash
# Spring Bootのプロファイル
SPRING_PROFILES_ACTIVE=dev

# PostgreSQL（DBコンテナ側）の設定
POSTGRES_DB=example_db
POSTGRES_USER=your_username
POSTGRES_PASSWORD=your_password

# Spring Boot アプリ側の DB 接続情報
SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/example_db
SPRING_DATASOURCE_USERNAME=your_username
SPRING_DATASOURCE_PASSWORD=your_password
```


### `Dockerfile`

以下は、Spring Boot アプリを *multi-stage build* で構築するための `Dockerfile` の記述例です。

```Dockerfile
# Build stage
FROM eclipse-temurin:21-jdk AS build

WORKDIR /app

# ビルドに必要なファイルをコピーして依存関係をキャッシュ
COPY build.gradle settings.gradle gradlew gradlew.bat ./
COPY gradle ./gradle
COPY example-backend/build.gradle ./example-backend/

# gradlewに実行権限を付与（Linux環境）
RUN chmod +x gradlew

# ソースコードをコピーしてビルド
COPY example-backend/src ./example-backend/src
RUN ./gradlew clean build --no-daemon

# Runtime stage
FROM eclipse-temurin:21-jre
WORKDIR /app

# build stage からビルド済み JAR ファイルのみをコピー
COPY --from=build /app/example-backend/build/libs/*.jar app.jar

# アプリ起動
CMD ["java", "-jar", "app.jar"]
```


### `compose.yaml`

以下は、Spring Boot アプリケーションと PostgreSQL を Docker Compose で構成する際の `compose.yaml` の記述例です。

```yaml
services:
  example-backend:
    build:
      context: .
      dockerfile: example-backend/Dockerfile
    env_file:
      - .env
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=${SPRING_PROFILES_ACTIVE}
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
```

### rootの`settings.gradle`

以下は、**Multi-Project Build** 構成のための `settings.gradle` の記述例です。
```gradle
// プロジェクト全体の名前を定義
rootProject.name = 'example'

// backend ディレクトリを Multi-Project Build として含める
include('example-backend')
```


### rootの`build.gradle`

以下は、ルートプロジェクトにおいて **Multi-Project Build** を操作するためのタスク を定義した `build.gradle` の記述例です。

このテンプレートでは、Heroku など一部の PaaS 環境に対応するために、カスタムの `stage` タスクを定義しています。Heroku の Java ビルドパックでは、デフォルトで `./gradlew stage` が実行されるため、このタスクを>定義して bootJar に依存させることで、デプロイ時に自動的に JAR が生成されるようにしています。

```gradle
// Heroku等のデプロイプラットフォーム用
tasks.register("stage") {
    group = "build"
    description = "Prepares application for deployment"
    dependsOn("example-backend:bootJar")
}
```

### `application.properties`

以下は全環境共通の基本設定を定義するための`application.properties`の記述例です。

```properties
# --- エンコーディング設定 ---
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true

# --- Thymeleaf 設定 ---
spring.thymeleaf.mode=HTML
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.servlet.content-type=text/html

# --- メッセージ設定 ---
spring.messages.basename=messages
spring.messages.encoding=UTF-8

# --- 日付/時刻フォーマット ---
spring.mvc.format.date=yyyy-MM-dd
spring.mvc.format.time=HH:mm

# --- ログ設定 ---
logging.level.org.springframework=INFO
logging.level.com.example=DEBUG
```


### `application-dev.properties`

以下は、**ローカル開発環境**（`dev` プロファイル）用の設定例です。

```properties
# == ローカル開発環境のデータベース接続設定 ==

# PostgreSQL の JDBC ドライバ
spring.datasource.driver-class-name=org.postgresql.Driver

# == 接続情報（.env から読み込み）==
spring.datasource.url=${SPRING_DATASOURCE_URL}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD}

# == SQL 初期化設定 ==
# アプリ起動時に schema.sql / data.sql を自動実行
spring.sql.init.mode=always
spring.sql.init.schema-locations=classpath:schema.sql
spring.sql.init.data-locations=classpath:data.sql

# == ログ出力レベル ==
# アプリケーション固有のパッケージに対して DEBUG レベルのログを有効化
logging.level.com.example=DEBUG

# == MyBatis 設定 ==
# Mapper XML ファイルの配置ディレクトリ
mybatis.mapper-locations=classpath:mapper/*.xml
# MyBatisで使うエンティティのパッケージ指定
mybatis.type-aliases-package=com.example.domain.entity

# == Thymeleaf 設定 ==
# HTMLテンプレートのキャッシュ無効化（開発中の変更を即時反映）
spring.thymeleaf.cache=false
```


### `application-test.properties`

以下は、**テスト環境**（`test` プロファイル）用の設定例です。

```properties
#== テスト環境用のデータベース接続設定 ==

# == 接続情報 ==
# H2のURLの設定（PostgreSQL互換モード）
spring.datasource.url=jdbc:h2:mem:testdb;MODE=PostgreSQL
spring.datasource.username=sa
spring.datasource.password=

# == SQL 初期化設定 ==
# アプリ起動時に schema.sql / data.sql を自動実行
spring.sql.init.mode=always
spring.sql.init.schema-locations=classpath:schema-test.sql
spring.sql.init.data-locations=classpath:data-test.sql

# == MyBatis 設定 ==
# Mapper XML ファイルの配置ディレクトリ
mybatis.mapper-locations=classpath:mapper/*.xml
# MyBatisで使うエンティティのパッケージ指定
mybatis.type-aliases-package=com.example.domain.entity
```


### `application-prod.properties`

以下は、**デプロイ環境**（`prod` プロファイル）用の設定例です。

```properties
# == デプロイ環境用のデータベース接続設定 ==

# PostgreSQL の JDBC ドライバ
spring.datasource.driver-class-name=org.postgresql.Driver

# == DB接続設定 ==
# PostgreSQLのURLなどは外部環境変数から読み込む（Heroku等）
spring.datasource.url=${JDBC_DATABASE_URL}
spring.datasource.username=${JDBC_DATABASE_USERNAME}
spring.datasource.password=${JDBC_DATABASE_PASSWORD}

# == SQL 初期化設定 ==
# アプリ起動時に schema.sql / data.sql を実行しない
spring.sql.init.mode=never

# == MyBatis 設定 ==
# Mapper XML ファイルの配置ディレクトリ
mybatis.mapper-locations=classpath:mapper/*.xml
# MyBatisで使うエンティティのパッケージ指定
mybatis.type-aliases-package=com.example.domain.entity

# == Thymeleaf 設定 ==
# Thymeleafのキャッシュを有効にする（パフォーマンス重視）
spring.thymeleaf.cache=true
```


### `Procfile`

以下は、**Heroku** で Spring Boot アプリケーションを起動するための`Procfile`の記述例です。

```Procfile
# backendの JAR ファイルを使ってビルド
web: cd example-backend && java -Dserver.port=$PORT -jar build/libs/example-backend-0.0.1-SNAPSHOT.jar
```


### `system.properties`

以下は、**Heroku**用のJavaバージョン指定をする`system.properties`の記述例です。

```properties
# Javaのバージョンを指定
java.runtime.version=21
```