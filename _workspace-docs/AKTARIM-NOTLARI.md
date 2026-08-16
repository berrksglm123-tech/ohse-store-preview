# Proje Aktarımı — Yeni Bilgisayar Kurulumu

Hazırlanma: 16 Ağustos 2026. Kaynak makine: bu Mac (`sarpsaglam`, arm64, Node v24.15.0).
Hedef: ikinci bilgisayar + ikinci GitHub/Claude hesabı.

## Aktarılan repo'lar (4)

Hepsi **`berrksglm123-tech`** hesabında, **private**, 16 Ağu 2026'da push edildi.

| Repo | Yerel klasör | İçerik | Kurulum |
|------|--------------|--------|---------|
| `ohse-site` | `Desktop/ohse-sites/` | `ohse/` + `countdown/` — statik, build yok (88 dosya) | `python3 -m http.server` |
| `noizy-site` | `Desktop/noizy/` | NOIZY statik site, ayrı repo (10 dosya) | `python3 -m http.server` |
| `ohse-app` | `Desktop/ohse-app/` | Expo / React Native (76 dosya) | `npm install` |
| `ohse-muhasebe` | `Desktop/ohse-muhasebe/` | Next.js ERP / CEO dashboard (71 dosya) | `npm install` + `.env.local` |

**Repo adı ≠ klasör adı** iki yerde: `ohse-site` → `ohse-sites/`, `noizy-site` → `noizy/`.

**Dikkat:** `noizy/index.html` tasarımın kaynağı, `ohse-sites/ohse/store.html` onun birebir
kopyası. Artık **ayrı repo'lardalar** — birini değiştirince diğerini güncellemek iki ayrı
commit demek. Bu bilinçli bir tercih.

**`pilates-app` bu aktarıma dahil değil** — eski hesapta (`sarpsaglam3/pilates-app`) kalıyor ve
oradan Vercel'e deploy oluyor.

## Yeni makinede yapılacaklar — hedef makine **Windows**

Önce üç şey kurulu olmalı:
- **Git for Windows** — <https://git-scm.com/download/win>
- **Node.js LTS** — <https://nodejs.org> (`ohse-app` ve `ohse-muhasebe` için gerekli)
- **GitHub CLI** — <https://cli.github.com> (opsiyonel; `git clone` de yeter)

PowerShell'de:

```powershell
cd $HOME\Desktop
git clone https://github.com/berrksglm123-tech/ohse-site.git     ohse-sites
git clone https://github.com/berrksglm123-tech/noizy-site.git    noizy
git clone https://github.com/berrksglm123-tech/ohse-app.git
git clone https://github.com/berrksglm123-tech/ohse-muhasebe.git

cd $HOME\Desktop\ohse-app      ; npm install
cd $HOME\Desktop\ohse-muhasebe ; npm install
```

Repo'lar private, ilk `git clone`'da GitHub kimliği sorar — `berrksglm123-tech` hesabıyla gir.
Klasör adlarını bu makinedekiyle aynı tutmak için `clone` komutlarına hedef ad verildi
(`ohse-site` → `ohse-sites`, `noizy-site` → `noizy`).

**Satır sonu ayarı** — iki makinede de aynı dosyalara dokunacaksan gereksiz diff çıkmasın diye,
Windows'ta bir kez:
```powershell
git config --global core.autocrlf true
```

**Statik siteleri önizleme** (`ohse-sites`, `noizy`) — build yok, sadece sunucu lazım:
```powershell
cd $HOME\Desktop\ohse-sites\ohse
python -m http.server
```

Claude Code kurulumu (aktarım için şart değil):
```powershell
npm install -g @anthropic-ai/claude-code
```
Bu yöntem Windows'ta kesin çalışır ve Node'u zaten kurmuş olacaksın. Anthropic'in native
Windows yükleyicisi de var; güncel komut için <https://docs.anthropic.com/en/docs/claude-code>
sayfasına bak. (Bu makinedeki `curl … install.sh` komutu macOS/Linux içindir, Windows'ta çalışmaz.)

## Gizli dosyalar — repo'da YOK, elle taşınacak

`.gitignore`'da oldukları için GitHub'a hiç girmediler. Hedef makine Windows olduğu için
**AirDrop yok**. Dosyayı olduğu gibi taşımak yerine karşıda **sıfırdan oluştur** — böylece
`service_role` anahtarı hiçbir kanalda dolaşmaz.

`ohse-muhasebe/.env.local` içindeki 6 anahtar ve her birinin ne yapılacağı:

| Anahtar | Ne yapmalı |
|---------|-----------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase panelinden yeniden kopyala |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase panelinden yeniden kopyala |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase panelinden yeniden kopyala — **taşıma** |
| `OWNER_PIN` | Zaten biliniyor, elle yaz |
| `AUTH_SECRET` | **Tek gerçek taşınması gereken.** Değişirse açık oturumlar düşer (kritik değil, yeniden giriş yeterli) |
| `VERCEL_OIDC_TOKEN` | Hiç taşıma, `vercel` CLI yeniden üretir |

Supabase değerleri: <https://supabase.com> → proje → **Project Settings → API**.

`AUTH_SECRET`'i taşımak için şifre yöneticisi (1Password, Bitwarden) veya USB kullan.
E-posta, WhatsApp, Notlar veya sohbete yapıştırma. Alternatif: yeni bir değer üret ve
her iki tarafta da onu kullan — tek sonucu mevcut oturumların düşmesi.

Windows'ta dosya adı `.env.local` — Explorer nokta ile başlayan ad oluşturmayı zorlaştırır;
PowerShell'den yap: `New-Item -Path .env.local -ItemType File`

## Claude Code tarafı

- **`Desktop/CLAUDE.md` ve bu dosya git'e DAHİL DEĞİL** — Desktop kökü bir repo değil, dolayısıyla
  bu iki dosya push ile karşıya geçmez. `.env.local` ile birlikte **elle** kopyala; `CLAUDE.md`
  olmadan yeni makinedeki Claude, OHSE deploy kuralları ve Shopify variant ID uyarısı gibi
  load-bearing bilgileri bilmez.
- Repo içi dokümanlar (`pilates-app/CLAUDE.md`, `pilates-app/spec.md`) klonla birlikte gelir.
- **Hafıza dosyaları** (`~/.claude/projects/-Users-sarpsaglam/memory/`, 6 dosya) elle
  kopyalanmalı. Windows'taki karşılığı: `C:\Users\<kullanici>\.claude\projects\<proje-slug>\memory\`.
  Slug, çalışılan dizinden türediği için Windows'ta farklı olur — en kolayı karşıda Claude Code'u
  bir kez çalıştırıp oluşan klasörü bulmak, sonra dosyaları oraya kopyalamak.
  İçindekiler: `MEMORY.md`, `nova-zeka-business.md`, `project_ohse_brand_identity.md`,
  `project_ohse_landing.md`, `project_ohse_streetwear.md`, `wati-mcp-setup.md`.
  **Not:** bu hafıza dosyaları `~/streetwear-site`, `~/ohse-teaser`, `~/ohse-landing` gibi
  aktarılmayan prototip klasörlerine atıf yapıyor — yeni makinede o yollar bulunmayacak.
- **`~/.claude.json` KOPYALANMAMALI** — içindeki MCP OAuth token'ları eski hesaba bağlı.
  Yeni hesapta MCP sunucularını (WATI, Higgsfield vb.) sıfırdan bağla.

## Bu makinede yapılan değişiklikler (16 Ağu 2026)

- `Desktop/ohse/` ve `Desktop/countdown/` → `Desktop/ohse-sites/` altına taşındı.
  `Desktop/noizy/` yerinde kaldı, kendi repo'su oldu.
- **Kalıcı olarak silindi** (kullanıcı onayıyla, Çöp Kutusu'na gitmedi, yedeği yok):
  `Desktop/ohse-design-studio/` (467M, 52 kaynak dosya),
  `Desktop/isimos/` (517M, 87 kaynak dosya, spec.md + NOTES.md + canlı Supabase anahtarları),
  `Desktop/ohse-fatura/` (164K, 4 dosya). Toplam ~984M.
  → `isimos` ve `ohse-design-studio`'nun Supabase projeleri hâlâ canlı olabilir; kullanılmayacaksa
  Supabase panelinden kapatılması veya anahtarların iptal edilmesi iyi olur.
- `Desktop/CLAUDE.md` yeni yapıya göre güncellendi.
- `ohse-app` (13 dosya) ve `ohse-muhasebe` (42 dosya) commit'lenmemiş işleri commit'lendi.
- `gh` CLI kuruldu: `~/.local/bin/gh` (v2.97.0).

## Aktarılmayanlar

`Desktop/eda` (kişisel sayfa), `Desktop/OHSE-SON-GUNCEL`, `Desktop/OHSE-yukle-2026-06-19`
(deploy yedekleri), kökteki `*.zip` deploy paketleri, `~/ohse-landing`, `~/ohse-teaser`,
`~/ohse-nav`, `~/ohse-blister`, `~/ohse-deploy`, `~/streetwear-site`, `~/spline-sayac-site`
(eski prototipler), `~/LangAlpha` ve `~/n8n-mcp` (başkasının repo'ları, yeniden klonlanabilir),
`~/wati-mcp`.
