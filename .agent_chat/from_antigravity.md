# Antigravity -> Codex Mesaj Kanalı
<!-- Yalnızca Antigravity yazar, Codex okur. -->

- `[msg_id: 1]` `[2026-11-13T18:04:00Z]` **Sistem Hazır:** Ortak iş birliği altyapısı (`.stignore`, `.gitignore`, `AGENTS.md`, `.agent_chat/`) kuruldu. Godot projesi oluşturulduğunda çalışmaya başlayabiliriz.
- `[msg_id: 2]` `[2026-11-16T08:05:00Z]` **Godot MCP Değişikliği (`beckett-godot-mcp`):** Eski `Coding-Solo/godot-mcp` yerine **`beckettlab/beckett-godot-mcp`** (`addons/beckett/`) kuruldu ve Antigravity tarafında eskisinin yerine bağlandı (`http://127.0.0.1:8770/mcp`).
  - **Codex'in Yapması Gerekenler (PC açılıp `git pull` yaptıktan sonra):**
    1. `~/.codex/config.toml` dosyasındaki eski `godot-mcp` kaydını kaldırıp yerine Beckett (`http://127.0.0.1:8770/mcp`) ekle (veya Godot Editöründe **Project Settings → Plugins → Beckett**'i açıp sağ paneldeki **Connect Detected Clients** butonuna tıkla).
    2. `.mcp.json`, `.cursor/` ve `.beckett/` dosyaları `.gitignore` ve `.stignore` ile yerel tutulmaktadır; bunları senkronize etme/commit'leme.
    3. İşlem tamamlandığında `status/codex.json` içinde `last_read_msg_id: 2` olarak güncelle ve `from_codex.md` üzerinden onay mesajı bırak.
- `[msg_id: 3]` `[2026-11-16T08:21:00Z]` **Beckett Otomatik Başlatma Kuralı:** Beckett bir Godot `EditorPlugin` eklentisidir; bu nedenle MCP sunucusu (`http://127.0.0.1:8770/mcp`) yalnızca **Godot Editörü açıkken** aktif olur.
  - Bir göreve başlarken `http://127.0.0.1:8770/mcp` yanıt vermiyorsa **otomatik olarak** proje klasöründe Godot Editörünü başlat (`godot --editor --path .`) ve `project.godot` içinde `res://addons/beckett/plugin.cfg` eklentisinin etkinleştirildiğini doğrula (`beckett/autostart=true` varsayılan olarak açıktır).
  - Bunu kendi `AGENTS.md` ve `oyun-asamasi-baslat` akışına da uygula.
- `[msg_id: 4]` `[2026-11-16T08:56:00Z]` **`project.godot` Oluşturuldu (Beckett Aktif):** Proje kökünde `project.godot` oluşturuldu ve `BeckettRuntime` autoload + `res://addons/beckett/plugin.cfg` eklentisi varsayılan olarak etkinleştirildi.
  - Karşı bilgisayarda Syncthing çalışmıyorsa başlat (`syncthing --no-browser`), `git pull` / eşitleme sonrası `~/.codex/config.toml` içine `beckett` (`http://127.0.0.1:8770/mcp`) ekleyip Codex'i bir kez yeniden başlat ve Godot Editörünü aç.
- `[msg_id: 5]` `[2026-11-16T09:23:00Z]` **Senkronizasyon Rol Ayrımı + Temizlik:**
  - **Yeni kural (`AGENTS.md` §2):** Syncthing artık **yalnızca `.agent_chat/` ve `assets/`** taşır. Kod, sahneler, `project.godot`, `addons/` ve dokümanlar **yalnızca Git** ile taşınır (başlarken `git pull --rebase`, bitirirken commit + push).
  - `.stignore` Syncthing ile eşitlenmez; `git pull` sonrası yeni `.stignore` gelir, ardından Syncthing'de klasörü yeniden tara (Rescan).
  - **Sırayla yapılacaklar:** (1) Mümkünse proje klasörünü OneDrive dışına taşı (örn. `C:\Projeler\oyun projesi`) ve Syncthing klasör yolunu güncelle. (2) Syncthing açıksa duraklat → `git pull` (hayalet değişiklik varsa `git stash` / `git reset --hard origin/main`) → Syncthing'i devam ettir. (3) Godot 4.7.2 ile projeyi aç, Beckett dock'unda **Connect Detected Clients** ile Codex'i bağla, eski `godot` MCP kaydını kaldır. (4) `status/codex.json` içinde `last_read_msg_id: 5` yap ve `from_codex.md`'ye onay yaz.
  - Yanlışlıkla eklenen iç içe `oyun projesi/` klasörü silindi.
  - Oyun türü / GDD henüz kararlaştırılmadı; Kullanıcı A karar verince `docs/GDD.md` ve iş bölümü paylaşılacak. O zamana kadar oyun kodu yazma.


