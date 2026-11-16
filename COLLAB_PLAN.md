# Antigravity + Codex Çoklu Ajan Godot İş Birliği Planı

Bu plan, farklı ağlardaki iki bilgisayarda **Antigravity** ve **Codex** ajanlarının aynı Godot projesinde **sıfır çakışma**, **durum farkındalığı (açık/kapalı kontrolü)**, **değişiklik takibi** ve **otomatik temizleme** ile çalışması için hazırlanmıştır.

---

## 1. Klasör ve Dosya Mimarisi

```text
oyun projesi/
├── .gitignore                 # .godot/ ve geçici dosyaları hariç tutar
├── .stignore                  # Syncthing için .godot/ hariç tutma kuralları
├── AGENTS.md                  # Her iki ajanın uyması gereken ortak protokol
├── project.godot
└── .agent_chat/
    ├── from_antigravity.md    # Sadece Antigravity yazar, Codex okur
    ├── from_codex.md          # Sadece Codex yazar, Antigravity okur
    ├── CHANGELOG.md           # Yapılan tüm dosya değişikliklerinin özeti
    ├── status/
    │   ├── antigravity.json   # Antigravity nabız (heartbeat), aktif görev ve kilitli dosyalar
    │   └── codex.json         # Codex nabız (heartbeat), aktif görev ve kilitli dosyalar
    ├── memory/
    │   ├── antigravity_memory.md  # Antigravity oturumlar arası kalıcı bağlam ve kararlar
    │   └── codex_memory.md        # Codex oturumlar arası kalıcı bağlam ve kararlar
    └── archive/               # Temizlenen eski mesajların taşındığı arşiv
```

---

## 2. Çalışma Akışı (Adım Adım)

1. **Senkronizasyon Katmanı:**
   - **Anlık (iki bilgisayar açıkken):** **Syncthing** (P2P, farklı ağlarda kendi röleleri üzerinden bağlanır). `.stignore` ile `.godot/` hariç tutulur; `*.uid` ve `*.import` dosyaları mutlaka eşitlenir.
   - **Asenkron (biri kapalıyken):** Ortak **GitHub** deposu. Bilgisayar kapalıysa diğer taraf işini bitirip `git push` yapar; kapalı bilgisayar açıldığında `git pull` ile güncel durumu alır.
2. **Başlangıç Kontrolü (Her Görev Öncesi Zorunlu):**
   - Ajan çalışmaya başlamadan önce:
     1. `status/<peer>.json` dosyasındaki `last_seen_utc` değerine bakar. Son 5 dakika içinde güncellenmişse karşı taraf **çevrimiçi** kabul edilir; değilse **çevrimdışı (asenkron)** moda geçilir.
     2. `from_<peer>.md` gelen kutusunu ve `CHANGELOG.md` dosyasını okur; takım arkadaşının ne eklediğini öğrenir.
     3. Düzenleyeceği dosyanın `status/<peer>.json` içindeki `locked_files` listesinde olmadığından emin olur ve kendi `locked_files` listesine ekler.
3. **Değişiklik Bildirimi:**
   - Her görev sonunda ajan:
     - Değiştirdiği dosyaları ve ne yaptığını `CHANGELOG.md` dosyasına tek satır/blok olarak ekler.
     - Karşı tarafa sıradaki adımı veya notu kendi kanalına (`from_antigravity.md` / `from_codex.md`) `msg_id` ile yazar.
     - Kendi `status/<self>.json` dosyasındaki `locked_files` listesini temizler.
4. **Otomatik Sıfırlama / Temizleme (Reset):**
   - Mesaj dosyası 50 satırı geçtiğinde veya görev tamamlandığında ajan **sadece kendi yazdığı** `from_<self>.md` dosyasını `.agent_chat/archive/` altına taşır ve dosyayı sıfırlar.

---

## 3. Olası Çöküş Senaryoları, Hatalar ve Çözümleri

| # | Olası Hata / Çöküş | Neden Olur? | Önlem / Çözüm |
|---|---|---|---|
| 1 | **Godot `.godot/` bozulması ve reimport döngüsü** | `.godot/` klasörünün ve `uid_cache.bin` dosyasının iki PC arasında senkronize edilmesi. | `.stignore` ve `.gitignore` içine `.godot/` mutlaka eklenir; `*.uid` ve `*.import` ise korunur. |
| 2 | **Aynı `.tscn` sahnesinde `sync-conflict` çakışması** | İki ajanın aynı anda `main.tscn` veya `project.godot` dosyasına yazması. | `status/*.json` içinde `locked_files` kilidi + sahneleri modüler ayırma (`player.tscn`, `ui.tscn`, `world.tscn`). |
| 3 | **Okunmamış mesajın erken silinmesi (Reset Hatası)** | Antigravity, Codex mesajı henüz okumadan `from_antigravity.md` dosyasını sıfırlarsa mesaj kaybolur. | **Ack-before-reset:** Her mesajın `msg_id` numarası vardır. Ajan, karşı tarafın `status/*.json` içindeki `last_read_msg_id` değeri güncellenmeden o mesajı arşivlemez/silmez. |
| 4 | **Yanlış Çevrimiçi/Çevrimdışı Tespiti (Saat Farkı)** | İki bilgisayarın yerel saatlerinin farklı olması. | Tüm `last_seen_utc` alanları zorunlu olarak **ISO-8601 UTC (`Z`)** formatında yazılır. |
| 5 | **Godot Editörünün RAM'deki eski sahneyi ezmesi** | Editörde sahne açıkken ve kaydedilmemişken karşıdan yeni `.tscn` gelmesi. | Göreve başlamadan önce editörde `Ctrl+S` ile temiz durum sağlanır; kilitli sahnelere dışarıdan yazılmaz. |
| 6 | **Biri kapalıyken Syncthing'in senkronize edememesi** | P2P Syncthing yalnızca iki cihaz da açıkken anlık çalışır. | **Hibrit model:** Anlık işler Syncthing üzerinden akarken, oturum kapanışında mutlaka `git commit && git push` yapılır. |

---

## 4. Görsel Üretim Stratejisi (Google Flow — Yarı Otomatik & Sıfır Ban Riski)

Google Cloud kredilerinin kullanılmayabileceği ve resmi olmayan tarayıcı otomasyonlarının ban/ToS riski taşıdığı göz önünde bulundurularak **ana görsel üretim stratejisi** şu şekilde planlanmıştır:

1. **Prompt Hazırlığı:** Antigravity ve Codex, gereken karakter, taş platform, çiçek ve arka plan görselleri için hazır promptları ve varsa referans eskiz/ekran görüntülerini `.agent_chat/asset_requests/` (`from_codex.md` / `refs/`) altına yazar. Codex ayrıca Antigravity kullanıcısına bırakmak istediği özel notları `.agent_chat/for_user/from_codex.md` dosyasına bırakır; Antigravity bir sonraki seansta bunları otomatik okuyup sana iletir.
2. **Kullanıcı Üretimi (Google Flow):** Kullanıcı, tarayıcıda Google AI Pro aboneliğindeki Flow kredilerini kullanarak görselleri üretir ve `assets/incoming/` klasörüne kaydeder (ban riski **sıfır**).
3. **Otomatik İşleme ve Entegrasyon:** `assets/incoming/` içine düşen görseller, ajanlar (veya Python/Pillow işleme scripti) tarafından otomatik olarak arka plandan temizlenir, piksel/çözünürlük ayarı yapılır, `assets/sprites/` altına taşınır ve Godot sahnelerine bağlanır.

---

## 5. Planlanan MCP Sunucuları, Skill'ler ve Eklentiler (Kurulum Yol Haritası)

> **Not:** Bu araçlar şu an kurulu değildir; ihtiyaç duyulan aşamada sırayla entegre edilmek üzere planlanmıştır.

| Kategori | Araç / Eklenti | Kullanım Amacı |
|---|---|---|
| **Ajan Haberleşmesi** | `kitty-hive` (veya `agent-collab-hub`) | Farklı ağlardaki Antigravity ve Codex arasında mesajlaşma, görev devri ve Cloudflare Tunnel ile güvenli bağlantı. |
| **Godot Editör** | `beckett-godot-mcp` (`addons/beckett/`) | Godot 4.7+ editör içi canlı MCP eklentisi (`http://127.0.0.1:8770/mcp`): sahne/düğüm düzenleme (`Ctrl+Z` destekli), `write_script` doğrulama ve çalışan oyundan `screenshot` / `game_logs` okuma. (`.mcp.json` ve `.beckett/` hariç tutulur). |
| **2D Piksel Sanat & Ses** | `Godot-x-Aseprite-MCP-all` (`aseprite`, `rfxgen`, `ffmpeg`) | Sprite/animasyon düzenleme, retro 8-bit ses efektleri üretimi ve Godot uyumlu `.ogg` dönüşümü. |
| **Doküman Okuma** | `markitdown-mcp` | PDF, Word (`.docx`), tablo ve tasarım dokümanlarını (GDD) anında Markdown'a çevirme. |
| **Word Düzenleme (Opsiyonel)** | `Office Word MCP` | Hikaye veya GDD doğrudan `.docx` olarak düzenlenecekse kullanılır. |
| **Tarayıcı & Test** | `playwright-mcp` | Doküman tarama ve Godot HTML5/Web export çıktısını tarayıcıda otomatik test etme (Flow otomasyonu için kullanılmaz). |
| **Godot İçi Senkronizasyon** | `Backstitch` eklentisi | Godot editörü içinde sahne ve kaynak değişikliklerini gerçek zamanlı eşitleme. |
| **Skill'ler** | `AGENTS.md`, `kur` skill'i | `AGENTS.md` ortak iş birliği protokolünü yönetir; `kur` skill'i yeni GitHub MCP sunucularını otomatik kurar. |

---

## 6. Derinlemesine Çöküş Analizi ve Çözümler (Sub-Agent Denetimi)

Yalnızca **Kullanıcı A (Antigravity)** tarafının Google Flow'a erişebildiği, **Kullanıcı B (Codex)** tarafının ise Flow'suz çalıştığı asimetrik düzende tespit edilen kritik çöküş noktaları ve çözümleri:

### P0 — Kritik (Veri Kaybı / Bozulma / Kilitlenme)
1. **Syncthing + Git Çift-Senkronizasyon Bölünmesi (Split-Brain):**
   - *Sorun:* `.stignore` `.git` klasörünü hariç tutarken Syncthing çalışma dosyalarını eşitler. Kapalı olan PC açıldığında Syncthing dosyaları günceller ama o PC'nin yerel `.git/HEAD` indeksi geride kalır; `git status` hayalet değişiklikler gösterir ve `git pull` hata verir. Ayrıca `.gitignore` içindeki `*.sync-conflict-*` çakışma dosyalarını gizler.
   - *Çözüm:* Kod ve sahneler için ya **tek Git-committer** (veya Git çekmeden önce Syncthing duraklatma) uygulanır ya da Git sadece kod/sahneleri (`agent/antigravity` & `agent/codex` dalları), Syncthing ise yalnızca `.agent_chat/` ve `assets/` klasörlerini taşır. Görev öncesi `*.sync-conflict-*` taraması zorunlu hale getirilir.
2. **`CHANGELOG.md` ve `asset_prompts.md` Çift Yazım Çakışması:**
   - *Sorun:* İki ajanın aynı `CHANGELOG.md` veya `asset_prompts.md` dosyasına ekleme yapması *Tek Yazar Kuralı*'nı bozar ve `sync-conflict` üretir.
   - *Çözüm:* Tek yazarlı dosyalara bölünür: `.agent_chat/changelog/antigravity.md` ve `codex.md` (veya olay başına `.agent_chat/events/<utc>_<agent>.md`); prompt istekleri de `asset_requests/from_antigravity.md` ve `from_codex.md` olarak ayrılır.
3. **Asimetrik Flow Hattı (`assets/incoming/`) Yarışları ve Video Şişmesi:**
   - *Sorun:* Tarayıcı indirmeleri yarımken (`.crdownload`/`.part`) Syncthing eksik dosya taşıyabilir; iki PC aynı ham görseli aynı anda işleyip çift `.uid`/`.import` üretebilir; büyük Flow videoları (`.mp4`) Syncthing rölelerini tıkar ve GitHub'ın 100 MB sınırına takılır; Kullanıcı A çevrimdışıyken Codex görsel bekleyip kilitlenir.
   - *Çözüm:* `assets/incoming/`, `assets/incoming_staging/`, `*.crdownload`, `*.part`, `*.partial`, `*.mp4`, `*.webm`, `*.mov` hem `.stignore` hem `.gitignore` içine eklenir. Ham dosyaları **yalnızca PC A (Antigravity)** işler ve sadece hafif son çıktıları (`assets/sprites/*.png`, `assets/audio/*.ogg`) eşitlenen klasöre yazar. Codex ise görsel beklerken `assets/placeholders/` ile geliştirme yapar ve bloke olmaz.

### P1 — Yüksek (Kilitlenme / Sahne Bozulması)
4. **`locked_files` Çökme Kilitlenmesi (Deadlock) ve TOCTOU Yarışı:**
   - *Sorun:* Bir ajan çökerse veya laptop uyursa `locked_files` sonsuza kadar kilitli kalır; ayrıca iki ajan aynı anda boş görüp aynı dosyayı kilitleyebilir.
   - *Çözüm:* `status/*.json` içine `lock_lease_utc` (20 dk TTL) ve `session_id` eklenir; süre dolmuş ve `last_seen_utc` > 15 dk ise kilit kırılabilir. Kilitleme sırasında **yaz → 2-3 sn bekle → karşıyı tekrar oku** (eşitlikte Codex Antigravity'ye yol verir) uygulanır.
5. **`ack-before-reset` `msg_id` Çakışması:**
   - *Sorun:* Tek dosya sıfırlanırken `msg_id` tekrar `1`'e dönerse, karşı taraftaki `last_read_msg_id` yeni mesajları "okunmuş" sanabilir.
   - *Çözüm:* Her mesaj ayrı, değişmez dosyaya yazılır (`.agent_chat/outbox/<agent>/0001.md`, `0002.md`...) ve `msg_id` hiçbir zaman sıfırlanmaz; yalnızca okunmuş eski dosyalar `archive/` içine taşınır.
6. **Godot 4.7 + Syncthing + `godot-mcp-bridge` + `Backstitch` Çarpışması:**
   - *Sorun:* Aynı anda hem `Backstitch` hem `Syncthing` hem `godot-mcp-bridge` kullanmak 4 yönlü yazma çakışması yaratır; ayrıca `.gd` ile `.gd.uid` farklı anda gelirse Godot yanlış UID üretir.
   - *Çözüm:* Syncthing + `godot-mcp-bridge` kullanılıyorsa **`Backstitch` çıkarılır** (iki gerçek zamanlı sahne senkronizörü aynı anda çalıştırılmaz). Aynı sahne iki PC'de aynı anda açık tutulmaz.

### P2 — Orta (Planlanan MCP Tuzakları)
7. **`kitty-hive` Cloudflare Tünel Kopmaları ve Çift Mesaj Kaynağı:** Kalıcı tünel + bearer token kullanılır; `.agent_chat/` ile aynı anda iki ayrı mesaj kaynağı yerine biri ana, diğeri çevrimdışı yedek olarak tanımlanır.
8. **Aseprite / `playwright-mcp` / `markitdown-mcp`:** `status/*.json` içinde `capabilities` (`["flow", "aseprite", "godot_editor"]`) tanımlanarak görevler yalnızca o araca sahip PC'ye yönlendirilir; Godot Web export Playwright testleri için yerel COOP/COEP sunucusu kullanılır; GDD Markdown dönüşümü tek ajana (`Antigravity`) atanır.

---

## 7. Obsidian Kasası (Vault) ve Grafik Görünümü Stratejisi

Proje klasörü doğrudan bir **Obsidian Vault** olarak yapılandırılmıştır:

- **Merkez Harita (`INDEX.md`):** Tüm plan, kural, bellek ve mesaj kanalları `[[wikilink]]` bağlantılarıyla [INDEX.md](INDEX.md) üzerinden birbirine bağlanır. Obsidian'da **Graph View (`Ctrl+G`)** açıldığında Antigravity (mavi), Codex (turuncu), Ana Plan (yeşil) ve Görsel İstekler (turkuaz) renk gruplarıyla görsel harita halinde izlenir.
- **Sıfır Çakışma Ayarı:** `.obsidian/workspace.json` ve önbellek dosyaları `.stignore` / `.gitignore` ile hariç tutulur; böylece iki farklı bilgisayarda Obsidian açıkken pencere düzeni çakışması yaşanmaz.
- **Godot Koruması (`.gdignore`):** `.obsidian/` ve `.agent_chat/` içine eklenen `.gdignore` dosyaları sayesinde Godot Editörü bu dokümantasyon/kasa klasörlerini oyun varlığı sanıp taramaz.



