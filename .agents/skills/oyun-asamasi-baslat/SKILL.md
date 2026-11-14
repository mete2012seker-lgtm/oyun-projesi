---
name: oyun-asamasi-baslat
description: Antigravity ve Codex ortak Godot oyun projesinde yeni bir geliştirme aşamasına başlarken bellek, çakışma, durum, mesaj ve dosya kilidi kontrollerini çalıştırır.
---

# Oyun Aşaması Başlat

1. Kendi bellek dosyanı oku (`.agent_chat/memory/antigravity_memory.md` veya `.agent_chat/memory/codex_memory.md`).
2. Proje kökünde `*.sync-conflict-*` dosyası varsa dur ve kullanıcıyı uyar.
3. `.agent_chat/status/<peer>.json` dosyasından karşı ajanın çevrimiçi durumunu (`last_seen_utc` <= 5 dk) ve `locked_files` / `lock_lease_utc` kilidini kontrol et.
4. `.agent_chat/from_<peer>.md` ve `.agent_chat/CHANGELOG.md` dosyalarını oku (Antigravity ise `for_user/from_codex.md` ve `asset_requests/from_codex.md` dahil). Okunan `msg_id`'yi kendi `status/<self>.json` dosyana yaz.
5. Düzenleyeceğin dosyaları `locked_files` + `lock_lease_utc` (+20 dk UTC) ile kilitle ve göreve başla.
