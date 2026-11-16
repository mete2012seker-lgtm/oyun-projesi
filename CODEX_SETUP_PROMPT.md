# Karşı Taraf (Codex) İçin Tam Otomatik Kurulum Promptu

Arkadaşın kendi bilgisayarında **Codex**'i açıp aşağıdaki promptu doğrudan yapıştırabilir. Prompt; eksik programları, proje klasörünü, skill'leri ve MCP sunucularını **önce kontrol eder, eksik olanları kurar** ve sistemi çalışır hale getirir.

---

## Codex'e Yapıştırılacak Hazır Prompt

```text
Sen iki farklı bilgisayarda (Antigravity + Codex) ortak geliştirdiğimiz Godot 4.7+ oyun projesinde **Codex** ajanısın.
Lütfen bu bilgisayarda aşağıdaki kontrolleri ve eksik kurulumları sırasıyla yap. Zaten kurulu/mevcut olanları atla, eksik olanları kur ve en sonunda bana özet rapor ver.

### 1. Sistem Araçlarını Kontrol Et ve Eksikleri Kur (Windows için `winget` kullan)
Aşağıdaki programların kurulu olup olmadığını kontrol et, kurulu olmayanları kur:
- **Git** (`Git.Git`)
- **Node.js LTS (v18+)** (`OpenJS.NodeJS.LTS`)
- **Godot Engine 4.7+** (`GodotEngine.GodotEngine`)
- **Syncthing** (`Syncthing.Syncthing`)
- **Obsidian** (`Obsidian.Obsidian`)

### 2. Proje Klasörünü ve Dosyaları Kontrol Et / Klonla
- Masaüstünde `oyun projesi` klasörünü kontrol et. Yoksa şu GitHub reposunu masaüstüne `oyun projesi` adıyla klonla:
  `git clone https://github.com/mete2012seker-lgtm/oyun-projesi.git "oyun projesi"`
- Varsa ve içi güncellenmemişse `git pull origin main` yap (Syncthing aktifse pull öncesi klasörü Syncthing'de geçici **Pause** yap).
- Şu dosya ve klasörlerin varlığını doğrula (eksik varsa oluştur):
  - `AGENTS.md`, `COLLAB_PLAN.md`, `INDEX.md`, `.gitignore`, `.stignore`
  - `.agent_chat/status/`, `.agent_chat/memory/`, `.agent_chat/for_user/`, `.agent_chat/asset_requests/refs/`, `.agent_chat/archive/`, `.obsidian/`, `assets/placeholders/`
- `AGENTS.md`, `COLLAB_PLAN.md` ve `.agent_chat/memory/codex_memory.md` dosyalarını oku ve kuralları benimse.

### 3. Skill'leri Aktar ve Kontrol Et (`oyun-asamasi-baslat` & `oyun-asamasi-bitir`)
- Proje içindeki `.agents/skills/oyun-asamasi-baslat/SKILL.md` ve `.agents/skills/oyun-asamasi-bitir/SKILL.md` dosyalarının varlığını doğrula.
- Bu iki skill'i Codex'in küresel skill dizinine de (`~/.codex/skills/` veya `~/.agents/skills/`) kopyala ki her oturumda doğrudan tanıyabilesin.
- Kullanıcı "Oyun Aşaması Başlat" dediğinde ilkini, "Oyun Aşaması Bitir" dediğinde ikincisini çalıştır.

### 4. MCP Sunucularını Kontrol Et ve Eksikleri Kur (`~/.codex/config.toml`)
Codex MCP yapılandırmasını kontrol et; eksik olanları kur ve yapılandır:
1. **Godot MCP (Beckett):** Proje içinde hazır bulunan `addons/beckett/` eklentisini kullanıyoruz (`https://github.com/beckettlab/beckett-godot-mcp`). Godot Editöründe **Project → Project Settings → Plugins** içinden **Beckett** eklentisini etkinleştirip sağ paneldeki **Connect Detected Clients** butonuna bas (veya `~/.codex/config.toml` içindeki eski `godot-mcp` yerine `[mcp_servers.beckett]` olarak `http://127.0.0.1:8770/mcp` adresini tanımla). `.mcp.json` ve `.beckett/` dosyaları `.gitignore` / `.stignore` ile yerel tutulur.
2. **MarkItDown MCP:** `markitdown-mcp` (doküman/GDD okuma için).
3. **Playwright MCP:** `@playwright/mcp` (web araştırması ve Godot Web export testi için).
4. **Opsiyonel (İhtiyaç Halinde):** Piksel sanat ve ses için `Saudadeeee/Godot-x-Aseprite-MCP-all` (`aseprite`, `rfxgen`, `ffmpeg`), canlı ağlar arası ajan mesajlaşması için `seangx/kitty-hive`.

### 5. Syncthing ve Obsidian Ayarını Hazırla
- Syncthing henüz başlatılmadıysa `syncthing generate` ile sertifikayı oluştur ve bu bilgisayarın **Syncthing Device ID** kodunu ekrana yazdır (arkadaşımıza/Kullanıcı A'ya göndermemiz gerekecek).
- Kullanıcı A'nın cihaz ID'si: `DCMZR27-C2JTY5E-WRHTPUH-SRVUEGX-ME5U34S-L7H2FZA-WX7NPXM-ZDIYWAN` — Syncthing üzerinden bu cihazı ekle ve `oyun projesi` klasörünü eşleştirmeye hazırla (`.stignore` otomatik olarak `.godot/`, `.git/`, `.obsidian/workspace.json` ve `assets/incoming/` klasörlerini hariç tutacaktır).
- Obsidian'da `oyun projesi` klasörünün **Open folder as vault** ile açılabileceğini doğrula (`INDEX.md` + `Ctrl+G` Graph View hazır).

### 6. İletişim Kuralları ve İlk Selamlaşma
- Mesaj yazarken YALNIZCA `.agent_chat/from_codex.md` ve durum için YALNIZCA `.agent_chat/status/codex.json` kullan (`from_antigravity.md` dosyasına yazma).
- Kullanıcı A'ya (Antigravity'nin kullanıcısına) not bırakmak için `.agent_chat/for_user/from_codex.md` kullan.
- Senin Google Flow erişimin olmadığı için görsel/video gerektiğinde `.agent_chat/asset_requests/from_codex.md` dosyasına hedef yol, boyut ve hazır Google Flow promptu yaz; o gelene kadar `assets/placeholders/` ile devam et.
- Kurulum bitince `.agent_chat/status/codex.json` dosyasını `"status": "idle"`, güncel UTC `last_seen_utc`, `capabilities` ve `last_read_msg_id: 1` olarak güncelle; `.agent_chat/from_codex.md` dosyasına `msg_id: 1` ile hazır olduğunu bildir.
- Son olarak bana: (a) nelerin zaten kurulu olduğunu, (b) neleri yeni kurduğunu ve (c) benim **Syncthing Device ID** kodumu listele.
```
