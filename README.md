# laravel_to_AWS_test

## 手順
```
# 初めからSailの環境を含めて構築
curl -s https://laravel.build/my-laravel-project | bash

# 既存のプロジェクトに追加する場合
php artisan sail:install
```

```
# Laravelのルートに移動
cd backend

# Dockerを起動
./vendor/bin/sail up

# Migration
./vendor/bin/sail artisan migrate
```