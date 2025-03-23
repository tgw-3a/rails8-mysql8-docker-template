# Rails + MySQL Docker Template

このプロジェクトは、Rails + MySQL 環境を Docker 上で構築するテンプレートです。  
This project is a template for setting up a Rails + MySQL environment using Docker.

以下のいずれかの方法で利用できます：  
You can use it in one of the following ways:

環境に依存するため、必ずエラーがでないとはかぎりません。  
Because this depends on your environment, errors may still occur.  
その場合はエラーに従って対処してください。  
In that case, please follow the error messages and resolve them accordingly.

- **STARTERキットを使わない方法**（ベーステンプレートをもとに構築）  
  Without using the STARTER kit (build from base template)
- **STARTERキットを使う方法**（`rails new` から始める）  
  Using the STARTER kit (initialize with `rails new`)

---

## 🧩 STARTERキットを使わない場合  
## Without Using the STARTER Kit

STARTERキットの `replacement_file` だけを残し、それ以外の STARTER ディレクトリ以下を削除してください。  
Keep only the `replacement_file` from the STARTER directory and delete the rest.

### 手順 / Steps

1. `.env` ファイルを作成  
   Create `.env` file
   ```bash
   cp .env.example .env
   ```

2. Railsの資格情報を初期化  
   Initialize Rails credentials
   ```bash
   rm config/credentials.yml.enc config/master.key
   EDITOR="code --wait" bin/rails credentials:edit
   ```

3. `.env` に `RAILS_MASTER_KEY=` を追加し、`config/master.key` に生成されたキーを貼り付ける  
   Paste the generated key from `config/master.key` into `RAILS_MASTER_KEY=` in `.env`

4. `.gitignore` に `.env` を追加（されていない場合）  
   Add `.env` to `.gitignore` if not already present  
   ※ `config/credentials.yml.enc` も `.gitignore` に入れておくと安全です。  
   It is also recommended to add `config/credentials.yml.enc` to `.gitignore` for security.

5. `.dockerignore` 内の以下をコメントアウトまたは削除（されていない場合）  
   Uncomment or remove the following lines in `.dockerignore` if not
   ```dockerignore
   # /config/master.key
   # /config/credentials/*.key
   ```

6. 秘密鍵を Docker ビルド時に渡す  
   Pass the master key during Docker build
   ```bash
   cp config/master.key .master.key && \
   DOCKER_BUILDKIT=1 docker build \
     --secret id=master_key,src=.master.key \
     -t rails . && \
   rm .master.key
   ```

7. `replacement_file` の中身で対象ファイルを置き換え  
   Replace existing files with those from `replacement_file`

8. コンテナ起動 / Start containers  
   ```bash
   docker compose up -d --build
   ```

9. データベース作成 / Create the database  
   ```bash
   docker compose exec web bundle exec rails db:create
   ```

10. 確認 / Open in browser  
   http://localhost:3000

---

## 🚀 STARTERキットを使う場合  
## Using the STARTER Kit

テンプレートから構築する場合、その他のディレクトリは削除し、STARTER ディレクトリに移動してください。  
Delete other directories and move into the STARTER directory.

### 手順 / Steps

1. STARTER ディレクトリへ移動  
   Move into the STARTER directory

2. `.env` ファイルの作成し、項目を書き換え  
   Create `.env`
   ```bash
   cp .env.example .env
   ```

3. Rails プロジェクトを初期化  
   Initialize Rails project
   ```bash
   docker compose run --rm web rails new . --force --no-deps --database=mysql
   ```

4. 資格情報の初期化 / Initialize credentials  
   ```bash
   rm config/credentials.yml.enc config/master.key
   EDITOR="code --wait" bin/rails credentials:edit
   ```

   .envファイルのMasterKeyに、作成された秘密鍵をペースト  
   Paste the created master key into the RAILS_MASTER_KEY in `.env`.

5. `.gitignore` に `.env` を追加（必要に応じて）  
   Add `.env` to `.gitignore` if needed  
   ※ `config/credentials.yml.enc` も `.gitignore` に入れておくと安全です。  
   It is also recommended to add `config/credentials.yml.enc` to `.gitignore` for security.

6. `.dockerignore` の以下をコメントアウトまたは削除  
   Uncomment or delete the following from `.dockerignore`
   ```dockerignore
   # /config/master.key
   # /config/credentials/*.key
   ```

7. Docker ビルド / Docker build  
   ```bash
   cp config/master.key .master.key && \
   DOCKER_BUILDKIT=1 docker build \
     --secret id=master_key,src=.master.key \
     -t rails . && \
   rm .master.key
   ```

8. `Dockerfile` に以下を追加または置き換え  
   Add or replace in `Dockerfile`
   ```Dockerfile
   # ...（省略 / omitted）...

   # Precompiling assets for production without requiring secret RAILS_MASTER_KEY
   RUN --mount=type=secret,id=master_key \
      RAILS_MASTER_KEY=$(cat /run/secrets/master_key) \
      bundle exec rails assets:precompile
   # Final stage for app image
   ```

   ※ 以下は必要に応じて：  
   Run the following if necessary:

9. `replacement_file` の中身で対象ファイルを置き換え  
   Replace existing files with those from `replacement_file`

10. 今までにできた該当のイメージやボリュームを削除し、
    コンテナdownとコンテナ起動  
    Remove any existing related images and volumes, then bring the containers down and start them again.  

    今までにできた該当のイメージやボリュームを削除。間違えて他の大切なファイルを消さないよう注意してください。  
    Delete any previously created related images and volumes. Be careful not to accidentally remove any important files.
    ```bash
    docker compose down
    docker compose up -d --build
    ```

11. データベース作成 / Create the database  
    ```bash
    docker compose exec web bundle exec rails db:create
    ```

12. 確認 / Open in browser  
    http://localhost:3000

---

## 📝 補足 / Notes

- `replacement_file` は、テンプレートに差し替えるファイル群です。  
  `replacement_file` contains files to be replaced in the main template.
- STARTERキットは `rails new` からセットアップしたい場合に便利です。  
  The STARTER kit is useful if you want to build the project from scratch with `rails new`.

---

## 🙏 ありがとうございます / Thanks

使っていただきありがとうございます！  
Thanks for using this template!
