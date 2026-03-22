# Katkıda Bulunma

Claude HUD'a katkıda bulunduğunuz için teşekkürler. Bu repo küçük ve hızlı gelişiyor, bu yüzden netlik ve hızlı incelemeyi ön planda tutuyoruz.

## Nasıl Katkıda Bulunulur

1. Repoyu fork'layın ve klonlayın
2. Bir branch/dal oluşturun
3. Değişikliklerinizi yapın
4. Testleri çalıştırın ve gerekirse dokümantasyonu güncelleyin
5. Pull request açın

## Geliştirme

    npm ci
    npm run build
    npm test

## Testler

Tam test stratejisi, fikstürler ve snapshot güncellemeleri için `TESTING.md` dosyasına bakın.

## Kod Stili

- Değişiklikleri odaklı ve küçük tutun.
- Davranış değişiklikleri için test yazmayı tercih edin.
- Gerekli olmadıkça eklenti/dependency eklemeyin.

## Derleme Süreci

**Önemli**: PR'lar yalnızca `src/` içindeki dosyaları değiştirmeli — `dist/` değişikliklerini dahil etmeyin.

PR'ınız merge edildikten sonra CI otomatik olarak `dist/` klasörünü derler ve commit eder. Bu sayede PR'lar kaynak koda odaklanır ve inceleme kolaylaşır.

    PR'ınız: yalnızca src/ değişiklikleri → Merge → CI dist/ derler → Otomatik commit edilir

## Pull Request'ler

- Sorunu ve çözümü açıklayın.
- Testleri ekleyin ya da neden gerekli olmadığını açıklayın.
- İlgili issue'ları bağlayın.
- Yalnızca `src/` dosyalarını değiştirin — `dist/` CI tarafından otomatik yönetilir.

## Yeni Sürüm Yayınlama

Yeni bir sürüm çıkarırken:

1. **Sürüm numaralarını** şu üç dosyada güncelleyin:
   - `package.json` → `"version": "X.Y.Z"`
   - `.claude-plugin/plugin.json` → `"version": "X.Y.Z"`
   - `.claude-plugin/marketplace.json` → `"version": "X.Y.Z"`

2. **CHANGELOG.md**'yi son sürümden bu yana yapılan değişikliklerle güncelleyin

3. **Commit'leyip merge edin** — CI, `dist/` klasörünü otomatik olarak derler

### Kullanıcılar Güncellemeleri Nasıl Alır

Claude Code eklentileri `/plugin` arayüzü üzerinden güncellemeyi destekler:

- **Şimdi güncelle** — Ana branchden/daldan en son sürümü çeker ve hemen yükler
- **Güncelleme için işaretle** — Güncellemeyi daha sonra yapmak üzere bekletir

Claude Code, `plugin.json` içindeki `version` alanını yüklü sürümle karşılaştırır. Sürüm numarasını artırmak (örn. 0.0.1 → 0.0.2) kullanıcıların güncelleme olduğunu görmesini sağlar.

### Sürüm Stratejisi

Anlamsal sürümleme (`MAJOR.MINOR.PATCH`) kullanıyoruz:

- **PATCH** (0.0.x): Hata düzeltmeleri, küçük iyileştirmeler
- **MINOR** (0.x.0): Yeni özellikler, geriye dönük uyumlu değişiklikler
- **MAJOR** (x.0.0): Geriye dönük uyumsuz değişiklikler
