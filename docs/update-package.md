# パッケージの更新手順

vpm-repos に登録されているパッケージを新しいバージョンに更新する手順。

以下では次のプレースホルダを使う（Hikari を `0.3.5` に更新する場合の例を併記）。

| プレースホルダ | 意味 | 例 |
| --- | --- | --- |
| `<package-id>` | パッケージ ID | `com.suzufactory.hikari` |
| `<source-repo>` | zip 配布元のリポジトリ | `suzufactory/HikariEngine` |
| `<source-tag>` | 配布元のリリースタグ | `v0.3.5` |
| `<zip-name>` | zip アセットのファイル名 | `com.suzufactory.hikari.zip` |
| `<release-tag>` | vpm-repos 側で作るリリースタグ | `hikari-0.3.5` |
| `<version>` | パッケージのバージョン | `0.3.5` |

## 手順

1. 配布元のリリースから zip を取得する

   ```sh
   gh release download <source-tag> --repo <source-repo> --pattern "<zip-name>"
   ```

2. zip をこのリポジトリ（vpm-repos）の Releases にアップロードする

   ```sh
   gh release create <release-tag> --repo suzufactory/vpm-repos --title "<title>" --notes "<notes>" <zip-name>
   ```

3. SHA256 を計算する

   ```sh
   sha256sum <zip-name>
   ```

4. `vpm.json` の該当パッケージのバージョンを書き換える
   - `version` を新バージョンに
   - `url` を vpm-repos の Release アセットに
     （`https://github.com/suzufactory/vpm-repos/releases/download/<release-tag>/<zip-name>`）
   - `zipSHA256` を 3 で得たハッシュに

5. アップロードした Release の URL から再ダウンロードし、ハッシュが一致することを確認する

   ```sh
   curl -sL -o check.zip "https://github.com/suzufactory/vpm-repos/releases/download/<release-tag>/<zip-name>"
   sha256sum check.zip
   ```

6. `vpm.json` をコミットして push する

> zip は必ず vpm-repos の Releases に置き、`vpm.json` の `url` もそこを指すこと（配布元リポジトリの URL は使わない）。

## 旧 Release の削除（任意）

過去バージョンの Release（とタグ）の削除は、**明示的に指示されたときのみ**行う。更新手順の一部として自動的には行わない。

削除する場合は、現行バージョン以外の `<package-id>` の Release を対象にする（他パッケージ、例: `scm-*` は削除しない）。

```sh
gh release delete <old-release-tag> --repo suzufactory/vpm-repos --cleanup-tag --yes
```

## 例: Hikari 0.3.5 への更新

```sh
gh release download v0.3.5 --repo suzufactory/HikariEngine --pattern "com.suzufactory.hikari.zip"

gh release create hikari-0.3.5 --repo suzufactory/vpm-repos --title "Hikari 0.3.5" --notes "Hikari 0.3.5" com.suzufactory.hikari.zip

sha256sum com.suzufactory.hikari.zip
```

`vpm.json` の `url` は
`https://github.com/suzufactory/vpm-repos/releases/download/hikari-0.3.5/com.suzufactory.hikari.zip`。
