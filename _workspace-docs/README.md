# Workspace dokümanları — bu repo'ya ait DEĞİL

Bu klasördeki iki dosya `ohse-sites` reposunu değil, **tüm Desktop workspace'ini** anlatıyor.
Git ile taşınabilsinler diye buraya konuldular; Desktop kökü bir git reposu olmadığı için
başka türlü karşı makineye geçemiyorlardı.

## Yeni makinede ilk iş: bunları Desktop köküne taşı

PowerShell (Windows):
```powershell
Move-Item $HOME\Desktop\ohse-sites\_workspace-docs\CLAUDE.md          $HOME\Desktop\
Move-Item $HOME\Desktop\ohse-sites\_workspace-docs\AKTARIM-NOTLARI.md $HOME\Desktop\
```

macOS / Linux:
```bash
mv ~/Desktop/ohse-sites/_workspace-docs/{CLAUDE.md,AKTARIM-NOTLARI.md} ~/Desktop/
```

## Neden alt klasörde, repo kökünde değil

`CLAUDE.md` repo kökünde dursaydı Claude Code onu **bu reponun** talimatı sanardı. Oysa içeriği
`pilates-app`, `ohse-muhasebe` gibi ayrı repo'ları da kapsıyor — yanlış yerde okununca yanıltıcı
olur. Bu yüzden `_workspace-docs/` altında duruyor ve taşındıktan sonra bu klasör silinebilir.

## İçerik

- **`CLAUDE.md`** — workspace haritası. OHSE'nin cPanel deploy prosedürü, `shop.ohse.store`
  Shopify ayrımı ve **canlı ödeme çalıştıran Shopify variant ID'lerine dokunma** uyarısı burada.
  Bu dosya olmadan yeni makinedeki Claude bu kısıtları bilmez.
- **`AKTARIM-NOTLARI.md`** — 16 Ağu 2026 aktarımının kaydı: hangi repo hangi klasöre karşılık
  geliyor, Windows kurulum adımları, `.env.local`'in nasıl yeniden oluşturulacağı, silinen
  projeler.
