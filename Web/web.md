## خلاصه کاربردی — Web & Network Basics

### 1. Internet و Web

- **Internet** = شبکه‌ای از شبکه‌ها.
- **Web** = یکی از سرویس‌هایی که روی Internet کار می‌کند.
- **Client** = درخواست می‌فرستد؛ مثل Browser، curl، Mobile App.
- **Server** = درخواست را دریافت و پاسخ می‌دهد؛ مثل FastAPI.

### 2. IP و Port

- **IP** = آدرس یک سیستم در شبکه.
- **IPv4** مثل `192.168.1.10`
- **IPv6** مثل `2606:4700:...`
- **Private IP** = داخل شبکه محلی/VPN.
- **Public IP** = قابل استفاده برای ارتباط اینترنتی.
- **Port** = مشخص می‌کند با کدام سرویس ارتباط داری.
    - `80` → HTTP
    - `443` → HTTPS
    - `5432` → PostgreSQL
    - `6379` → Redis
- **Ephemeral Port** = پورت موقتی که Client برای اتصال خروجی می‌گیرد.

### 3. Domain و DNS

- **Domain** مثل `example.com` برای انسان قابل‌خواندن است.
- **DNS** دامنه را به IP تبدیل می‌کند:

```text
example.com → DNS → IP
```

- `127.0.0.53` در سیستم تو یک **Local DNS Stub Resolver** بود.
- یک Domain می‌تواند چند IPv4/IPv6 داشته باشد.

### 4. URL

مثلاً:

```text
https://api.example.com:443/users/15?active=true
```

- `https` → Scheme
- `api.example.com` → Host
- `443` → Port
- `/users/15` → Path
- `active=true` → Query Parameter

### 5. HTTP

HTTP زبان ارتباط Client و Server است:

```text
Client → HTTP Request → Server
Client ← HTTP Response ← Server
```

Request شامل:

- Method
- Path
- Headers
- Body

Response شامل:

- Status Code
- Headers
- Body

### 6. Status Codeهای مهم

```text
200 → موفق
201 → ایجاد شد
204 → بدون Body
400 → درخواست اشتباه
401 → احراز هویت لازم است
403 → دسترسی ممنوع
404 → پیدا نشد
422 → Validation Error
429 → درخواست بیش از حد
500 → خطای Server
```

### 7. HTTPS و TLS

```text
HTTPS = HTTP + TLS
```

TLS برای:

- رمزنگاری ارتباط
- احراز هویت Server با Certificate
- ایجاد کلید امن ارتباط

### 8. چیزی که با `curl -v` دیدی

عملاً این مسیر اتفاق افتاد:

```text
example.com
   ↓
DNS
   ↓
IPv6 تلاش شد → شکست
   ↓
IPv4
   ↓
Port 443
   ↓
TLS 1.3
   ↓
ALPN → HTTP/2
   ↓
GET /
   ↓
Cloudflare
   ↓
Cache HIT
   ↓
200 OK
   ↓
HTML
```

### 9. چند Header مهم

- `Content-Type` → نوع Body
- `User-Agent` → نوع Client
- `Accept` → چه نوع محتواهایی را قبول می‌کند
- `Last-Modified` → آخرین تغییر Resource
- `Cache-Control` → قوانین Cache
- `Location` → مقصد Redirect

### 10. سه دستور مهم فعلی

```bash
curl https://example.com
```

دریافت Body

```bash
curl -I https://example.com
```

فقط Header/HEAD

```bash
curl -v https://example.com
```

نمایش جزئیات کامل ارتباط

```bash
nslookup example.com
```

بررسی DNS

---

### مهم‌ترین تصویر ذهنی فعلی

```text
Domain
  ↓
DNS
  ↓
IP
  ↓
Port
  ↓
TCP
  ↓
TLS
  ↓
HTTP
  ↓
Server
  ↓
Response
```
