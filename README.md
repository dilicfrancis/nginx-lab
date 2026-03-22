# nginx-lab

A collection of 16 nginx configuration files, each isolating a specific feature — from basic location matching and variable usage to SSL hardening, HTTP/2 Server Push, rate limiting, and FastCGI caching.

## Configuration Files

```
nginx-lab/
├── location_nginx.conf
├── variable_nginx.conf
├── try_files.conf
├── access_logs.conf
├── rewrites.conf
├── inheritance.conf
├── headersandexpires.conf
├── compressedresponsesgzip.conf
├── buffersandtimeouts.conf
├── HTTPS-SSL.conf
├── HTTP2.conf
├── serverpush.conf
├── fastCGIcache.conf
├── basicauth.conf
├── ratelimiting.conf
└── hardeningnginx.conf
```

## File-by-File Breakdown

### Core Routing & Logic

| File | What it demonstrates |
|------|---------------------|
| **location_nginx.conf** | All location match types — exact (`=`), preferential prefix (`^~`), regex case-sensitive (`~`), regex case-insensitive (`~*`), prefix (default) — with priority ordering |
| **variable_nginx.conf** | Built-in variables (`$date_local`, `$arg_apikey`, `$host`, `$uri`), custom variables (`set $weekend`), conditional logic (`if`), regex date matching, query-param API-key validation |
| **try_files.conf** | File fallback chain — `$uri` → static file → named location (`@404`); demonstrates named locations as internal-only routing targets |
| **rewrites.conf** | `rewrite` with capture groups (`^/user/(\w+)` → `/hello/$1`), `last` flag for re-evaluation, `return 307` external redirects, rewrite-vs-redirect distinction |
| **inheritance.conf** | Directive inheritance across http → server → location contexts; array directives (`access_log`), standard directives (`root`), action directives (`return`) |

### Logging & Headers

| File | What it demonstrates |
|------|---------------------|
| **access_logs.conf** | Per-location log files, disabling logging for specific paths, custom log destinations |
| **headersandexpires.conf** | `Cache-Control: public`, `Pragma`, `Vary: Accept-Encoding`, 1-month expiry for static assets (CSS, JS, JPG, PNG), disabled access logs for cached content |

### Performance

| File | What it demonstrates |
|------|---------------------|
| **compressedresponsesgzip.conf** | `gzip on`, compression level 3, type filtering (text/css, text/javascript), static-file caching headers |
| **buffersandtimeouts.conf** | POST body buffer (10K), max body size (8MB), header buffer (1K), client body/header timeouts (12s), keepalive (15s), send timeout (10s), `sendfile on`, `tcp_nopush on` |
| **fastCGIcache.conf** | Microcache at `/tmp/nginx_cache`, zone ZONE_1 (100MB), 60-min TTL for 200s, cache-key composition (`$scheme$request_method$host$request_uri`), bypass via `?skipcache=1`, `X-Cache` response header (HIT/MISS/BYPASS) |

### Security

| File | What it demonstrates |
|------|---------------------|
| **HTTPS-SSL.conf** | HTTP → HTTPS redirect, TLSv1/1.1/1.2, cipher-suite optimisation (ECDH+AESGCM preferred), Diffie-Hellman params, HSTS header, SSL session caching and tickets, self-signed certs |
| **basicauth.conf** | HTTP Basic Authentication via `.htpasswd`, combined with HTTPS, rate-limit zone definition |
| **hardeningnginx.conf** | `server_tokens off`, `X-Frame-Options: SAMEORIGIN`, `X-XSS-Protection: 1; mode=block`, HSTS, full SSL/TLS with optimised ciphers |
| **ratelimiting.conf** | `limit_req_zone` keyed on `$request_uri` (10m, 1r/s), `burst=5 nodelay`, 503 on excess, combined with HTTPS |

### HTTP/2

| File | What it demonstrates |
|------|---------------------|
| **HTTP2.conf** | HTTP/2 protocol on port 443 with SSL, PHP-FPM via FastCGI |
| **serverpush.conf** | `http2_push` — preloads `/style.css` and `/thumb.png` alongside `/index.html` to eliminate round-trips |

## Nginx Concepts Covered

**Routing:** location match types and priority, try_files fallback chains, rewrites with capture groups, named locations, redirects

**Variables & Conditionals:** built-in variables, custom variables with `set`, `if` blocks, regex matching, query-parameter access

**Directive Inheritance:** http → server → location context hierarchy, array vs standard vs action directive behaviour

**SSL/TLS:** HTTPS redirects, certificate configuration, protocol and cipher selection, DH params, HSTS, session caching

**Performance Tuning:** gzip compression, buffer sizing, timeout configuration, sendfile/tcp_nopush, FastCGI microcaching with bypass logic

**Security:** version hiding, clickjacking/XSS headers, basic auth, rate limiting with burst handling

**HTTP/2:** protocol enablement, server push for asset preloading

**Logging:** per-location log routing, log disabling for static assets

## Technology Stack

- **Web server:** nginx
- **Backend:** PHP-FPM (php7.1-fpm) via FastCGI
- **User context:** www-data
- **Web root:** `/sites/demo`
- **SSL certs:** `/etc/nginx/ssl/`
- **Logs:** `/var/log/nginx/`
- **Cache:** `/tmp/nginx_cache/`
