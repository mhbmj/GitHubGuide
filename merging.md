# ادغام تغییرات (Merge) و حل تعارض (Merge Conflict) در Git

## ۱. Merge چیست؟

پس از اینکه روی یک Branch کار کردید، باید تغییرات آن را به شاخه اصلی برگردانید. به این کار **Merge** (ادغام) می‌گویند.

Git بسته به وضعیت تاریخچه شاخه‌ها، به دو روش اصلی ادغام می‌کند: **Fast-forward** و **Three-way**.

---

## ۲. دستور `git merge`

شکل کلی:

```bash
git merge <branch-name>
```

این دستور، شاخه‌ی `<branch-name>` را در **شاخه‌ای که الان روی آن هستید** ادغام می‌کند. پس همیشه اول به شاخه مقصد بروید:

```bash
git switch main
git merge feature/login-form
```

گزینه‌های پرکاربرد:

| دستور | توضیح |
|---|---|
| `git merge <branch>` | ادغام معمولی |
| `git merge --no-ff <branch>` | حتی اگر ممکن است، Merge Commit بساز |
| `git merge --abort` | انصراف از ادغامِ نیمه‌کاره (هنگام تعارض) |

---

## ۳. Fast-forward Merge

وقتی شاخه اصلی از زمان جدا شدن شاخه فرعی **هیچ commit جدیدی نگرفته باشد**، Git فقط اشاره‌گر `main` را جلو می‌برد. هیچ commit جدیدی ساخته نمی‌شود و تاریخچه خطی می‌ماند.

**قبل از Merge:**

```
main:      A --- B
                  \
feature:           C --- D
```

**بعد از Merge:**

```
main:      A --- B --- C --- D
                              ^
                        main و feature
```

### مثال

```bash
git switch main
git merge feature/login-form
```

خروجی نمونه:

```
Updating a1b2c3d..e4f5g6h
Fast-forward
 login.html | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
```

عبارت `Fast-forward` در خروجی نشان می‌دهد این نوع ادغام انجام شده است.

---

## ۴. Three-way Merge

وقتی **هر دو شاخه بعد از نقطه جدایی commit جدید داشته باشند**، نمی‌شود فقط اشاره‌گر را جلو برد. Git از سه نقطه برای ادغام استفاده می‌کند:

1. آخرین commit شاخه اول
2. آخرین commit شاخه دوم
3. جد مشترک (common ancestor)

سپس یک **Merge Commit** جدید می‌سازد که دو والد دارد.

**قبل از Merge:**

```
main:      A --- B --- E
                  \
feature:           C --- D
```

**بعد از Merge:**

```
main:      A --- B --- E ------- M
                  \             /
feature:           C --- D ----
```

`M` همان Merge Commit است.

### مثال

```bash
git switch main
git merge feature/profile
```

اگر تعارضی نباشد، ویرایشگر برای نوشتن پیام Merge Commit باز می‌شود (پیام پیش‌فرض را می‌توانید ذخیره کنید) و خروجی چیزی شبیه زیر است:

```
Merge made by the 'ort' strategy.
 profile.html | 15 +++++++++++++++
 1 file changed, 15 insertions(+)
```

برای دیدن تاریخچه به شکل گرافیکی:

```bash
git log --oneline --graph --all
```

---

## ۴. Merge Conflict (تعارض ادغام) چیست؟

**Merge Conflict** زمانی رخ می‌دهد که دو شاخه **یک بخش یکسان از یک فایل** را به شکل متفاوت تغییر داده باشند و Git نداند کدام نسخه درست است. Git در این حالت متوقف می‌شود و تصمیم را به شما می‌سپارد.

### دلایل رایج

- دو نفر یک خط از یک فایل را متفاوت ویرایش کنند.
- یک نفر فایل را حذف کند و دیگری آن را ویرایش کند.
- جابه‌جایی یا تغییر نام فایل در یک شاخه و ویرایش آن در شاخه دیگر.

### ظاهر فایل دارای تعارض

Git علامت‌هایی داخل فایل قرار می‌دهد:

```
<<<<<<< HEAD
Project Status: Completed
=======
Project Status: Under Review
>>>>>>> feature/status-review
```

| بخش | معنی |
|---|---|
| `<<<<<<< HEAD` | شروع تغییرات شاخه فعلی (مثلاً `main`) |
| `=======` | جداکننده دو نسخه |
| `>>>>>>> feature/status-review` | پایان تغییرات شاخه‌ای که در حال ادغام است |

---

## ۵. مراحل حل تعارض

**گام ۱:** اجرای Merge و مشاهده پیام تعارض:

```bash
git switch main
git merge feature/status-review
```

```
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

**گام ۲:** پیدا کردن فایل‌های دارای تعارض:

```bash
git status
```

فایل‌ها زیر عنوان `Unmerged paths` نمایش داده می‌شوند.

**گام ۳:** باز کردن فایل در ویرایشگر (مثل VS Code) و تصمیم‌گیری. سه انتخاب دارید:

- نگه داشتن نسخه شاخه فعلی
- نگه داشتن نسخه شاخه دیگر
- ترکیب هر دو یا نوشتن نسخه جدید

**گام ۴:** حذف علامت‌های `<<<<<<<`، `=======` و `>>>>>>>` و نگه داشتن متن نهایی. برای مثال:

```
Project Status: Completed - Pending Review
```

**گام ۵:** افزودن فایل حل‌شده به Staging:

```bash
git add README.md
```

**گام ۶:** تکمیل Merge با commit:

```bash
git commit -m "merge: resolve conflict in README project status"
```

**گام ۷:** بررسی تاریخچه و ارسال به GitHub:

```bash
git log --oneline --graph
git push origin main
```

> اگر وسط کار پشیمان شدید و می‌خواهید به حالت قبل از Merge برگردید: `git merge --abort`

---

## ۶. مثال عملی کامل: ایجاد و حل یک تعارض

فرض کنید خط زیر در `README.md` وجود دارد:

```
Project Status: In Progress
```

**۱. دو شاخه از `main` می‌سازیم:**

```bash
git switch main
git switch -c feature/status-completed
# ... ویرایش خط به "Project Status: Completed" ...
git commit -am "docs: set project status to Completed"

git switch main
git switch -c feature/status-review
# ... ویرایش همان خط به "Project Status: Under Review" ...
git commit -am "docs: set project status to Under Review"
```

**۲. شاخه اول را ادغام می‌کنیم (بدون مشکل، Fast-forward):**

```bash
git switch main
git merge feature/status-completed
```

**۳. شاخه دوم را ادغام می‌کنیم (تعارض رخ می‌دهد):**

```bash
git merge feature/status-review
```

```
CONFLICT (content): Merge conflict in README.md
```

**۴. فایل را باز می‌کنیم و تعارض را دستی حل می‌کنیم، سپس:**

```bash
git add README.md
git commit -m "merge: resolve README status conflict"
```

هر دو تغییر اولیه همچنان در تاریخچه (`git log`) قابل مشاهده‌اند.

---

## ۷. نکات مهم برای پیشگیری از تعارض

- قبل از شروع کار، `git pull` بزنید تا آخرین تغییرات را داشته باشید.
- شاخه‌ها را کوتاه‌عمر نگه دارید و زود ادغام کنید.
- با هم‌تیمی‌ها هماهنگ کنید که چه کسی روی کدام فایل کار می‌کند.
- Commit‌های کوچک و مکرر بزنید.

---

## ۸. خلاصه دستورها

```bash
git merge <branch>            # ادغام یک شاخه در شاخه فعلی
git merge --no-ff <branch>    # ادغام همراه با Merge Commit
git merge --abort             # انصراف از ادغام
git status                    # دیدن فایل‌های دارای تعارض
git add <file>                # علامت‌گذاری فایل به‌عنوان حل‌شده
git commit                    # تکمیل Merge
git log --oneline --graph     # مشاهده تاریخچه گرافیکی
```
