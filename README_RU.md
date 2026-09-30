# RMX3830 Hotspot Enhancer v0.3.1

GitHub Actions-ready проект диагностического LSPosed-модуля для realme C51 RMX3830 / Android 15.

## Что это

v0.3.1 — **диагностический Modern Xposed API 102 модуль**. Он пока не меняет настройки hotspot. Его задача — на реальном Android 15 собрать точные сведения о доступных методах:

- `android.net.wifi.SoftApConfiguration`
- `android.net.wifi.SoftApConfiguration$Builder`
- `com.android.server.wifi.SoftApManager`
- Realme/Android Settings-классы tethering

Модуль логирует вызовы подходящих методов в `logcat` и не подменяет их результат.

## Почему исправлена архитектура

Используется Modern Xposed API 102:

- `META-INF/xposed/java_init.list`
- `META-INF/xposed/module.prop`
- `META-INF/xposed/scope.list`
- `XposedModule`
- `onSystemServerStarting()` для system_server
- `onPackageReady()` для Settings
- `XposedInterface.Hooker`

Это соответствует современной схеме libxposed/LSPosed.

## Сборка через GitHub Actions

На твоём 32-битном Windows ничего из Android SDK для сборки не требуется.

1. Создай новый GitHub repository.
2. Загрузи содержимое этого ZIP в repository.
3. Убедись, что `.github/workflows/build.yml` тоже загружен.
4. Открой вкладку **Actions**.
5. Выбери **Build RMX3830 Hotspot Enhancer**.
6. Нажми **Run workflow**.
7. После успешной сборки открой job и скачай artifact:
   `RMX3830-Hotspot-Enhancer-v0.3.1-debug`

GitHub runner сам использует Linux + JDK 17 + Android SDK 35 + Gradle 8.7.

## Установка APK

Это **LSPosed APK**, а не Magisk ZIP.

Установи APK как обычное приложение, затем открой LSPosed Manager и включи модуль для scopes:

- System Framework / `android`
- Settings / `com.android.settings`

После перезагрузки или перезапуска соответствующих процессов смотри:

```sh
su
logcat -d -s RMX3830Hotspot:I '*:S'
```

Также можно:

```sh
logcat -c
logcat -s RMX3830Hotspot:I '*:S'
```

## Ожидаемый результат

Нас интересуют строки вида:

```text
RMX3830Hotspot: FOUND android.net.wifi.SoftApConfiguration
RMX3830Hotspot: FOUND android.net.wifi.SoftApConfiguration$Builder
RMX3830Hotspot: FOUND com.android.server.wifi.SoftApManager
RMX3830Hotspot: METHOD ...
RMX3830Hotspot: HOOKED ... count=...
```

После этого по реальным методам RMX3830 можно делать следующий этап — v0.4 с функциональным изменением Soft AP, а не гадать по AOSP API.

## Важно

v0.3.1 намеренно ничего не меняет в hotspot. Это безопасный этап разведки перед функциональными Hook.
