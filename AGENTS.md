# Ortak Ajan Protokolü (Antigravity & Codex)

Bu dosya, farklı ağlardaki iki bilgisayarda çalışan **Antigravity** ve **Codex** ajanlarının aynı Godot projesinde çakışmasız çalışması için uyulması gereken kuralları tanımlar.

## 1. Göreve Başlamadan Önce (Zorunlu)
Her iki ajan da herhangi bir dosyayı düzenlemeden önce sırasıyla şu kontrolleri yapar:
0. **Kalıcı Bellek:** Yeni bir sohbet oturumu açıldığında ilk olarak kendi bellek dosyanı oku (`.agent_chat/memory/antigravity_memory.md` veya `.agent_chat/memory/codex_memory.md`).
1. Karşı ajanın durum dosyasını oku:
   - Antigravity → `.agent_chat/status/codex.json`
   - Codex → `.agent_chat/status/antigravity.json`
   - `last_seen_utc` (ISO-8601 UTC) son **5 dakika** içindeyse karşı taraf **çevrimiçi**, değilse **çevrimdışı (asenkron)** kabul edilir.
2. Karşı ajanın mesaj kanalını oku:
   - Antigravity → `.agent_chat/from_codex.md`, `.agent_chat/for_user/from_codex.md` ve `.agent_chat/asset_requests/from_codex.md`
   - Codex → `.agent_chat/from_antigravity.md`
   - **Kullanıcıya İletim Kuralı:** Antigravity, oturum/görev başında `for_user/from_codex.md` içinde yeni bir not veya `asset_requests/from_codex.md` içinde `status: pending` bir görsel/prompt isteği (ve varsa `refs/` altındaki referans görüntüyü) görürse, bunu doğrudan kendi kullanıcısına iletir.
   - Okunan son mesajın `msg_id` değerini kendi `status/<self>.json` dosyasındaki `last_read_msg_id` alanına yaz.
3. `.agent_chat/CHANGELOG.md` dosyasını okuyarak son yapılan değişiklikleri öğren.
4. Düzenlenecek dosyaların karşı ajanın `locked_files` listesinde olmadığını doğrula ve kendi `locked_files` listene ekle.
5. **Beckett MCP Otomatik Başlatma:** `project.godot` mevcutsa ve `http://127.0.0.1:8770/mcp` (Beckett MCP) yanıt vermiyorsa, ajan Godot Editörünü otomatik olarak başlatır (`--editor --path .`) ve `res://addons/beckett/plugin.cfg` eklentisinin aktif olduğunu doğrular.

## 2. Sıfır Çakışma Kuralları
- **Tek Yazar Kuralı:**
  - `.agent_chat/from_antigravity.md` dosyasına **yalnızca Antigravity** yazar.
  - `.agent_chat/from_codex.md` dosyasına **yalnızca Codex** yazar.
  - `status/antigravity.json` dosyasına yalnızca Antigravity, `status/codex.json` dosyasına yalnızca Codex yazar.
- **Godot Sahneleri:** `main.tscn` ve `project.godot` gibi ortak dosyalar aynı anda düzenlenmez. Yeni özellikler mümkün olduğunca ayrı sahnelerde (`scenes/player.tscn`, `scenes/ui.tscn` vb.) geliştirilir.
- **Zaman Damgası:** Tüm tarih/saat alanları **UTC (`Z`)** formatında yazılır (`YYYY-MM-DDTHH:MM:SSZ`).

## 3. Görev Bitiminde ve Temizleme (Ack-Before-Reset)
1. Yapılan değişiklikleri `.agent_chat/CHANGELOG.md` dosyasına ekle.
2. Karşı tarafa devredilecek bilgi veya sonraki adımı artan `msg_id` ile kendi kanalına (`from_<self>.md`) yaz.
3. Kendi `status/<self>.json` dosyandaki `locked_files` listesini boşalt ve `last_seen_utc` değerini güncelle.
4. **Otomatik Arşivleme:** Kendi mesaj dosyan 50 satırı geçtiğinde veya bir kilometre taşı kapandığında, yalnızca karşı tarafın `last_read_msg_id` ile onayladığı mesajları `.agent_chat/archive/` klasörüne taşıyıp kendi dosyanı sıfırla.
5. **Bellek Güncelleme:** Sohbet uzadığında veya yeni sohbete geçmeden önce alınan kararları, mimari notları ve sıradaki adımı kendi `memory/<agent>_memory.md` dosyana özetle.
