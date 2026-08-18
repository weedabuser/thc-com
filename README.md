# THC-COM

این شاخه شامل پیکربندی Docker برای راه‌اندازی محلی وردپرس است.

فایل‌های اضافه‌شده در این شاخه:
- docker-compose.yml: پیکربندی سرویس‌های WordPress, MariaDB و phpMyAdmin
- .env.example: متغیرهای محیطی نمونه (کپی کنید به .env و مقادیر امن وارد کنید)
- .gitignore: جلوگیری از کامیت فایل‌های حساس و دِم‌های محلی

قدم‌به‌قدم اجرا (Local development)

1) کلون کردن مخزن و رفتن به شاخهٔ feature/docker-setup

   git clone https://github.com/weedabuser/thc-com.git
   cd thc-com
   git fetch origin
   git checkout feature/docker-setup

2) ساخت فایل .env از روی نمونه و مقداردهی مقادیر

   cp .env.example .env
   # سپس .env را باز کن و مقدار DB_PASSWORD و DB_ROOT_PASSWORD را با پسورد قوی جایگزین کن

3) ساخت پوشهٔ wp-content و تنظیم دسترسی

   mkdir -p wp-content
   # (اختیاری) اگر مشکل دسترسی داشتید:
   sudo chown -R 33:33 wp-content

4) کشیدن ایمیج‌ها و بالا آوردن سرویس‌ها

   docker compose pull
   docker compose up -d

5) بررسی وضعیت سرویس‌ها

   docker compose ps
   docker compose logs -f wordpress

6) باز کردن در مرورگر

   WordPress installer: http://localhost:8080
   phpMyAdmin: http://localhost:8081  (کاربر: مقدار DB_USER در .env)

7) ایمپورت دیتابیس (در صورت وجود dump.sql یا dump.sql.gz)

   # gzip
   gunzip -c dump.sql.gz | docker compose exec -T db mysql -u${DB_USER} -p${DB_PASSWORD} ${DB_NAME}

   # sql ساده
   docker compose exec -T db mysql -u${DB_USER} -p${DB_PASSWORD} ${DB_NAME} < dump.sql

8) تغییر آدرس سایت (اگر نیاز است)

   # در phpMyAdmin جدول wp_options و فیلدهای siteurl و home را به http://localhost:8080 تغییر دهید

نکات امنیتی و عملیاتی
- فایل .env را هیچ‌گاه در گیت کامیت نکنید (.gitignore این را جلوگیری می‌کند).
- برای محیط production از Secrets Manager یا docker secrets استفاده کنید.
- phpMyAdmin برای تولید مناسب نیست؛ یا آن را از Compose حذف کنید یا دسترسی آن را محدود سازید.
- برنامه‌تان را بکاپ بگیرید: از volume db_data و پوشه wp-content/uploads نسخه پشتیبان تهیه کنید.

ایجاد Pull Request

من فایل‌ها را به شاخه feature/docker-setup اضافه کردم. برای ساخت Pull Request از این شاخه به main می‌توانید از gh CLI یا رابط وب استفاده کنید.

با gh CLI:

   gh pr create --base main --head feature/docker-setup --title "Add Docker-based local WordPress setup" --body "Adds docker-compose.yml, .env.example and .gitignore for local WP development. See README for setup instructions."

یا می‌توانید به آدرس زیر در گیت‌هاب مراجعه و Pull Request را از طریق وب باز کنید:

https://github.com/weedabuser/thc-com/compare/main...feature/docker-setup
