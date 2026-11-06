# Mood2Glow

**GitHub description:** Turn Persian mood and scene descriptions into three RGB color anchors using multilingual-e5-small embeddings and an MLP, with a smooth LED-strip preview.

Mood2Glow یک نمونه‌پروژه برای تبدیل توصیف متنیِ حس‌وحال، صحنه یا رنگ به سه رنگ RGB است. نوت‌بوک، متن را با مدل چندزبانه‌ی E5 به embedding تبدیل می‌کند و یک شبکه‌ی MLP سه رنگ پیشنهادی و احتمال مرتبط‌بودن متن با رنگ را پیش‌بینی می‌کند. اگر متن مرتبط تشخیص داده نشود، رنگ خنثی جایگزین می‌شود.

سه رنگ پیش‌بینی‌شده به‌عنوان نقاط ابتدا، میانه و انتهای نوار رنگ استفاده می‌شوند. نوت‌بوک با درون‌یابی خطی، پیش‌نمایشی نرم از این گرادیان می‌سازد. در نسخه‌ی فعلی، ارسال داده به سخت‌افزار یا کنترل مستقیم ریسه‌ی NeoPixel پیاده‌سازی نشده است.

## روند کلی

1. خواندن نمونه‌های متنی و برچسب‌های RGB از فایل‌های JSONL پوشه‌ی `Datasets/`.
2. ساخت embeddingهای ۳۸۴بعدی با `intfloat/multilingual-e5-small` و پیشوند `query:`.
3. آموزش MLP برای پیش‌بینی ۹ مقدار RGB (سه رنگ) و امتیاز مرتبط‌بودن.
4. نمایش گرادیان و ارزیابی نتیجه برای متن ورودی.

## ساختار پروژه

- `main.ipynb` — آماده‌سازی داده، ساخت embedding، آموزش و ارزیابی مدل، و تابع پیش‌بینی.
- `Datasets/` — داده‌های آموزشی JSONL.
- `mood_model.pt` — وزن‌های ذخیره‌شده‌ی MLP (با اجرای نوت‌بوک دوباره تولید می‌شوند).
- `embeddings.npy` — embeddingهای تولیدشده (با اجرای نوت‌بوک دوباره تولید می‌شوند).
- `multilingual-e5-small/` — کپی محلی مدل، در صورت وجود؛ این پوشه در Git نادیده گرفته می‌شود.

## راه‌اندازی

به Python 3.10 یا جدیدتر نیاز است. از ریشه‌ی پروژه، محیط مجازی بسازید و وابستگی‌ها را نصب کنید:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install jupyter ipykernel torch sentence-transformers numpy pandas scikit-learn matplotlib
```

سپس نوت‌بوک را باز کنید:

```powershell
jupyter notebook main.ipynb
```

سلول‌ها را به‌ترتیب اجرا کنید. در اولین اجرا، `sentence-transformers` مدل `intfloat/multilingual-e5-small` را از Hugging Face دریافت و در cache محلی ذخیره می‌کند؛ بنابراین برای دریافت اولیه‌ی مدل، اتصال اینترنت لازم است. نیازی به قرار دادن فایل‌های مدل در مخزن GitHub نیست.

## استفاده از پیش‌بینی

پس از اجرای سلول‌های نوت‌بوک، متن را به تابع `predict` بدهید:

```python
predict("غروب آرام کنار دریا")
```

خروجی شامل وضعیت مرتبط‌بودن، احتمال پیش‌بینی و سه رنگ RGB در بازه‌ی ۰ تا ۲۵۵ است. برای اتصال به ریسه، می‌توان این مقادیر را در کد سخت‌افزاری به‌عنوان نقاط رنگ به کار برد و گرادیان را متناسب با تعداد LEDها محاسبه کرد.

## نکته درباره‌ی داده و مدل

- مدل embedding از مخزن عمومی [`intfloat/multilingual-e5-small`](https://huggingface.co/intfloat/multilingual-e5-small) دریافت می‌شود.
- داده‌ها و وزن‌های MLP این پروژه جدا از مدل embedding هستند؛ تغییر داده یا معماری ممکن است نیازمند آموزش مجدد باشد.
- پیش‌نمایش نوت‌بوک برای نمایش بصری است و تضمینی درباره‌ی کیفیت رنگ برای هر متن یا سخت‌افزار خاص نمی‌دهد.
