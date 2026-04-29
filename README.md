```markdown
# 📦 Signed-AAB-Builder

## 🔍 প্রজেক্ট পরিচিতি
এটি একটি GitHub Actions Workflow যা যেকোনো APK ফাইলকে Signed AAB (Android App Bundle) এ convert করে। কোনো Android Studio বা local machine দরকার নেই — শুধু GitHub Actions এ Manual trigger করলেই হয়।

---

## ⚙️ কিভাবে কাজ করে

### ধাপ ১: APK Download
- User একটি Direct Download URL দেয়
- curl দিয়ে APK download হয়
- Valid APK কিনা verify করা হয়

### ধাপ ২: Proto Format Convert
- GitHub Actions এ pre-installed `aapt2` ব্যবহার করে
- APK কে Proto format এ convert করে (aapt2 convert --output-format proto)
- কারণ: bundletool শুধু proto format বোঝে, binary format না

### ধাপ ৩: Module Structure তৈরি
bundletool এর জন্য নির্দিষ্ট folder structure তৈরি করা হয়:
```
module/
├── manifest/
│   └── AndroidManifest.xml
├── dex/
│   ├── classes.dex
│   ├── classes2.dex
│   └── classes3.dex
├── res/
├── lib/
├── assets/
├── root/
│   ├── kotlin/
│   └── META-INF/services/
└── resources.pb

```

### ধাপ ৪: AAB Build
- module folder থেকে ZIP তৈরি
- bundletool দিয়ে unsigned AAB তৈরি

### ধাপ ৫: Sign
- jarsigner দিয়ে AAB সাইন করা হয়
- Keystore file repository তে রাখা আছে

### ধাপ ৬: Upload
- Signed AAB artifact হিসেবে upload হয়
- GitHub Actions থেকে download করা যায়

---

## 📁 Repository Structure
```
Signed-AAB-Builder/
├── .github/
│   └── workflows/
│       └── build-aab.yml      ← Main workflow file
├── keystore.p12               ← Keystore file (signing এর জন্য)
└── README.md
```

---

## 🔑 Keystore Auto-Detection
Workflow নিজেই keystore খোঁজে এই অগ্রাধিকারে:
1. `keystore.p12`
2. `release-key.jks`
3. যেকোনো `*.jks` ফাইল
4. যেকোনো `*.p12` ফাইল
5. না পেলে error দিয়ে বন্ধ হয়

---

## ▶️ কিভাবে ব্যবহার করবে
1. GitHub → Actions → "Build Signed AAB (Easy Manual Trigger)"
2. "Run workflow" button click করো
3. নিচের তথ্য দাও:

| Field | বিবরণ | উদাহরণ |
|-------|--------|---------|
| `apk_url` | APK এর Direct Download Link | `https://files.catbox.moe/xxxxx.apk` |
| `keystore_password` | Keystore এর password | `Acs@12345` |
| `key_alias` | Key এর alias নাম | `key0` |
| `key_password` | Key এর password | `Acs@12345` |

4. Run শেষে **Artifacts** থেকে `signed-aab-file` download করো
5. ZIP খুললে `signed-release.aab` পাবে

---

## ✅ Supported APK Types
- সাধারণ সব APK ✅
- Kotlin/Java APK ✅
- Multiple DEX APK ✅
- Native Library (lib/) সহ APK ✅
- যেকোনো size এর APK ✅

## ❌ Unsupported APK Types
- Protected APK (DexGuard, Bangcle) ❌
- Split APK / XAPK ❌
- System APK ❌

---

## 🔗 Direct Link Format
```

✅ https://github.com/user/repo/raw/main/app.apk
✅ https://files.catbox.moe/xxxxx.apk
✅ https://pixeldrain.com/api/file/XXXXXXXX
❌ Google Drive sharing link
❌ APKPure / APKMirror page link
```

---

## 🛠️ ব্যবহৃত Tools
| Tool | কাজ | Version |
|------|-----|---------|
| `aapt2` | APK → Proto convert | Pre-installed (Android SDK) |
| `bundletool` | AAB তৈরি | 1.15.6 |
| `jarsigner` | AAB সাইন | Pre-installed (Java) |
| `curl` | APK download | Pre-installed |

---

## ⏱️ কতক্ষণ লাগে
মোট সময়: **30-40 সেকেন্ড**
| Step | সময় |
|------|------|
| Setup | ~2s |
| Install Tools | ~10s |
| Download APK | ~2s |
| Build + Sign | ~15s |
| Upload | ~3s |

---

## 🔮 ভবিষ্যতে যা যোগ করা যেতে পারে
- [ ] Google Drive link auto-handle
- [ ] Multiple APK একসাথে process
- [ ] Telegram bot দিয়ে trigger
- [ ] Output AAB সরাসরি Telegram এ পাঠানো
- [ ] APK info (package name, version) দেখানো

---

## 👤 Owner
- GitHub: [@airstudent2](https://github.com/airstudent2)
- Repository: [Signed-AAB-Builder](https://github.com/airstudent2/Signed-AAB-Builder)
```

এটা README.md হিসেবে repository তে add করলে যেকোনো AI বা মানুষ প্রজেক্টটা সম্পূর্ণ বুঝতে পারবে এবং পরবর্তীতে update করতে পারবে। 🎉
