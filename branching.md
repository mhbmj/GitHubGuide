# شاخه‌ها (Branch) در Git و نحوه مدیریت آن‌ها

## ۱. مفهوم Branch چیست؟

**Branch** (شاخه) یک مسیر مستقل از توسعه است. وقتی یک Branch جدید می‌سازید، یک «نسخه موازی» از پروژه دارید که می‌توانید بدون ترس از خراب شدن کد اصلی، روی آن کار کنید.

تصور کنید یک کتاب را با چند نفر می‌نویسید. هر نفر یک کپی از کتاب را برمی‌دارد، فصل خودش را می‌نویسد و در پایان، فصل‌ها با هم ترکیب می‌شوند. Branch در Git همین کار را انجام می‌دهد.

### چرا از Branch استفاده می‌کنیم؟

- **امنیت کد اصلی:** شاخه اصلی (`main`) همیشه سالم و کارا می‌ماند.
- **کار موازی:** چند نفر هم‌زمان روی چند قابلیت مختلف کار می‌کنند.
- **آزمایش آزادانه:** می‌توانید ایده‌ای را امتحان کنید و اگر خوب نبود، شاخه را حذف کنید.
- **بررسی کد:** تغییرات هر شاخه قبل از ادغام، با Pull Request بررسی می‌شود.

### نمایش ساده Branch

```
main:      A --- B --- C
                  \
feature:           D --- E
```

در این تصویر، از commit شماره `B` یک شاخه به نام `feature` جدا شده و دو commit (`D` و `E`) مستقل از `main` ثبت کرده است.

> در Git، شاخه اصلی معمولاً `main` (یا در پروژه‌های قدیمی‌تر `master`) نام دارد. شاخه فعلی شما با اشاره‌گری به نام `HEAD` مشخص می‌شود.

---

## ۲. دستور `git branch`

این دستور برای **مشاهده، ساخت، تغییر نام و حذف** شاخه‌ها استفاده می‌شود (خودش به شاخه دیگر نمی‌رود).

| هدف | دستور |
|---|---|
| نمایش شاخه‌های محلی | `git branch` |
| نمایش همه شاخه‌ها (محلی و ریموت) | `git branch -a` |
| ساخت شاخه جدید | `git branch <branch-name>` |
| تغییر نام شاخه فعلی | `git branch -m <new-name>` |
| حذف شاخه (ادغام‌شده) | `git branch -d <branch-name>` |
| حذف اجباری شاخه | `git branch -D <branch-name>` |

### مثال

```bash
git branch
```

خروجی نمونه:

```
* main
  feature-login
```

علامت `*` نشان می‌دهد که الان روی کدام شاخه هستید.

```bash
git branch feature-profile      # ساخت شاخه جدید
git branch -d feature-profile   # حذف شاخه
```

> دقت کنید: `git branch <name>` فقط شاخه را **می‌سازد**، اما شما را به آن منتقل **نمی‌کند**.

---

## ۳. دستورهای `git checkout` و `git switch`

برای **رفتن به یک شاخه** از یکی از این دو دستور استفاده می‌شود.

### `git checkout` (روش قدیمی)

```bash
git checkout feature-login        # رفتن به شاخه موجود
git checkout -b feature-profile   # ساخت شاخه جدید و رفتن به آن
```

`git checkout` چند کار مختلف انجام می‌دهد (تغییر شاخه، بازگرداندن فایل‌ها و ...)، و برای همین برای مبتدی‌ها گیج‌کننده است.

### `git switch` (روش جدید و پیشنهادی)

از Git نسخه 2.23 به بعد اضافه شده و فقط برای **جابه‌جایی بین شاخه‌ها** است:

```bash
git switch feature-login          # رفتن به شاخه موجود
git switch -c feature-profile     # ساخت شاخه جدید و رفتن به آن
git switch -                      # بازگشت به شاخه قبلی
```

### مقایسه

| کار | `git checkout` | `git switch` |
|---|---|---|
| رفتن به شاخه | `git checkout dev` | `git switch dev` |
| ساخت و رفتن | `git checkout -b dev` | `git switch -c dev` |
| بازگرداندن فایل | `git checkout -- file.txt` | (کار این دستور نیست؛ از `git restore` استفاده می‌شود) |

> **توصیه:** برای تغییر شاخه از `git switch` استفاده کنید؛ ساده‌تر و کم‌خطرتر است.

> **نکته:** اگر تغییرات commit‌نشده‌ای دارید که با شاخه مقصد تداخل دارد، Git اجازه تغییر شاخه نمی‌دهد. ابتدا commit کنید یا از `git stash` استفاده کنید.

---

## ۴. قواعد نام‌گذاری Branch

نام‌گذاری خوب باعث می‌شود اعضای تیم بفهمند هر شاخه برای چه کاری است.

### قواعد پیشنهادی

1. فقط از حروف کوچک انگلیسی، عدد و خط تیره (`-`) استفاده کنید.
2. از فاصله و کاراکترهای خاص پرهیز کنید.
3. نام کوتاه، اما توصیفی باشد.
4. از پیشوند برای نوع کار استفاده کنید.
5. در صورت استفاده از Issue، شماره آن را در نام بیاورید.

### پیشوندهای رایج

| پیشوند | کاربرد | مثال |
|---|---|---|
| `feature/` | افزودن قابلیت جدید | `feature/login-form` |
| `bugfix/` | رفع باگ | `bugfix/fix-navbar-overlap` |
| `hotfix/` | رفع فوری مشکل | `hotfix/payment-crash` |
| `docs/` | تغییرات مستندات | `docs/branching-guide` |
| `refactor/` | بازنویسی بدون تغییر رفتار | `refactor/cleanup-utils` |

### نمونه‌های خوب و بد

| بد ❌ | خوب ✅ |
|---|---|
| `my branch` | `feature/user-profile` |
| `test123` | `bugfix/login-error` |
| `Ali-New-Changes` | `docs/branching-guide` |
| `fix` | `bugfix/issue-12-empty-email` |

---

## ۵. مثال عملی کامل

فرض کنید می‌خواهید فایل `branching.md` را در یک پروژه تیمی بنویسید.

**گام ۱: بررسی وضعیت و شاخه فعلی**

```bash
git status
git branch
```

**گام ۲: دریافت آخرین تغییرات شاخه اصلی**

```bash
git switch main
git pull origin main
```

**گام ۳: ساخت شاخه جدید و رفتن به آن**

```bash
git switch -c docs/branching-guide
```

**گام ۴: انجام تغییرات و ثبت آن‌ها**

```bash
git add branching.md
git commit -m "docs: add branch concept section"
# ... ادامه کار ...
git commit -am "docs: add naming conventions section"
```

**گام ۵: ارسال شاخه به GitHub**

```bash
git push -u origin docs/branching-guide
```

**گام ۶: ساخت Pull Request** در GitHub تا تغییرات به `main` ادغام شوند (توضیح در `merging.md` و `collaboration.md`).

**گام ۷: پاک‌سازی پس از ادغام**

```bash
git switch main
git pull origin main
git branch -d docs/branching-guide              # حذف شاخه محلی
git push origin --delete docs/branching-guide   # حذف شاخه ریموت
```

---

## ۶. خلاصه دستورها

```bash
git branch                    # لیست شاخه‌ها
git branch <name>             # ساخت شاخه
git switch <name>             # رفتن به شاخه
git switch -c <name>          # ساخت و رفتن
git branch -d <name>          # حذف شاخه
git push -u origin <name>     # ارسال شاخه به GitHub
```
