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

