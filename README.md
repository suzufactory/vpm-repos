# suzufactory vpm-repos

[Add to VCC](https://suzufactory.github.io/vpm-repos/redirect.html)

https://suzufactory.github.io/vpm-repos/vpm.json

## Hikari のバージョン更新手順

Hikari を新しいバージョン（例: `0.3.5`）に更新する手順。

1. HikariEngine のリリースから zip を取得する

   ```sh
   gh release download v0.3.5 --repo suzufactory/HikariEngine \
     --pattern "com.suzufactory.hikari.zip"
   ```

2. zip をこのリポジトリ（vpm-repos）の Releases にアップロードする

   ```sh
   gh release create hikari-0.3.5 --repo suzufactory/vpm-repos \
     --title "Hikari 0.3.5" --notes "Hikari 0.3.5" \
     com.suzufactory.hikari.zip
   ```

3. SHA256 を計算する

   ```sh
   sha256sum com.suzufactory.hikari.zip
   ```

4. `vpm.json` の該当バージョンを書き換える
   - `version` を新バージョンに
   - `url` を vpm-repos の Release アセットに
     （`https://github.com/suzufactory/vpm-repos/releases/download/hikari-<version>/com.suzufactory.hikari.zip`）
   - `zipSHA256` を 3 で得たハッシュに

5. アップロードした Release の URL から再ダウンロードし、ハッシュが一致することを確認する

6. `vpm.json` をコミットして push する

> zip は必ず vpm-repos の Releases に置き、`vpm.json` の `url` もそこを指すこと（HikariEngine の URL は使わない）。
