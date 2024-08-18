# todoapi

## はじめに

* バージョン構成
  JAVA: openjdk@17
  環境パス： openjdk@17
  エディタ: IntelliJ IDEA Community Edition
  拡張機能: 
    OpenAPI (Swagger) Editor
    Japan Language Pack / 日本語パック
  spring initializr:
    Project: Gradle - Groovy
    Language: Java
    Spring Boot: 3.3.2
  APIサーバー: OpenAPI 3.0.0
  APIクライアント: Postman 11.8.1
  ソース管理: GitHub

## Mac-IntelliJ IDEAショートカット
* オブジェクト検索　　　｜　shift + command + P
* クラス参照　　　　　　｜　command + B　｜　マウスオーバー
* メソッドオーバーライド｜　control + O　｜　[コード]-[メソッドのオーバーライド]
* デバッグ実行　　　　　｜　control + shift + D

## 成果物定義
* todoapi/build.gradle
  ```java
  plugins {
    id 'java'
    id 'org.springframework.boot' version '3.0.4'
    id 'io.spring.dependency-management' version '1.1.0'
    id "org.openapi.generator" version "6.4.0"
  }

  group = 'com.example'
  version = '0.0.1-SNAPSHOT'
  sourceCompatibility = '17'

  configurations {
    compileOnly {
      extendsFrom annotationProcessor
    }
  }

  repositories {
    mavenCentral()
  }

  dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.0'
    implementation 'org.openapitools:jackson-databind-nullable:0.2.1'
    implementation 'io.swagger.core.v3:swagger-annotations:2.2.8'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
  }

  tasks.named('test') {
    useJUnitPlatform()
  }

  task buildApiDoc(type: org.openapitools.generator.gradle.plugin.tasks.GenerateTask){
    generatorName = "html2"
    inputSpec = "$rootDir/src/main/resources/api-schema.yaml".toString()
    outputDir = "$buildDir/apidoc".toString()
  }

  task buildSpringServer(type: org.openapitools.generator.gradle.plugin.tasks.GenerateTask){
    generatorName = "spring"
    inputSpec = "$rootDir/src/main/resources/api-schema.yaml".toString()
    outputDir = "$buildDir/spring".toString()
    apiPackage = "com.example.todoapi.controller"
    modelPackage = "com.example.todoapi.model"
    configOptions = [
        interfaceOnly: "true",
        useSpringBoot3: "true"
    ]
  }

  openApiValidate {
    inputSpec = "$rootDir/src/main/resources/api-schema.yaml"
  }

  compileJava.dependsOn tasks.buildSpringServer
  sourceSets.main.java.srcDir "$buildDir/spring/src/main/java" 
  ```

* todoapi/src/main/resources/schema.yaml
  ```yaml
  openapi: "3.0.0"
  info:
    title: TODO API Document
    version: "0.0.1"
    description: TODO APIのドキュメントです
  paths:
    /health:
      get:
        responses:
          '200':
            description: OK
  ```

* todoapi/src/main/java/com.example.todoapi/controller/HealthController
  ```java
  package com.example.todoapi.controller;

  import org.springframework.http.ResponseEntity;
  import org.springframework.web.bind.annotation.RestController;

  @RestController
  public class HealthController implements HealthApi {

      @Override
      public ResponseEntity<Void> healthGet() {
          return ResponseEntity.ok().build();
      }
  }
  ```

* todoapi/.gitignore
  ```java
  HELP.md
  .gradle
  build/
  !gradle/wrapper/gradle-wrapper.jar
  !**/src/main/**/build/
  !**/src/test/**/build/

  ### STS ###
  .apt_generated
  .classpath
  .factorypath
  .project
  .settings
  .springBeans
  .sts4-cache
  bin/
  !**/src/main/**/bin/
  !**/src/test/**/bin/

  ### IntelliJ IDEA ###
  .idea
  *.iws
  *.iml
  *.ipr
  out/
  !**/src/main/**/out/
  !**/src/test/**/out/

  ### NetBeans ###
  /nbproject/private/
  /nbbuild/
  /dist/
  /nbdist/
  /.nb-gradle/

  ### VS Code ###
  .vscode/

  # Logs (Original)
  *.log

  # OS generated files (Original)
  .DS_Store

  # Other files (Original)
  *.class
  ```

## 開発環境セットアップ

1. JDKインストール

  * Homebrewを用いてJDK17をインストール
    ```terminal
    brew install openjdk@17
    ```

2. 環境パス設定（openjdk@17を環境パス登録）

  * `openjdk@17`をシステムのJavaディレクトリにシンボリックリンクする
    ```terminal
    sudo ln -sfn /usr/local/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
    ```

  * `openjdk@17`を環境パスの最初に追加する
    ```terminal
    echo 'export PATH="/usr/local/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
    ```

  * 環境パスの変更を即時反映させる（.zshrcファイルを再読込して即時反映）
    ```terminal
    source ~/.zshrc
    ```

  * Javaバージョン確認
    ```terminal
    java -version
    ```

3. IntelliJ IDEAの無料版をインストール

  * JetBrains Toolbox Appインストール
    * 今回はMac(IntelChip)をダウンロードしてインストール
    * https://www.jetbrains.com/ja-jp/toolbox-app/

  * IntelliJ IDEA Community Editionインストール
    * JetBrains Toolbox Appにて「IntelliJ IDEA Community Edition」をインストール
    * 無料版でもSpringboot開発において必要十分（他の言語やFW開発もするには有償版推奨）

4. 拡張機能インストール

  * OpenAPI (Swagger) Editor
    * 拡張機機能インストール
      * [IntelliJ IDEA] - [Settings] - [Plugins] - [Marcketplace]にて、
      「OpenAPI」と入力して検索し、「OpenAPI (Swagger) Editor」をインストール

  * Japan Language Pack / 日本語パック
    * 拡張機機能インストール済を確認
      * [IntelliJ IDEA] - [Settings] - [Plugins] - [Installed]にて、
      「Japan Language Pack / 日本語パック」のEnableを確認
    * 日本語化
      * [IntelliJ IDEA] - [Settings] - [Appearance & Behavior] - [System Settings] - [Language and Region]にて、[Language]を「Japanese 日本語」に変更

5. spring initializrからSpringの雛形をダウンロード（後述build.gradleで実装するバージョン優先）

  * https://start.spring.io/
    * Project       Gradle - Groovy
    * Language      Java
    * Spring Boot   3.3.2
    * Project Metadata
        * Group         com.example
        * Artifact      todoapi
        * Name          todoapi
        * Description   todoapi
        * Package name  com.example.todoapi
        * Packaging     Jar
        * Java          17
    * Dependencies
        * Spring Web
  * 上記を設定して、[Generate]をクリック

  * ここでSpringboot3.3.2指定で雛形ダウンロードするが、
  後述build.gradleにてSpringboot3.0.4として実装する

6. 開発ディレクトリを用意

  * 開発ディレクトリ作成
    ```terminal
    # 開発ディレクトリに移動
    cd Development
    mkdir todoapi        // todoapiディレクトリが無ければ作成
    cd todoapi           // todoapiディレクトリに移動
    ```

  * IntelliJ IDEAでプロジェクトを開く
    * spring initializrで作成したSpringの雛形zipを展開してプロジェクトフォルダに設置
    * IDEAでtodoapiプロジェクトを新規作成して開発ディレクトリを読込

  * IntelliJ IDEAで初回起動
    * IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [src] - [main] - [java] - [...] - [TodoApiApplication]をダブルクリック
    * [▶️]をクリックして、[TodoApiApplication]を実行します。
    * ブラウザで、 http://localhost:8080 を開いて初期画面を確認します

7. OpenAPI（スキーマ駆動開発のAPI基本設計）

  * スキーマファイルの作成場所
    * ${PROJECT_ROOT}/src/main/resources/api-schema.yaml
        ```yaml
        openapi: "3.0.0"
        info:
            title: TODO API Document
            version: "0.0.1"
            description: TODO APIのドキュメントです
        paths:
            /health:
                get:
                    responses:
                        '200':
                            description: OK
        ```

  * 【参考文献】スキーマ記法（OpenAPI Specification v3.0.0）
    * OpenAPIとGeneratorのバージョンを合わせて、今回はOpenAPI 3.0.0の下記の仕様で開発する。
    https://spec.openapis.org/oas/v3.0.0.html#schema
      * REQUIREDが記述が必要な必須項目
      * OPENAPIの最上位層には、openapiとinfoとpathsの記載が必要なことが分かる

  * 【参考文献】Online YAML Parser
    * YAML記法の練習が行えるサイト
    https://yaml-online-parser.appspot.com/

8. Springジェネレーター（スキーマ駆動開発のBE側基本設計）

  * JAVA側(build.gradle)のpluginsに「org.openapi.generator」を追加
    ```java
    plugins {
      id 'java'
      id 'org.springframework.boot' version '3.0.4'
      id 'io.spring.dependency-management' version '1.1.0'
      id "org.openapi.generator" version "6.4.0"
    }
    ```

  * Gradleプロジェクト更新
    * IDEAの右上側にある[Gradle] - [🔄]にてGradleプロジェクトを更新
    * [Gradle] - [todoapi] - [Tasks] - [openapi tools] - [openApiGenerators]をダブルクリックでタスク実行して、ジェネレーター一覧をターミナルで確認することもできます

  * 【参考文献】OpenAPI Generator (Gradle Plugin)
  APIスキーマからJAVA側のもろもろを生成するジェネレーター
  https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-gradle-plugin#plugin-setup

9. APIドキュメント生成（スキーマからAPIドキュメント生成）

  * JAVA側(build.gradle)の末尾にタスク複数設定コードを実装
    ```java
    task buildApiDoc(type: org.openapitools.generator.gradle.plugin.tasks.GenerateTask){
      generatorName = "html2"
      inputSpec = "$rootDir/src/main/resources/api-schema.yaml".toString()
      outputDir = "$buildDir/apidoc".toString()
    }
    ```

  * Gradleプロジェクト更新
    * IDEAの右上側にある[🔄]（Gradleの変更を読み込む）（Load Gradle Changes）にて設定を反映
      * [Gradle] - [todoapi] - [Tasks] - [others] - [buildApiDoc]をダブルクリックでタスク実行してAPI設計書のHTMLが生成されます
      * IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [build] - [apidoc] - [index.html]を右クリックして[開く] - [ブラウザ] - [Chrome]をクリックして、Swagger-UIを確認できるようになります

  * 【参考文献】OpenAPI GitHub（タスク複数設定コード）
  https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-gradle-plugin#generate-multiple-sources

  * 【参考文献】OpenAPI Generator（html2ジェネレーター仕様）
  https://openapi-generator.tech/docs/generators/html2

10. Springコード生成（スキーマからSpringコード生成）

  * JAVA側(build.gradle)の末尾にタスク複数設定コードを実装
    ```java
    task buildSpringServer(type: org.openapitools.generator.gradle.plugin.tasks.GenerateTask){
      generatorName = "spring"
      inputSpec = "$rootDir/src/main/resources/api-schema.yaml".toString()
      outputDir = "$buildDir/spring".toString()
      apiPackage = "com.example.todoapi.controller"
      modelPackage = "com.example.todoapi.model"
      configOptions = [
          interfaceOnly: "true",
          useSpringBoot3: "true"
      ]
    }
    ```

  * Gradleプロジェクト更新
    * IDEAの右上側にある[🔄]（Gradleの変更を読み込む）（Load Gradle Changes）にて設定を反映
      * [Gradle] - [todoapi] - [Tasks] - [others] - [buildSpringServer]をダブルクリックでタスク実行してSpringコードが生成されます
      * IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [build] - [spring] - [src]にて、自動生成されたSpringコードを確認できます。後述の自動インポートの設定でこのjavaコードを利用できるようになります

  * 【参考文献】OpenAPI GitHub（タスク複数設定コード）
  https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-gradle-plugin#generate-multiple-sources

  * 【参考文献】OpenAPI Generator（Springジェネレーター仕様）
  https://openapi-generator.tech/docs/generators/spring

11. Gradleタスクの依存関係の設定

  * JAVA側(build.gradle)末尾にGradleタスク依存関係設定コードを実装
    ```java
    compileJava.dependsOn tasks.buildSpringServer
    ```

  * Gradleプロジェクト更新
    * IDEAの右上側にある[🔄]（Gradleの変更を読み込む）（Load Gradle Changes）にて設定を反映
      * [Gradle] - [todoapi] - [Tasks] - [others] - [compile.Java]をダブルクリックでタスク実行を確認すると、Springコード生成をしてからJAVAコンパイルが走ることを確認できます
      * これは、IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [build] - [spring]を削除しても、「compile.Java」のタスクを実行すると最初にSrpingコード生成をして[spring]がビルドされからJAVAコンパイルが実行されるようになります

  * 【参考文献】OpenAPI GitHub（Gradleタスク依存関係設定コード）
  https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-gradle-plugin#tasks

12. Spring自動生成コードのインポート設定

  * IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [src] - [main] - [java] - [com.example.todoapi]を右クリックして[新規] - [Java クラス]をクリックして、「controller.HealthController」を作成
    ```java
    package com.example.todoapi.controller;

    import org.springframework.http.ResponseEntity;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HealthController implements HealthApi {

        @Override
        public ResponseEntity<Void> healthGet() {
            return ResponseEntity.ok().build();
        }
    }
    ```

  * JAVA側(build.gradle)末尾にSpring自動生成コードのインポート設定を実装
    ```java
    sourceSets.main.java.srcDir "$buildDir/spring/src/main/java"
    ```

  * Gradleプロジェクト更新
    * IDEAの右上側にある[🔄]（Gradleの変更を読み込む）（Load Gradle Changes）にて設定を反映
      * これは、IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [build] - [spring]に自動生成したSpringコードのクラスを、本プロジェクトで利用できるようになります

13. ライブラリ導入

  * build.gradleのdependenciesに導入ライブラリを追加
    ```java
    dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-web'
      implementation 'org.springframework.boot:spring-boot-starter-validation'
      implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.0'
      implementation 'org.openapitools:jackson-databind-nullable:0.2.1'
      implementation 'io.swagger.core.v3:swagger-annotations:2.2.8'
      compileOnly 'org.projectlombok:lombok'
      developmentOnly 'org.springframework.boot:spring-boot-devtools'
      runtimeOnly 'com.h2database:h2'
      annotationProcessor 'org.projectlombok:lombok'
      testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
    ```

  * IntelliJ IDEAで動作起動
    * IDEAの左上側にあるプロジェクトツリーにて、[todoapi] - [src] - [main] - [java] - [...] - [TodoApiApplication]をダブルクリック
    * [▶️]をクリックして、[TodoApiApplication]を実行します。
    * ブラウザで、 http://localhost:8080/health を開いて白頁が表示されることを確認します

14. Postmanで疎通確認

  * Postmanのデスクトップアプリをダウンロードしてインストール
    * https://www.postman.com/

  * Postmanで疎通確認
    * Postmanを起動
    * [Collections]-[+]をクリックして新規コレクションを作成
    * [GET]の[http://localhost:8080/health]にて[Send]をクリックして疎通確認

15. Git導入

  * プロジェクトの.gitignoreにGit管理の除外ファイル指定を確認
    * todoapi/.gitignore

  * Gitの初期化
    まず、プロジェクトディレクトリに移動し、Gitを初期化します。
    ```sh
    cd /Development/todoapi  # プロジェクトディレクトリに移動
    git init                 # Gitリポジトリを初期化
    ```

  * GitHubでリポジトリを作成
    * GitHubにログインします。
    * 右上の「New repository」ボタンをクリックします。
    * 以下の情報を入力します。
      * Repository name: プロジェクト名（例: springboot_todoapi）
      * Description: オプションでプロジェクトの説明を入力します。
      * Visibility: 「Public」または「Private」を選択します。
      * 「Create repository」ボタンをクリックしてリポジトリを作成します。

  * リモートリポジトリを設定
    * GitHubで作成したリポジトリにプロジェクトをプッシュするために、リモートリポジトリのURLをGitに設定します。
    * GitHubのリポジトリ作成ページで表示されるリモートリポジトリのURLを使います。例えば、次のようなコマンドでリモートを追加します。
      ```sh
      git remote add origin https://github.com/your-username/springboot_todoapi.git
      ```

  * ファイルのステージングとコミット
  次に、プロジェクトの全ファイルをGitで管理するためにステージングします。
    ```sh
    git add .  # すべてのファイルをステージング
    git commit -m "Initial commit"  # 初回のコミットメッセージを付けてコミット

  * GitHubに初回プッシュ
  コミットが完了したら、GitHubにプッシュします。
    ```sh
    git push -u origin main  # 初回プッシュでmasterブランチを指定
    ```

## 参考文献
  * https://github.com/poco-tech/poco-tech-spring-boot-web-api/tree/main