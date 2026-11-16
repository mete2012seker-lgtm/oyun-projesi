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
- `project.godot` oluşturuldu (Godot 4.7.2, Forward Plus, `BeckettRuntime` autoload, Beckett plugin aktif). Beckett MCP 8770 portunda token ile çalışıyor (`.mcp.json`, yerel).
- `[2026-11-16T09:23Z]` **Rol ayrımı kararı:** Syncthing yalnızca `.agent_chat/` + `assets/`; kod/sahneler/addons yalnızca Git. `AGENTS.md` §2'ye yazıldı. İç içe `oyun projesi/` klasörü silindi.
- Syncthing: bu PC "Metepc" (DCMZR27), karşı "Arkadas-PC" (JIVB3BM), folder `oyun-projesi`. Son kontrolde karşı cihaz bağlı değildi.
- Codex son görülme 2026-11-14T18:51Z, `last_read_msg_id: 1`; msg 2-5 onay bekliyor. Codex yolu OneDrive altında (taşıması istendi).

## Sıradaki Adımlar
- Kullanıcı A'dan **oyun türü** kararı al → `docs/GDD.md`, klasör yapısı (`scenes/`, `scripts/`, `assets/sprites|audio`), `run/main_scene`, input map.
- Önerilen iş bölümü: Antigravity = ana sahne/oyun döngüsü/UI + Flow görselleri; Codex = oyuncu/düşman/fizik (placeholder ile).
- Codex onayını (`from_codex.md`) ve Syncthing bağlantısını kontrol et.
