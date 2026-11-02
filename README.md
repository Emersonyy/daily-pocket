Daily Pocket · 每日口袋

A native Android expense tracker for people living or studying in Japan. Record spending in Japanese yen or Chinese yuan, manage daily and weekly budgets, and optionally log expenses from a selected QQ contact’s notifications.

**Version:** 1.15 · **Version code:** 16 · **Requires:** Android 8.0 / API 26 or later

The current app interface is in Chinese. This README provides English setup and development instructions.

## Download

[Download the Android APK](releases/DailyPocket-v1.15.apk) · [SHA-256 checksum](releases/SHA256SUMS.txt) · [中文说明](README.zh-CN.md)

Transfer the APK to your Android phone and follow the installation prompts. If updating an existing installation, export a JSON backup first and install over the previous version. Do not uninstall the app or clear its data if you want to retain your local ledger.

The included APK uses the original project’s signing certificate. An APK built with a different certificate cannot replace that installation directly.

## Features

- **Notebook-style monthly ledger:** Browse daily spending and collapse the monthly fixed-expense section.
- **JPY and CNY entries:** Record yuan directly or convert yen to yuan using an available daily reference exchange rate. Each entry retains its original amount and conversion result.
- **Expense management:** Add and edit entries, attach notes, and swipe left to reveal note and delete actions.
- **Category totals:** Combine matching supermarket and meal labels within a day, and view totals by label across a week or month. Individual transactions remain available.
- **Budget tracking:** Track daily allowances, weekly extra allowances, monthly balances, and overspending.
- **Optional QQ automation:** Recognize supported amount messages from a selected notification nickname.
- **Local backups:** Export and restore the ledger as JSON.
- **Background diagnostics:** View listener status, recovery attempts, and pending currency conversions.

Fixed monthly expenses include rent, electricity, gas, phone, transport, and other bills. They contribute to total monthly spending but do not reduce the daily-spending budget.

## Budget Rules

All dates and settlement times use **Japan Standard Time (UTC+9)**.

| View | Calculation |
| --- | --- |
| Daily remaining | CNY 75 − daily spending |
| Full-week planned remaining | Weekly base allowance + CNY 150 − weekly spending |
| Effective weekly remaining | Weekly base allowance + activated extra allowance − weekly spending |
| Monthly remaining | Accrued daily allowances + allocated weekly adjustments − monthly daily spending |

A complete week has a CNY 525 base allowance. The budget-start week includes only days on or after the configured start date. For example, CNY 221.73 in weekly spending leaves **CNY 453.27 including the extra allowance**, or **CNY 303.27 before that allowance is activated**.

Activate the weekly CNY 150 allowance manually, or let it take effect at the following Monday’s 00:00 settlement. It is counted once per week. The monthly balance accrues CNY 75 only as each day arrives; future days are not included. Negative balances are shown in red.

Ordinary monthly balances reset at the start of each month. A custom cross-month rule applies when a weekly allowance is activated early in the preceding month: that month receives CNY 150, while the month containing the week’s Sunday receives a CNY 150 deduction, without a later add-back. For example, activation on September 30 gives September the credit and October a −150 opening adjustment; October 1 then adds its daily CNY 75 allowance. Other weekly allowances are allocated to the month containing Sunday.

Older versions did not retain activation dates. Existing records without that metadata keep the previous Sunday-based allocation rather than guessing a historical activation date.

## QQ Notification Logging

Enable QQ automatic logging in the app and grant Android notification access. The current target nickname is `夏日漱石`, configured in [`QqRules.java`](app/src/main/java/com/dailypocket/app/QqRules.java). To use another contact, update the matching constant and related interface text before building.

| Notification text | Interpretation |
| --- | --- |
| `288` | JPY 288 |
| `23.86rmb` | CNY 23.86 |
| `3.82人民币` | CNY 3.82 |
| `超市288` | JPY 288, labeled supermarket |
| `饭23.86rmb` | CNY 23.86, labeled meal |

The app uses notification content, not a QQ login or chat-history connection. A nickname does not verify a QQ account number. Logging from WeChat, Alipay, or other apps is not implemented.

QQ must expose readable notification text. Grouped notifications and repeated amounts may lack enough information for perfect deduplication or recovery. Background operation also depends on Android permissions and the phone manufacturer’s power-management policies; an uninterrupted connection is not guaranteed.

Yen entries awaiting an exchange rate are retained locally and excluded from totals until converted. Reference rates are updated daily, not in real time, and historical entries are not recalculated when rates change.

## Project Structure

```text
app/src/main/       Android manifest, Java source, and resources
tests/              JVM-only regression checks
build.py            APK build script; no Gradle dependency
run_tests.py        Regression-check runner
docs/               Detailed usage and background setup guides in Chinese
releases/           Installable APK and SHA-256 checksum
```

## Build from Source

Requirements: **Python 3, JDK 17, Android SDK Platform 35, and Android Build Tools 35.0.0**.

This project uses a custom command-line build script and does not include a Gradle project.

```sh
python3 build.py \
  --jdk /path/to/jdk-17 \
  --platform /path/to/android-sdk/platforms/android-35/android.jar \
  --tools /path/to/android-sdk/build-tools/35.0.0 \
  --out build/apk \
  --keystore /private/path/daily-pocket.keystore
```

On macOS, the JDK path usually ends in `Contents/Home`. The output is `build/apk/DailyPocket.apk`. The build script uses macOS/Linux executable names; Windows requires adapting the SDK executable suffixes.

If the specified keystore does not exist, the script creates a local development key with alias `daily-pocket` and password `android`. This is a development signing setup, not production key management. The original release signing key is not included. Keep private signing keys out of GitHub.

## Tests and Verification

```sh
python3 run_tests.py --jdk /path/to/jdk-17
```

Version 1.15 passed **263 pure-Java assertions**, including amount parsing, notification deduplication, category grouping, budget calculations, cross-month adjustments, gesture-direction logic, and listener-recovery policies. Its APK also passed compilation and signature verification.

These checks do not replace Android integration tests. Installation, touch interaction, QQ notification delivery, and background lifecycle behavior have not been verified on a physical device or emulator in the development environment.

## Data and Privacy

The ledger and notification-processing diagnostics are stored locally on the phone. Exchange-rate requests use currency and date information, not expense amounts or notes. Uninstalling the app or clearing its data deletes the local ledger. Importing a JSON backup replaces the existing ledger.

The prepared project folder excludes personal ledgers, exported notification content, chat screenshots, private signing keys, and the local SDK.

## Documentation

- [Detailed usage and version history — Chinese](docs/使用说明.md)
- [Background logging setup and testing — Chinese](docs/后台记账启用与测试.md)

## License

No open-source license has been selected for this project. Public repository access alone does not grant permission to reuse, modify, or redistribute the code. The project owner can add a license when ready.

