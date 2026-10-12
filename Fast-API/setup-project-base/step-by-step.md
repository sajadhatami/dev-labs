**۱. مدیریت وابستگی‌ها و محیط ایزوله (uv)**

* اقدام: ساخت ساختار اولیه با ابزار مدرن uv و ایجاد `pyproject.toml`.

```bash
uv init --no-package backend
cd backend

```

* نصب پکیج‌های اصلی:

```bash
uv add fastapi sqlalchemy alembic "psycopg[binary]" pydantic-settings uvicorn

```

* نصب ابزارهای توسعه:

```bash
uv add ruff --dev

```

* نکته کلیدی: مشخص کردن نسخه دقیق پایتون در فایل `.python-version`:

```text
3.13

```

**۲. متغیرهای محیطی و پیکربندی (Config)**

* اقدام: ساخت فایل `.env`:

```env
APP_NAME=Raviku
DEBUG=true
DATABASE_URL=postgresql+psycopg://raviku:change_this_local_password@db:5432/raviku

```

* اقدام: فایل `app/core/config.py` برای خواندن امن متغیرها:

```python
from functools import lru_cache
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    APP_NAME: str = "Raviku"
    DEBUG: bool = False
    DATABASE_URL: str

    model_config = SettingsConfigDict(case_sensitive=True, extra="ignore")

@lru_cache
def get_settings() -> Settings:
    return Settings()

```

**۳. راه‌اندازی اتصال دیتابیس (SQLAlchemy)**

* اقدام: فایل `app/db/base.py` برای کلاس والد مدل‌ها:

```python
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
    pass

```

* اقدام: فایل `app/db/session.py` برای پیکربندی دیتابیس:

```python
from app.core.config import get_settings
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

settings = get_settings()

engine = create_engine(
    settings.DATABASE_URL,
    pool_pre_ping=True,
)

SessionLocal = sessionmaker(
    bind=engine,
    autoflush=False,
    expire_on_commit=False,
)

```

**۴. کانتینرسازی برای توسعه و پروداکشن (Docker)**

* اقدام: فایل `Dockerfile` فوق‌سبک:

```dockerfile
FROM ghcr.io/astral-sh/uv:python3.13-bookworm-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_LINK_MODE=copy \
    UV_PROJECT_ENVIRONMENT=/opt/venv \
    PATH="/opt/venv/bin:$PATH"

WORKDIR /app

COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev --no-install-project

COPY app ./app

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]

```

* اقدام: فایل `compose.yaml` با تنظیمات ماونت و Healthcheck:

```yaml
services:
  db:
    image: postgres:17
    restart: unless-stopped
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: [ "CMD-SHELL", "pg_isready -U \"$${POSTGRES_USER}\" -d \"$${POSTGRES_DB}\"" ]
      interval: 5s
      timeout: 5s
      retries: 10

  api:
    build: ./backend
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
    depends_on:
      db:
        condition: service_healthy

volumes:
  postgres_data:

```

**۵. پیاده‌سازی معماری ماژولار مونولیتیک (Modular Monolithic)**

* اقدام: ساختار دامنه‌محور با دستورات زیر:

```bash
mkdir -p backend/app/modules/{users,works,contents,scraping,ai_processing,library} backend/app/common
touch backend/app/modules/__init__.py backend/app/common/__init__.py

for module in users works contents scraping ai_processing library; do
    touch backend/app/modules/$module/{__init__.py,router.py,models.py,schemas.py,services.py,dependencies.py}
done

```

**۶. تنظیمات Entrypoint سرور و CORS**

* اقدام: فایل `app/main.py`:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.core.config import get_settings

settings = get_settings()

app = FastAPI(
    title=settings.APP_NAME,
    debug=settings.DEBUG,
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/health", tags=["Health"])
def health_check():
    return {"status": "ok"}

```

**۷. راه‌اندازی و کاستومایز کردن المبیک (Alembic)**

* اقدام: ساخت زیرساخت مایگریشن:

```bash
uv run alembic init alembic

```

* اقدام: ویرایش‌های ضروری در فایل `alembic/env.py`:

```python
# ۱. ایمپورت تنظیمات و کلاس بیس
from app.core.config import get_settings
from app.db.base import Base

# ۲. ایمپورت مدل‌ها (بدون حذف شدن توسط لینتر)
import app.modules.users.models  # noqa: F401
import app.modules.works.models  # noqa: F401
import app.modules.contents.models  # noqa: F401
import app.modules.scraping.models  # noqa: F401
import app.modules.ai_processing.models  # noqa: F401
import app.modules.library.models  # noqa: F401

# ۳. تنظیمات آدرس دیتابیس و متادیتا
settings = get_settings()
config = context.config
config.set_main_option("sqlalchemy.url", settings.DATABASE_URL)

target_metadata = Base.metadata

```

* اقدام: اجرای مایگریشن از داخل داکر:

```bash
docker compose exec api uv run alembic revision --autogenerate -m "init_modular_architecture"
docker compose exec api uv run alembic upgrade head

```

**۸. تضمین کیفیت کد با ابزار Linter**

* اقدام: افزودن تنظیمات Ruff به `pyproject.toml`:

```toml
[tool.ruff]
target-version = "py313"
line-length = 88
exclude = [
    ".git",
    ".ruff_cache",
    ".venv",
    "alembic",
]

[tool.ruff.lint]
select = ["E", "F", "I", "UP"] 
ignore = []

```

* دستورات اجرا برای مرتب‌سازی کدها:

```bash
uv run ruff check . --fix
uv run ruff format .

```
