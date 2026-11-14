---
name: oyun-asamasi-bitir
description: Antigravity ve Codex ortak Godot oyun projesinde bir geliştirme aşamasını veya sohbeti kapatırken günlük, mesaj devri, kilit bırakma, arşiv ve kalıcı bellek güncellemesini yapar.
---

# Oyun Aşaması Bitir

1. Değişen dosyaları ve özeti UTC zaman damgasıyla `.agent_chat/CHANGELOG.md` dosyasına ekle.
2. Sıradaki adımı artan `msg_id` ile kendi kanalına (`.agent_chat/from_<self>.md`) yaz.
3. `.agent_chat/status/<self>.json` içinde `locked_files: []`, `lock_lease_utc: ""`, `status: "idle"` ve güncel UTC `last_seen_utc` güncelle.
4. Onaylanmış (`last_read_msg_id`) eski mesajlar 50 satırı geçtiyse `.agent_chat/archive/` altına taşı.
5. Alınan kararları ve sıradaki adımı `.agent_chat/memory/<self>_memory.md` dosyasına kaydet ve kullanıcıya yeni sohbete geçilebileceğini bildir.
