# Antigravity Kalıcı Bellek (Oturumlar Arası Bağlam)
<!-- Yalnızca Antigravity günceller. Her yeni sohbette ilk okunacak dosyadır. -->

## Proje Özeti ve Hedefler
- **Proje:** Antigravity (Kullanıcı A) + Codex (Kullanıcı B) ortak Godot 4.7+ oyunu.
- **Klasör:** Masaüstü / `oyun projesi`.
- **Varlık Stratejisi:** Yalnızca Kullanıcı A Google Flow erişimine sahiptir (`assets/incoming/` -> yalnızca PC A işler -> `assets/sprites/`). Codex görsel beklerken `assets/placeholders/` kullanır.

## Alınan Kritik Kararlar
- Tek yazarlı iletişim kanalları (`from_antigravity.md`, `from_codex.md`, `for_user/from_codex.md`, `asset_requests/from_codex.md`).
- `locked_files` + `lock_lease_utc` (20 dk TTL) ile çakışma önleme.
- Syncthing `.stignore` ve Git `.gitignore` ile `.godot/`, `assets/incoming/`, `*.mp4` hariç tutulur.

## Son Durum ve Sıradaki Adımlar
- Godot MCP olarak `beckettlab/beckett-godot-mcp` (`addons/beckett/`) kuruldu ve `mcp_config.json` içinde eski `godot-mcp` yerine `beckett` (`http://127.0.0.1:8770/mcp`) tanımlandı.
- Google Flow MCP kurulmadı (yarı otomatik `asset_requests/` -> `assets/incoming/` stratejisi korunuyor).
- Codex'e `msg_id: 2` ile bilgilendirme bırakıldı ve GitHub'a pushlandı. Godot projesi (`project.godot`) oluşturulduğunda Beckett eklentisi etkinleştirilip oyuna başlanacak.
