# MacOSFilesystemについて

## 目次

保安
サンドボックス

### 保安について

#### サンドボックス

UNIXの最小権限原則

コード署名（Code Signing）
SSLと同じ認証書を使用してアプリの出所を検証し確認して変造有無を保証
署名されたないアプリが実行しようとする場合カーネルで終了（SIGKILL）される
脱獄(jailbreaking)を難しくする

区画化（Compartmentalization（Sandboxing））
アプリは隔離された環境で実行するべき
過去Leopardではseatbelt以後sandboxに名前が変えてLionで改善された
sandboxは監獄を生成しアプリに制限を与える

- ディレクトリ制限
アプリは自分のディレクトリ(/var/mobile/Applications/<app-GUID>)を事実上ルートでみなして他のアプリに関する情報やシステムファイルに接近できない

- プロセス隔離
アプリ