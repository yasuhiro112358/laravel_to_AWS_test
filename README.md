# laravel_to_AWS_test

## 手順
```
# 初めからSailの環境を含めて構築
curl -s https://laravel.build/my-laravel-project | bash

# 既存のプロジェクトに追加する場合
php artisan sail:install
```

### Dockerコンテナの起動
```
# Laravelのルートに移動
cd backend

# Dockerを起動
./vendor/bin/sail up
# デフォルトでは80番ポートで起動される
```

### .envの編集
デフォルトで問題なし
デプロイ前にAWSの設定に合わせる

### Dockerの再起動
```
./vendor/bin/sail down
./vendor/bin/sail up
```

### Migration
```
./vendor/bin/sail artisan migrate
```