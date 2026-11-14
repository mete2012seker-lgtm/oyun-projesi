# Karşı Taraf (Codex) İçin Kurulum Promptu ve Rehberi

Bu dosyadaki promptu, arkadaşın kendi bilgisayarında **Codex**'i proje klasöründe (`oyun projesi`) açtıktan sonra doğrudan kopyalayıp yapıştırabilir.

---

## Codex'e Yapıştırılacak Hazır Prompt

```text
Bu proje, iki farklı bilgisayarda (Antigravity + Codex) ortak geliştirdiğimiz bir Godot 4.7+ oyun projesidir.
Senin rolün **Codex** ajanısın. Lütfen şu adımları sırasıyla uygula:

1. Eğer masaüstünde `oyun projesi` klasörü henüz yoksa oluştur ve içinde şu yapıyı kur (varsa eksik dosyaları tamamla):
   - Kök dosyalar: `AGENTS.md`, `.gitignore`, `.stignore` (`.godot/`, `assets/incoming/`, `*.mp4`, `*.crdownload`, `*.part` hariç tutulmalı; `*.uid` ve `*.import` korunmalı).
   - Klasörler: `.agent_chat/status/`, `.agent_chat/memory/`, `.agent_chat/for_user/`, `.agent_chat/asset_requests/refs/`, `.agent_chat/archive/`, `assets/placeholders/`.
   - Önce `AGENTS.md`, `COLLAB_PLAN.md` ve `.agent_chat/memory/codex_memory.md` dosyalarını oku ve tüm kuralları benimse.
2. `.agent_chat/` altındaki kurallar:
   - Mesaj yazarken YALNIZCA `.agent_chat/from_codex.md` dosyasına yaz (`from_antigravity.md` dosyasına yazma).
   - Durumunu YALNIZCA `.agent_chat/status/codex.json` dosyasında güncelle (UTC `Z` zaman damgası, `locked_files` ve 20 dk `lock_lease_utc` ile).
   - Her yeni sohbet başında `.agent_chat/memory/codex_memory.md` dosyasını oku; önemli kararları ve bağlamı oturum sonunda buraya kaydet.
   - Proje içindeki `.agents/skills/oyun-asamasi-baslat/SKILL.md` ve `.agents/skills/oyun-asamasi-bitir/SKILL.md` skill'lerini aktif kullan (kullanıcı "Oyun Aşaması Başlat" dediğinde ilkini, "Oyun Aşaması Bitir" dediğinde ikincisini uygula).
   - Antigravity kullanıcısına (karşı tarafa) not bırakman gerekirse `.agent_chat/for_user/from_codex.md` dosyasına yaz.
   - Görsel/video ihtiyacın olduğunda (senin Google Flow erişimin olmadığı için) `.agent_chat/asset_requests/from_codex.md` dosyasına hedef yol, boyut ve hazır Google Flow promptu bırak; varsa referans eskizi `.agent_chat/asset_requests/refs/` altına koy. O gelene kadar `assets/placeholders/` ile geliştirme yap, bloke olma.
3. Bu bilgisayarda Godot 4.7+ kurulu olup olmadığını ve aşağıdaki planlanan MCP sunucularından hangilerinin kurulması gerektiğini kontrol edip Codex MCP yapılandırmasını (`~/.codex/config.toml` veya `.codex/config.toml`) hazırla:
   - Godot kontrolü: `godot-mcp` (veya canlı editör için `godot-mcp-bridge`: https://github.com/TomasLucasUTN/godot-mcp-bridge)
   - Doküman okuma: `markitdown-mcp`
   - Web/Export testi: `playwright-mcp`
   - Piksel sanat & ses (opsiyonel, gerekiyorsa): `Godot-x-Aseprite-MCP-all` (https://github.com/Saudadeeee/Godot-x-Aseprite-MCP-all)
   - Canlı ağlar arası mesajlaşma istenirse: `kitty-hive` (https://github.com/seangx/kitty-hive)
4. Tüm kurulum ve kontroller bittiğinde:
   - `.agent_chat/status/codex.json` dosyasını `"status": "idle"`, güncel UTC `last_seen_utc`, `capabilities` ve `last_read_msg_id: 1` olacak şekilde güncelle.
   - `.agent_chat/from_codex.md` dosyasına `msg_id: 1` ile kurulumun tamamlandığını ve hazır olduğunu bildiren bir selam mesajı yaz.
```

---

## Arkadaşının Bilgisayarında Yapacağı 2 Kısa Ön Hazırlık

1. **Ortak Klasörü Klonla ve Syncthing'e Bağla:**
   - GitHub reposunu klonla:
     ```bash
     git clone https://github.com/mete2012seker-lgtm/oyun-projesi.git "oyun projesi"
     ```
   - **Syncthing**'i kur/aç (`http://127.0.0.1:8384`), **Add Remote Device** ile Kullanıcı A'nın cihazını ekle:
     `DCMZR27-C2JTY5E-WRHTPUH-SRVUEGX-ME5U34S-L7H2FZA-WX7NPXM-ZDIYWAN`
   - Paylaşılan `oyun projesi` klasörünü klonladığın bu klasörle eşleştir (`.stignore` otomatik olarak `.godot/`, `.git/` ve büyük video/indirmeleri hariç tutar).
   - **Önemli Kural:** `git pull` yapmadan önce Syncthing'de bu klasörü geçici olarak **Pause** yap, `git pull` bitince **Resume** et.
2. **Godot & Node.js:** Bilgisayarda **Godot 4.7+** ve MCP sunucuları için **Node.js (v18+)** kurulu olduğundan emin ol, ardından yukarıdaki promptu Codex'e ver.
