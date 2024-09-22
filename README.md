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
sudo yum install php php-cli php-mbstring php-xml php-bcmath php-json php-zip php-pdo php-fpm -y
php -v

# Composerのインストール
cd ~
sudo yum install php-cli php-zip wget unzip -y
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

# 最新のシグネイチャを取得
php -r "copy('https://composer.github.io/installer.sig', 'composer-setup.sig'); echo 'Signature: ' . file_get_contents('composer-setup.sig') . PHP_EOL;"
# 取得したシグネイチャを記載する
php -r "if (hash_file('SHA384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
composer --version

# Gitのインストール
sudo yum install git -y

# Laravelプロジェクトを配置
cd /var/www
# 自分のリポジトリからクローン
sudo git clone https://github.com/yasuhiro112358/laravel_to_AWS_test
# backendディレクトリをlaravelディレクトリにリネームして移動
sudo mv laravel_to_AWS_test/backend laravel
# 一時ファイルの削除
sudo rm -rf laravel_to_AWS_test

# Install dependencies
cd /var/www/laravel
sudo COMPOSER_ALLOW_SUPERUSER=1 composer install

# 権限の設定
sudo chown -R apache:apache /var/www/laravel
sudo chmod -R 775 /var/www/laravel/storage /var/www/laravel/bootstrap/cache

# Apacheの設定変更
sudo nano /etc/httpd/conf.d/laravel.conf

# この内容を記載
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/laravel/public
    ServerName your-ec2-ip

    <Directory /var/www/laravel/public>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/httpd/laravel-error.log
    CustomLog /var/log/httpd/laravel-access.log combined
</VirtualHost>

#
sudo nano /etc/httpd/conf/httpd.conf
* ファイル内でAllowOverride NoneをAllowOverride Allに変更
<Directory "/var/www">
    AllowOverride All
    Require all granted
</Directory>

# Apacheの再起動
sudo systemctl restart httpd

# .envファイルの編集
sudo nano /var/www/laravel/.env

* 設定例
APP_URL=http://your-ec2-ip
DB_CONNECTION=mysql
DB_HOST=your-rds-endpoint
DB_PORT=3306
DB_DATABASE=your-database-name
DB_USERNAME=your-username
DB_PASSWORD=your-password
# おそらくAPP_URLとDB_HOSTのみ

# キャッシュのクリア
sudo php artisan config:clear
sudo php artisan route:clear
sudo php artisan cache:clear


```
