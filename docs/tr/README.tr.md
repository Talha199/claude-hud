# Claude HUD

Claude Code eklentisi — bağlam kullanımı, aktif araçlar, çalışan ajanlar ve yapılacaklar listesi gibi bilgileri anlık gösterir. Giriş alanının altında her zaman görünür.

[![Lisans](https://img.shields.io/github/license/jarrodwatts/claude-hud?v=2)](LICENSE)
[![Yıldızlar](https://img.shields.io/github/stars/jarrodwatts/claude-hud)](https://github.com/jarrodwatts/claude-hud/stargazers)

![Claude HUD çalışırken](/claude-hud-preview-5-2.png)

## Kurulum

Bir Claude Code oturumunda aşağıdaki komutları çalıştırın:

**1. Adım: Marketi ekleyin**
```
/plugin marketplace add jarrodwatts/claude-hud
```

**2. Adım: Eklentiyi yükleyin**

<details>
<summary><strong>⚠️ Linux kullanıcıları: Önce buraya tıklayın</strong></summary>

Linux'ta `/tmp` genellikle ayrı bir dosya sistemidir (tmpfs), bu da kurulumun şu hatayla başarısız olmasına neden olur:
```
EXDEV: cross-device link not permitted
```

**Çözüm**: Kurulumdan önce TMPDIR'ı ayarlayın:
```bash
mkdir -p ~/.cache/tmp && TMPDIR=~/.cache/tmp claude
```

Ardından aşağıdaki kurulum komutunu o oturumda çalıştırın. Bu bir [Claude Code platform kısıtlamasıdır](https://github.com/anthropics/claude-code/issues/14799).

</details>

```
/plugin install claude-hud
```

**3. Adım: Durum çubuğunu yapılandırın**
```
/claude-hud:setup
```

Hazır! Yeni statusLine yapılandırmasını yüklemek için Claude Code'u yeniden başlatın, ardından HUD görünecektir.

---

## Claude HUD Nedir?

Claude HUD, Claude Code oturumunuzda neler olduğunu daha iyi anlamanızı sağlar.

| Gördüğünüz | Neden Önemli |
|------------|--------------|
| **Proje yolu** | Hangi projede olduğunuzu bilin (1-3 dizin seviyesi ayarlanabilir) |
| **Bağlam sağlığı** | Bağlam pencerenizin ne kadar dolu olduğunu zamanında görün |
| **Araç aktivitesi** | Claude'un dosyaları okuyup düzenlediğini anlık izleyin |
| **Ajan takibi** | Hangi alt ajanların çalıştığını ve ne yaptıklarını görün |
| **Yapılacaklar ilerleme** | Görev tamamlanmasını gerçek zamanlı takip edin |

## Ne Görürsünüz

### Varsayılan (2 satır)
```
[Opus | Max] │ my-project git:(main*)
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1s 30d / 5s)
```
- **1. Satır** — Model, plan adı (veya `Bedrock`), proje yolu, git dalı
- **2. Satır** — Bağlam çubuğu (yeşil → sarı → kırmızı) ve kullanım hız sınırları

### İsteğe bağlı satırlar (`/claude-hud:configure` ile etkinleştirin)
```
◐ Düzenle: auth.ts | ✓ Oku ×3 | ✓ Grep ×2        ← Araç aktivitesi
◐ explore [haiku]: Auth kodu bulunuyor (2d 15s)    ← Ajan durumu
▸ Kimlik doğrulama hatasını düzelt (2/5)           ← Yapılacaklar ilerleme
```

---

## Nasıl Çalışır

Claude HUD, Claude Code'un yerleşik **statusline API**'sini kullanır — ayrı pencere yok, tmux gerekmiyor, her terminalde çalışır.
```
Claude Code → stdin JSON → claude-hud → stdout → terminalinizde gösterilir
           ↘ transcript JSONL (araçlar, ajanlar, yapılacaklar)
```

**Temel özellikler:**
- Claude Code'dan gerçek token verisi (tahmin değil)
- 1M bağlam dahil Claude Code'un bildirdiği pencere boyutuna göre ölçeklenir
- Araç/ajan aktivitesi için transcript'i ayrıştırır
- Her ~300ms'de güncellenir

---

## Yapılandırma

HUD'ınızı istediğiniz zaman özelleştirin:
```
/claude-hud:configure
```

Yönlendirmeli akış düzen ve görüntüleme ayarlarını yönetir. Özel renkler ve eşik değerleri gibi gelişmiş ayarlar korunur, ancak bunları doğrudan yapılandırma dosyasını düzenleyerek ayarlarsınız:

- **İlk kurulum**: Bir ön ayar seçin (Tam/Temel/Minimal), ardından öğeleri ince ayarlayın
- **İstediğiniz zaman özelleştirin**: Öğeleri açıp kapatın, git görüntüleme stilini değiştirin
- **Kaydetmeden önce önizleyin**: Değişiklikleri uygulamadan önce nasıl görüneceğini görün

### Ön Ayarlar

| Ön Ayar | Gösterilenler |
|---------|---------------|
| **Tam** | Her şey etkin — araçlar, ajanlar, yapılacaklar, git, kullanım, süre |
| **Temel** | Aktivite satırları + git durumu, sade görünüm |
| **Minimal** | Yalnızca temel — model adı ve bağlam çubuğu |

Ön ayar seçtikten sonra öğeleri tek tek açıp kapatabilirsiniz.

### Manuel Yapılandırma

`~/.claude/plugins/claude-hud/config.json` dosyasını doğrudan düzenleyerek `colors.*`, `pathLevels` ve eşik değerleri gibi gelişmiş ayarları yapabilirsiniz. `/claude-hud:configure` çalıştırmak bu manuel ayarları korur.

### Seçenekler

| Seçenek | Tür | Varsayılan | Açıklama |
|---------|-----|------------|----------|
| `lineLayout` | string | `expanded` | Düzen: `expanded` (çok satır) veya `compact` (tek satır) |
| `pathLevels` | 1-3 | 1 | Proje yolunda gösterilecek dizin seviyesi |
| `elementOrder` | string[] | `["project","context","usage","environment","tools","agents","todos"]` | Genişletilmiş modda öğe sırası |
| `gitStatus.enabled` | boolean | true | HUD'da git dalını göster |
| `gitStatus.showDirty` | boolean | true | Kaydedilmemiş değişiklikler için `*` göster |
| `gitStatus.showAheadBehind` | boolean | false | Uzak dalın önünde/gerisinde `↑N ↓N` göster |
| `gitStatus.showFileStats` | boolean | false | Dosya değişiklik sayılarını göster `!M +A ✘D ?U` |
| `display.showModel` | boolean | true | Model adını göster `[Opus]` |
| `display.showContextBar` | boolean | true | Görsel bağlam çubuğunu göster `████░░░░░░` |
| `display.contextValue` | `percent` \| `tokens` \| `remaining` | `percent` | Bağlam görüntüleme formatı |
| `display.showConfigCounts` | boolean | false | CLAUDE.md, kural, MCP, hook sayılarını göster |
| `display.showDuration` | boolean | false | Oturum süresini göster `⏱️ 5d` |
| `display.showSpeed` | boolean | false | Çıktı token hızını göster `out: 42.1 tok/s` |
| `display.showUsage` | boolean | true | Kullanım sınırlarını göster (Pro/Max/Team) |
| `display.usageBarEnabled` | boolean | true | Kullanımı metin yerine görsel çubukla göster |
| `display.sevenDayThreshold` | 0-100 | 80 | 7 günlük kullanımı gösterme eşiği (0 = her zaman) |
| `display.showTokenBreakdown` | boolean | true | Yüksek bağlamda (85%+) token detaylarını göster |
| `display.showTools` | boolean | false | Araç aktivite satırını göster |
| `display.showAgents` | boolean | false | Ajan aktivite satırını göster |
| `display.showTodos` | boolean | false | Yapılacaklar ilerleme satırını göster |
| `display.showSessionName` | boolean | false | Oturum adını veya `/rename` ile belirlenen başlığı göster |
| `usage.cacheTtlSeconds` | number | 60 | Başarılı kullanım API yanıtının önbellekte tutulma süresi (saniye) |
| `usage.failureCacheTtlSeconds` | number | 15 | Başarısız yanıtın yeniden denenmeden önce önbellekte tutulma süresi (saniye) |
| `colors.context` | renk adı | `green` | Bağlam çubuğu ve yüzde için temel renk |
| `colors.usage` | renk adı | `brightBlue` | Kullanım çubukları ve yüzdeler için temel renk |
| `colors.warning` | renk adı | `yellow` | Bağlam eşikleri ve uyarı metinleri için uyarı rengi |
| `colors.usageWarning` | renk adı | `brightMagenta` | Eşiğe yakın kullanım çubukları için uyarı rengi |
| `colors.critical` | renk adı | `red` | Limite ulaşılan durumlar için kritik renk |

Desteklenen renk adları: `red`, `green`, `yellow`, `magenta`, `cyan`, `brightBlue`, `brightMagenta`.

### Kullanım Sınırları (Pro/Max/Team)

Kullanım gösterimi Claude Pro, Max ve Team aboneleri için **varsayılan olarak etkindir**. Bağlam çubuğunun yanında hız sınırı tüketiminizi gösterir.

7 günlük yüzde, `display.sevenDayThreshold` değerinin üzerinde olduğunda görünür (varsayılan 80):
```
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1s 30d / 5s) | ██████████ 85% (2g / 7g)
```

Devre dışı bırakmak için `display.showUsage` değerini `false` yapın.

**Gereksinimler:**
- Claude Pro, Max veya Team aboneliği (API kullanıcıları için mevcut değil)
- Claude Code'dan OAuth kimlik bilgileri (oturum açtığınızda otomatik oluşturulur)

**Sorun giderme:** Kullanım görünmüyorsa:
- Pro/Max/Team hesabıyla giriş yapıldığından emin olun (API anahtarı değil)
- Yapılandırmada `display.showUsage` değerinin `false` olmadığını kontrol edin
- API kullanıcıları kullanım göremez (token başına ödeme yapıyorlar)
- AWS Bedrock modelleri `Bedrock` gösterir ve kullanım sınırlarını gizler
- `ANTHROPIC_BASE_URL` / `ANTHROPIC_API_BASE_URL` varsayılan dışında ayarlanmışsa kullanım gösterilmez
- Proxy arkasındaysanız `HTTPS_PROXY` (veya `HTTP_PROXY`/`ALL_PROXY`) ve `NO_PROXY` ayarlayın
- Yüksek gecikmeli ortamlarda `CLAUDE_HUD_USAGE_TIMEOUT_MS` ile zaman aşımını artırın (milisaniye)

### Örnek Yapılandırma
```json
{
  "lineLayout": "expanded",
  "pathLevels": 2,
  "elementOrder": ["project", "tools", "context", "usage", "environment", "agents", "todos"],
  "gitStatus": {
    "enabled": true,
    "showDirty": true,
    "showAheadBehind": true,
    "showFileStats": true
  },
  "display": {
    "showTools": true,
    "showAgents": true,
    "showTodos": true,
    "showConfigCounts": true,
    "showDuration": true
  },
  "colors": {
    "context": "cyan",
    "usage": "cyan",
    "warning": "yellow",
    "usageWarning": "magenta",
    "critical": "red"
  },
  "usage": {
    "cacheTtlSeconds": 120,
    "failureCacheTtlSeconds": 30
  }
}
```

### Görüntüleme Örnekleri

**1 seviye (varsayılan):** `[Opus] │ my-project git:(main)`

**2 seviye:** `[Opus] │ apps/my-project git:(main)`

**3 seviye:** `[Opus] │ dev/apps/my-project git:(main)`

**Değişiklik göstergesiyle:** `[Opus] │ my-project git:(main*)`

**Önde/geride ile:** `[Opus] │ my-project git:(main ↑2 ↓1)`

**Dosya istatistikleriyle:** `[Opus] │ my-project git:(main* !3 +1 ?2)`
- `!` = değiştirilen, `+` = eklenen/hazırlanan, `✘` = silinen, `?` = takip edilmeyen
- 0 olan sayılar temiz görünüm için gösterilmez

### Sorun Giderme

**Yapılandırma uygulanmıyor mu?**
- JSON sözdizimi hatalarını kontrol edin: geçersiz JSON sessizce varsayılanlara döner
- Geçerli değerler kullandığınızdan emin olun: `pathLevels` 1-3 arası, `lineLayout` `expanded` veya `compact` olmalı
- Yapılandırmayı silin ve `/claude-hud:configure` ile yeniden oluşturun

**Git durumu görünmüyor mu?**
- Bir git deposunda olduğunuzu doğrulayın
- Yapılandırmada `gitStatus.enabled` değerinin `false` olmadığını kontrol edin

**Araç/ajan/yapılacaklar satırları görünmüyor mu?**
- Bunlar varsayılan olarak gizlidir — yapılandırmada `showTools`, `showAgents`, `showTodos` ile etkinleştirin
- Yalnızca gösterilecek aktivite olduğunda görünürler

**Kurulumdan sonra HUD görünmüyor mu?**
- Yeni statusLine yapılandırmasını yüklemek için Claude Code'u yeniden başlatın
- macOS'ta Claude Code'dan tamamen çıkıp terminalde `claude` komutunu tekrar çalıştırın

---

## Gereksinimler

- Claude Code v1.0.80+
- Node.js 18+ veya Bun

---

## Geliştirme
```bash
git clone https://github.com/jarrodwatts/claude-hud
cd claude-hud
npm ci && npm run build
npm test
```

Yönergeler için [CONTRIBUTING.md](CONTRIBUTING.md) dosyasına bakın.

---

## Lisans

MIT — [LICENSE](LICENSE) dosyasına bakın

---

## Yıldız Geçmişi

[![Yıldız Geçmişi Grafiği](https://api.star-history.com/svg?repos=jarrodwatts/claude-hud&type=Date)](https://star-history.com/#jarrodwatts/claude-hud&Date)
