معرفی
ربات ایتا یک ابزار خودکار برای تعامل با پیام‌رسان ایتا (Eitaa) از طریق وب است. این ربات با استفاده از Selenium و اتوماسیون مرورگر کار می‌کند و قابلیت‌های زیر را ارائه می‌دهد:

ارسال و دریافت پیام (متن، عکس، ویدیو، صوت، فایل)

مدیریت گروه‌ها و کانال‌ها

ضبط صدای سیستم

مدیریت مخاطبین

و امکانات پیشرفته دیگر

توجه: این ربات برای web.eitaa.com طراحی شده و با ورژن ۲ مرورگر کروم یا فایرفاکس کار می‌کند.

نصب و راه‌اندازی
پیش‌نیازها
# نصب کتابخانه‌های مورد نیاز
pip install selenium requests comtypes pillow pyperclip colorama soundcard soundfile pycaw win32clipboard

# نصب WebDriver
# برای کروم: chromedriver
# برای فایرفاکس: geckodriver
ساختار اولیه
```python
from bot import Bot

# ایجاد نمونه از ربات
bot = Bot(
    headless=False,  # اجرا در حالت بی‌صدا (بدون نمایش مرورگر)
    autologin=True,  # استفاده از اطلاعات ذخیره شده قبلی
    Browser="2"      # "1" برای فایرفاکس، "2" برای کروم
)
```
کلاس‌ها و متدها
کلاس اصلی: Bot
سازنده کلاس (Constructor)
```python
Bot(headless: bool, autologin: bool, Browser: str = "2")
پارامترها:

headless: اجرای مرورگر در حالت بی‌صدا (بدون نمایش رابط کاربری)

autologin: استفاده از اطلاعات ذخیره شده قبلی برای ورود خودکار

Browser:انتخاب مرورگر ("1" = فایرفاکس، "2" = کروم)
```
متدهای مدیریت پیام

1. ارسال پیام متنی
python
send_message(chat_id: str, text: str) -> dict
توضیح: ارسال پیام متنی به یک چت مشخص

پارامترها:

chat_id: شناسه عددی چت مقصد

text: متن پیام

مثال:

result, message_element = bot.send_message("123456789", "سلام! این یک پیام تست است")
2. پاسخ به پیام
reply_to_message(text: str, chat_id: str, msg_id: str) -> dict
توضیح: پاسخ به یک پیام خاص در چت

پارامترها:

text: متن پاسخ

chat_id: شناسه چت

msg_id: شناسه پیامی که می‌خواهید به آن پاسخ دهید

مثال:

bot.reply_to_message("ممنون!", "123456789", "987654321")
3. ویرایش پیام
python
edit_message(new_text: str, message_element) -> None
توضیح: ویرایش یک پیام قبلی (فقط پیام‌های خودتان)

پارامترها:

new_text: متن جدید

message_element: المنت پیام (از متدهای دیگر دریافت می‌شود)

مثال:

# ابتدا پیام را پیدا کنید، سپس ویرایش کنید
message = bot.messageIdtoMap("987654321")
bot.edit_message("متن ویرایش شده", message)
4. فوروارد پیام
forward_message(target: str, message_element, quote: bool) -> tuple
توضیح: فوروارد کردن پیام به چت دیگر

پارامترها:

target: نام یا شناسه چت مقصد

message_element: المنت پیام

quote: اگر True باشد، پیام با نقل قول فوروارد می‌شود

5. پین کردن پیام
pin_message(message_element) -> None
توضیح: پین کردن پیام در بالای چت (فقط ادمین‌ها یا صاحب چت)

6. حذف پیام
delete_message(message_element) -> None
توضیح: حذف پیام از چت

متدهای دریافت پیام
1. دریافت پیام‌های جدید
on_new_message(chat_index: int) -> dict
توضیح: دریافت پیام‌های جدید (نخوانده) یک چت

پارامترها:

chat_index: ایندکس چت در لیست چت‌ها (شروع از 1)

خروجی:

{
    "result-1": {
        "message_id": "123",
        "link": "https://...",
        "chat": {
            "id": "456",
            "title": "اسم چت",
            "username": "username",
            "type": "chat_type"
        },
        "date": "2024-01-01 12:00:00",
        "text": "متن پیام",
        "is_from_me": false,
        "media": {
            "media-src": "https://..."
        }
    },
    # ... پیام‌های دیگر
}
2. دریافت همه پیام‌های چت
on_all_message(chat_index: int) -> dict
توضیح: دریافت همه پیام‌های یک چت (تاریخچه کامل)

3. دریافت آخرین پیام چت جاری
onchatupdate() -> tuple
توضیح: دریافت آخرین پیام ارسال یا دریافت شده در چت جاری

خروجی: (response_dict, message_element)

متدهای مدیریت چت
1. تغییر چت جاری
go_chat(chat_id: str) -> None
توضیح: رفتن به یک چت مشخص با شناسه

مثال:

bot.go_chat("123456789")  # رفتن به چت با شناسه 123456789
2. گرفتن شناسه چت جاری

chat_id() -> str
توضیح: دریافت شناسه چتی که در حال حاضر در آن هستید

3. حذف چت
delete_chat() -> None
توضیح: حذف چت جاری از لیست چت‌ها

4. دریافت اطلاعات چت
get_info(chat_id: str) -> str
توضیح: دریافت اطلاعات کامل یک چت (نام، بیو، وضعیت، شماره، etc.)

خروجی: رشته JSON شامل اطلاعات

{
    'name': 'نام کاربر',
    'status': 'آنلاین',
    'bio': 'بیوگرافی',
    'phone': '09123456789',
    'username': 'username',
    'chat_id': '123456'
}
متدهای مدیریت رسانه
1. ارسال آلبوم (چند عکس)
send_album(file_paths: list, caption: str, send_compressed: bool) -> None
توضیح: ارسال چند عکس به صورت آلبوم

پارامترها:

file_paths: لیست مسیرهای فایل‌های عکس

caption: کپشن (توضیحات)

send_compressed: ارسال با کیفیت فشرده (True) یا اصلی (False)

مثال:

```python
bot.send_album(
    ["photo1.jpg", "photo2.jpg"], 
    "عکس‌های سفر",
    True
)
```
2. ارسال فایل‌های دیگر
send_other(file_paths: list, caption: str, send_compressed: bool) -> None
توضیح: ارسال فایل‌های غیر عکس (PDF, ZIP, etc.)

3. ضبط صدا
save_audio(duration: int) -> None
توضیح: ضبط صدای سیستم (خروجی اسپیکر) به مدت مشخص

پارامترها:

duration: مدت زمان ضبط به ثانیه

خروجی: فایل output.mp3 ذخیره می‌شود

متدهای مدیریت صدا (سیستم)
1. گرفتن برنامه‌های در حال پخش صدا
list_active_sessions() -> list
توضیح: لیست برنامه‌هایی که در حال حاضر صدا پخش می‌کنند

خروجی: لیستی از (session, process_name, pid)

2. قطع صدای برنامه‌ها
mute_sessions(active_sessions: list) -> None
توضیح: قطع صدای برنامه‌های مشخص شده

3. وصل کردن صدای برنامه‌ها
unmute_sessions(active_sessions: list) -> None
توضیح: برگرداندن صدای برنامه‌های قطع شده

متدهای مدیریت گروه
1. گرفتن اعضای گروه
get_members(chat_id: str = None, return_map: bool = False) -> list
توضیح: دریافت لیست اعضای گروه جاری

پارامترها:

chat_id: شناسه کاربر خاص برای جستجو

return_map: اگر True باشد، المنت عضو را هم برمی‌گرداند

مثال:

members = bot.get_members()
print(f"تعداد اعضا: {len(members)}")
2. افزودن عضو به گروه
add_member(targets: list) -> None
توضیح: افزودن اعضای جدید به گروه جاری

پارامترها:

targets: لیست نام کاربری یا شماره موبایل اعضا

مثال:

bot.add_member(["@username1", "@username2"])
3. ترفیع به ادمین
promote_member(member_element, rights: list) -> None
توضیح: ترفیع یک عضو به ادمین با حقوق مشخص

پارامترها:

member_element: المنت کاربر (از get_members دریافت می‌شود)

rights: لیست حقوق (۰ تا ۷) - هر عدد نشان‌دهنده یک دسترسی

حقوق (اعداد):

۳: حذف پیام

۴: بن کاربر

۵: پین پیام

۶: اضافه کردن کاربر

۷: ویرایش اطلاعات گروه

4. حذف ادمین
delete_admin(member_element) -> None
توضیح: گرفتن مقام ادمین از یک کاربر

5. حذف عضو از گروه
delete_member(member_element) -> None
توضیح: حذف عضو از گروه (اخراج)

متدهای مدیریت مخاطبین
1. افزودن مخاطب جدید
add_contact(phone_number: str, name: str) -> str
توضیح: افزودن مخاطب جدید با شماره تلفن

پارامترها:

phone_number: شماره موبایل (با فرمت ۰۹۱۲... یا +98912...)

name: نام مخاطب

خروجی: شناسه چت مخاطب

مثال:

chat_id = bot.add_contact("09123456789", "علی رضایی")
2. گرفتن لیست مخاطبین
load_all_contacts(limit: int) -> list
توضیح: دریافت لیست همه مخاطبین

پارامترها:

limit: حداکثر تعداد مخاطبین برای بارگیری

خروجی: لیست شناسه‌های چت مخاطبین

متدهای مدیریت کانال
1. ساخت کانال جدید
create_channel(name: str, bio: str) -> str
توضیح: ایجاد کانال جدید با نام و توضیحات

پارامترها:

name: نام کانال

bio: توضیحات کانال

خروجی: رشته JSON شامل اطلاعات کانال

مثال:

channel_info = bot.create_channel("کانال خبری", "آخرین اخبار روز")
متدهای جستجو
search(tab_index: int, text: str) -> str
توضیح: جستجو در پیام‌ها، مخاطبین، گروه‌ها یا کانال‌ها

پارامترها:

tab_index: تب جستجو (۱=پیام‌ها، ۲=مخاطبین، ۳=گروه‌ها، ۴=کانال‌ها)

text: متن جستجو

خروجی: شناسه پیام یافت شده

مثال:

message_id = bot.search(1, "سلام")  # جستجوی "سلام" در پیام‌ها
متدهای کاربردی دیگر
1. اسکرول صفحه
scroll() -> None
توضیح: اسکرول ۳۰ پیکسل به پایین در صفحه جاری

2. رفتن به تنظیمات
go_settings() -> None
توضیح: رفتن به صفحه تنظیمات حساب کاربری

3. تغییر وضعیت تایپ
sendChatAction(chat_id: str) -> None
توضیح: شبیه‌سازی وضعیت "در حال تایپ..." در چت

4. گرفتن شناسه خودتان
myId() -> str
توضیح: دریافت شناسه عددی حساب کاربری خودتان

5. بررسی وضعیت آنلاین کاربر
isUserOnline(chat_id: str) -> bool
توضیح: بررسی اینکه آیا کاربر آنلاین است یا خیر

راهنمای استفاده
سناریو ۱: ورود و ارسال پیام ساده
```python
from bot import Bot

# ایجاد ربات با لاگین خودکار
bot = Bot(headless=False, autologin=True)

# رفتن به یک چت مشخص
bot.go_chat("123456789")

# ارسال پیام
result, msg = bot.send_message("123456789", "سلام! چطوری؟")

# نمایش نتیجه
print(result)
```
سناریو ۲: دریافت و ذخیره عکس‌های جدید
```python
# دریافت پیام‌های جدید از اولین چت
messages = bot.on_new_message(1)

# بررسی وجود عکس در پیام‌ها
for msg_id, msg_data in messages.items():
    if 'media' in msg_data:
        # ذخیره لینک عکس
        image_url = msg_data['media']['media-src']
        print(f"عکس جدید از: {msg_data['chat']['title']}")
        print(f"لینک: {image_url}")
```
سناریو ۳: مدیریت گروه
```python
# رفتن به گروه
bot.go_chat("-123456789")  # شناسه گروه با منفی شروع می‌شود

# گرفتن لیست اعضا
members = bot.get_members()
print(f"تعداد اعضا: {len(members)}")

# افزودن عضو جدید
bot.add_member(["@new_member"])

# حذف عضو
member_to_delete = members[0]  # فرض کنید عضو اول
bot.delete_member(member_to_delete)
سناریو ۴: ضبط صدا
python
# گرفتن برنامه‌های در حال پخش صدا
active = bot.list_active_sessions()

# قطع صدای همه برنامه‌ها
bot.mute_sessions(active)

# شروع ضبط (۱۰ ثانیه)
print("در حال ضبط...")
bot.save_audio(10)

# برگرداندن صدا
bot.unmute_sessions(active)
print("ضبط تمام شد! فایل output.mp3 ذخیره شد.")
سناریو ۵: ارسال آلبوم عکس
python
# لیست عکس‌ها
photos = ["C:/photos/1.jpg", "C:/photos/2.jpg", "C:/photos/3.jpg"]

# ارسال به عنوان آلبوم
bot.send_album(
    file_paths=photos,
    caption="عکس‌های تولد",
    send_compressed=False  # کیفیت اصلی
)
```
خطاهای رایج
1. خطای اتصال به ایتا
python
Eitaa Is Down Or System Is Offline!
علت: سرور ایتا در دسترس نیست یا اینترنت قطع است

راهکار: اتصال اینترنت را بررسی کنید و بعداً دوباره امتحان کنید

2. خطای لاگین
python
Login Data Not Found!
علت: فایل login.json وجود ندارد و autologin=True است

راهکار: ابتدا با autologin=False یک بار وارد شوید تا فایل ساخته شود

3. خطای کد نامعتبر
python
کد نامعتبر است
علت: کد تایید اشتباه وارد شده است

راهکار: کد جدید دریافت کنید و دوباره وارد کنید

4. خطای یافت نشدن المنت
```python
Error in find_message
```
علت: المنت پیام در صفحه پیدا نشد (احتمالاً صفحه بارگذاری کامل نشده)

راهکار: از sleep() بیشتر استفاده کنید یا از WebDriverWait استفاده کنید

5. خطای ارسال به کاربر
```python
canSendToUser returned "None"
```
علت: کاربر شما را بلاک کرده یا محدودیت ارسال دارد

راهکار: ابتدا بررسی کنید کاربر شما را بلاک نکرده باشد

نکات امنیتی
🔴 خطرات امنیتی (هشدار!)
ذخیره اطلاعات ورود به صورت متن ساده

فایل login.json شامل توکن دسترسی شماست

توصیه: این فایل را در مکان امن نگهداری کنید و با رمزگذاری ذخیره کنید

اجرای کد از طریق پیام

متد driver_command می‌تواند خطرناک باشد

توصیه: هرگز از این متد برای اجرای کدهای ناشناس استفاده نکنید

کلیپ‌بورد و اطلاعات حساس

اطلاعات در کلیپ‌بورد باقی می‌ماند

توصیه: بعد از هر بار استفاده، کلیپ‌بورد را خالی کنید

✅ نکات ایمنی
همیشه از headless=False برای تست اولیه استفاده کنید تا مشکلات را ببینید

قبل از استفاده در پروژه اصلی، با یک حساب تست کار کنید

از لاگین خودکار در سیستم‌های عمومی استفاده نکنید

هرگز فایل login.json را با دیگران به اشتراک نگذارید

برای عملیات حساس، حتماً از canSendToUser قبل از ارسال استفاده کنید

بهینه‌سازی‌های پیشنهادی
۱. افزایش سرعت
```python
# به جای sleep های طولانی
# از WebDriverWait استفاده کنید
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

wait = WebDriverWait(driver, 10)
element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, "#main-search")))
```
۲. مدیریت خطاهای بهتر
```python
try:
    bot.send_message(chat_id, text)
except Exception as e:
    print(f"خطا در ارسال: {e}")
    # لاگ خطا برای بررسی بعدی
    with open("error.log", "a") as log:
        log.write(f"{datetime.now()}: {e}\n")
```
۳. استفاده از ترد (Thread) برای عملیات همزمان
```python
import threading

def send_messages_parallel(bot, messages):
    threads = []
    for chat_id, text in messages:
        t = threading.Thread(target=bot.send_message, args=(chat_id, text))
        threads.append(t)
        t.start()
    
    for t in threads:
        t.join()
```
نکات نهایی
محدودیت ارسال: ایتا ممکن است ارسال سریع پیام را محدود کند

حفظ نشست: لاگین خودکار تا ۳۰ روز معتبر است

منابع سیستم: اجرای مرورگر منابع زیادی مصرف می‌کند

پشتیبانی مرورگر: فقط کروم و فایرفاکس پشتیبانی می‌شوند

قوانین استفاده: از این ربات برای اسپم یا مزاحمت استفاده نکنید
