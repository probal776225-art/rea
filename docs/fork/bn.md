# REA: বাংলা কুইক স্টার্ট

এই গাইডটি [Pradyumna Chakraborty Probal](https://github.com/probal776225-art)-এর
REA ফর্কের জন্য লেখা। মূল REA তৈরি করেছেন [morluto](https://github.com/morluto);
অফিসিয়াল প্রজেক্ট হলো [morluto/rea](https://github.com/morluto/rea)। লাইসেন্স MIT,
এবং মূল কপিরাইট নোটিশ অপরিবর্তিত রাখা হয়েছে। ইংরেজিতে বিস্তারিত:
[ফর্ক নোট](README.md)।

## REA কী করে

REA আপনার AI এজেন্টকে (যেমন Claude Code) রিভার্স ইঞ্জিনিয়ারিং টুল দেয়। সোর্স কোড
ছাড়াই কোনো অ্যাপ বা প্রোগ্রাম কীভাবে কাজ করে তা খুঁজে বের করতে পারে, এবং প্রতিটি
সিদ্ধান্তের পেছনের প্রমাণ (Evidence) ও সীমাবদ্ধতা দেখায়।

- **কোনো অতিরিক্ত টুল ছাড়াই:** JavaScript/Electron অ্যাপ, .NET, Go বাইনারি, মডেল ফাইল,
  ডেস্কটপ অ্যাপের ফোল্ডার।
- **Ghidra, Hopper বা IDA লাগে:** নেটিভ বাইনারি (pseudocode, assembly, strings, calls)।

শুধু নিজের সফটওয়্যার, অথবা যেটি বিশ্লেষণের অনুমতি আপনার আছে, সেটিই পরীক্ষা করুন।
বিস্তারিত: [DISCLAIMER](https://github.com/probal776225-art/rea/blob/main/DISCLAIMER.md)।

## ১. Node.js ঠিক করুন (macOS)

REA চলে Node.js 22.x (>=22.23.4), 24.x (>=24.22.1) অথবা 26.12.1+ এ।
Node.js 25 সমর্থিত নয়। আপনার বর্তমান Node.js না সরিয়েই Homebrew দিয়ে Node.js 24
পাশে ইনস্টল করুন:

```bash
brew install node@24
export PATH="$(brew --prefix node@24)/bin:$PATH"
node --version
```

`export` লাইনটি শুধু ওই টার্মিনাল উইন্ডোতে কাজ করে। নতুন উইন্ডো খুললে আবার চালান।

## ২. REA ইনস্টল ও এজেন্টে যুক্ত করুন

একই টার্মিনালে:

```bash
npm install --global rea-agents
rea setup
```

`rea setup` কোন এজেন্টে যুক্ত করবে জিজ্ঞেস করবে, পরিবর্তনগুলো দেখাবে, তারপর
অনুমতি চাইবে। এটি যে Node.js-এ চলছে (এখানে 24) সেটিই এজেন্টের কনফিগারেশনে লেখে,
তাই পরে ডিফল্ট Node.js 25 থাকলেও সমস্যা হয় না। শেষে এজেন্টটি রিস্টার্ট করুন।

এই ফর্কটি সোর্স থেকে বিল্ড করতে চাইলে [ফর্ক নোটের](README.md#build-and-install-from-source-on-macos)
ধাপগুলো দেখুন।

## ৩. Ghidra সেটআপ (Apple Silicon Mac)

নেটিভ বাইনারি বিশ্লেষণের জন্য Ghidra বিনামূল্যের এবং ওপেন-সোর্স।

1. JDK 21 ইনস্টল করুন:

   ```bash
   brew install openjdk@21
   export JAVA_HOME="$(brew --prefix openjdk@21)/libexec/openjdk.jdk/Contents/Home"
   ```

2. [Ghidra রিলিজ পেজ](https://github.com/NationalSecurityAgency/ghidra/releases) থেকে
   Ghidra 12.1.x (যেমন 12.1.4) ডাউনলোড করে আনজিপ করুন, এবং রিলিজ নোটের SHA-256 মিলিয়ে নিন।
3. অফিসিয়াল রিলিজে Apple Silicon-এর decompiler থাকে না, তাই একবার বিল্ড করুন
   (Xcode বা Command Line Tools লাগবে):

   ```bash
   cd /path/to/ghidra_12.1.4_PUBLIC/support/gradle
   ./gradlew --no-daemon buildNatives
   ```

4. REA-কে Ghidra-র ঠিকানা দিন এবং পরীক্ষা করুন:

   ```bash
   export GHIDRA_INSTALL_DIR=/path/to/ghidra_12.1.4_PUBLIC
   rea doctor --provider ghidra --json
   rea setup
   ```

   `rea setup` যাচাই করা Ghidra পাথ এজেন্টের কনফিগারেশনে সংরক্ষণ করে।

## ৪. ব্যবহার

এজেন্টকে সাধারণ ভাষায় বলুন, যেমন:

```text
Notes অ্যাপে সার্চ কীভাবে কাজ করে বুঝিয়ে দাও, প্রমাণ দেখাও, এবং আমার প্রজেক্টে
একই রকম ফিচার বানাও।
```

টার্মিনাল থেকে একটি JavaScript/Electron অ্যাপ বিশ্লেষণ:

```bash
rea analyze-javascript-application /absolute/path/to/app --json
```

## ৫. আপডেট

```bash
export PATH="$(brew --prefix node@24)/bin:$PATH"
rea update
```

আপডেটের পর যে `rea setup` কমান্ডটি দেখায় সেটি চালিয়ে এজেন্ট রিস্টার্ট করুন।
`rea update` অফিসিয়াল npm রিলিজ ইনস্টল করে।

## সমস্যা হলে

- **“Install Node.js 22.x … and rerun setup”:** আপনি অসমর্থিত Node.js-এ চালাচ্ছেন।
  ধাপ ১-এর `export` লাইনটি চালিয়ে আবার চেষ্টা করুন।
- **এজেন্টে REA-র টুল দেখা যাচ্ছে না:** এজেন্ট পুরোপুরি বন্ধ করে আবার খুলুন, তারপর
  `rea doctor --json` চালান।
- **Ghidra “not_configured”:** `GHIDRA_INSTALL_DIR` সেট করে `rea setup` আবার চালান।
- এই ফর্কের সমস্যা জানাতে: [issues](https://github.com/probal776225-art/rea/issues)।
  মূল REA-র বাগ: [morluto/rea issues](https://github.com/morluto/rea/issues)।
