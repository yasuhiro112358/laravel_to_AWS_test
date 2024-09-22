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

### phpMyAdminの追加
手動でbackend/docker-compose.ymlに設定を記載
8081ポートに設定

### Dockerの再起動
```
./vendor/bin/sail down
./vendor/bin/sail up
```

### Migration
```
./vendor/bin/sail artisan migrate
```

### AWSの設定
* インスタンスにSSH接続をして行う
```
# Apache Webサーバーのインストール
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

# PHPのインストール
sudo amazon-linux-extras enable php8.0
sudo yum clean metadata
sudo yum install php php-mbstring php-xml php-bcmath php-json php-zip php-pdo php-fpm -y

# Composerのインストール
sudo yum install composer -y

# MyAQLクライアントのインストール
sudo yum install mysql -y

# Gitのインストール
```
sudo yum install git -y
```

# Laravelプロジェクトを配置
cd /var/www
# 自分のリポジトリからクローン
sudo git clone https://github.com/yasuhiro112358/laravel_to_AWS_test
# backendディレクトリをlaravelディレクトリにリネームして移動
sudo mv laravel_to_aws_test/backend laravel
```