---
title: Protokolování aplikací MSAL | Azure
titleSuffix: Microsoft identity platform
description: Další informace o protokolování v aplikacích Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084892"
---
# <a name="logging-in-msal-applications"></a>Protokolování v aplikacích MSAL

Aplikace Microsoft Authentication Library (MSAL) generují zprávy protokolu, které mohou pomoci diagnostikovat problémy. Aplikace můžete nakonfigurovat protokolování s několika řádky kódu a mají vlastní kontrolu nad úrovní podrobností a zda jsou zaznamenána osobní a organizační data. Doporučujeme vytvořit zpětné volání protokolování MSAL a poskytnout způsob, jak pro uživatele odeslat protokoly, když mají problémy s ověřováním.

## <a name="logging-levels"></a>Úrovně protokolování

MSAL poskytuje několik úrovní protokolování podrobností:

- Chyba: Označuje, že se něco pokazilo a byla vygenerována chyba. Slouží k ladění a identifikaci problémů.
- Upozornění: Nemusí dojít k chybě nebo selhání, ale jsou určeny pro diagnostiku a určení problémů.
- Info: MSAL bude protokolovat události určené pro informační účely, které nemusí být nutně určeny pro ladění.
- Podrobné: Výchozí. MSAL zaznamenává úplné podrobnosti o chování knihovny.

## <a name="personal-and-organizational-data"></a>Osobní a organizační údaje

Ve výchozím nastavení protokolování MSAL nezachycuje žádná vysoce citlivá osobní nebo organizační data. Knihovna poskytuje možnost povolit protokolování osobních a organizačních dat, pokud se tak rozhodnete.

Podrobnosti o protokolování msal v určitém jazyce, zvolte kartu odpovídající vašemu jazyku:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Přihlášení MSAL.NET

 > [!NOTE]
 > Ukázky MSAL.NET protokolování a další informace najdete na [wiki MSAL.NET.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

V MSAL 3.x protokolování je nastavena `.WithLogging` pro aplikaci při vytváření aplikací pomocí tvůrce modifikátoru. Tato metoda přebírá volitelné parametry:

- `Level`umožňuje rozhodnout, jakou úroveň protokolování chcete. Nastavení na chyby se zobrazí pouze chyby
- `PiiLoggingEnabled`umožňuje protokolovat osobní a organizační data, pokud je nastavena na hodnotu true. Ve výchozím nastavení je tato hodnota nastavena na hodnotu false, takže aplikace nezaznamenává osobní údaje.
- `LogCallback`je nastavena na delegáta, který provádí protokolování. Pokud `PiiLoggingEnabled` je true, tato metoda obdrží zprávy `containsPii` dvakrát: jednou s parametrem rovná false a zpráva `containsPii` bez osobních údajů, a podruhé s parametrem rovná true a zpráva může obsahovat osobní údaje. V některých případech (pokud zpráva neobsahuje osobní údaje) bude zpráva stejná.
- `DefaultLoggingEnabled`umožňuje výchozí protokolování pro platformu. Ve výchozím nastavení je to nepravdivé. Pokud ji nastavíte na hodnotu true, používá trasování událostí v aplikacích Desktop/UPWP, NSLog v systému iOS a logcat v systému Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Přihlášení do MSAL pro Android pomocí Javy

Zapněte protokolování při vytváření aplikací vytvořením zpětného volání protokolování. Zpětné volání přebírá tyto parametry:

- `tag`je řetězec předaný zpětnému volání knihovnou. Je spojena s položkou protokolu a lze ji použít k řazení zpráv protokolování.
- `logLevel`umožňuje rozhodnout, jakou úroveň protokolování chcete. Podporované úrovně protokolu `Error`jsou: `Info`, `Verbose` `Warning`, a .
- `message`je obsah položky protokolu.
- `containsPII`určuje, zda jsou protokolovány zprávy obsahující osobní údaje nebo údaje organizace. Ve výchozím nastavení je nastavena na false, takže aplikace nezaznamenává osobní údaje. Pokud `containsPII` `true`je , tato metoda obdrží zprávy `containsPII` dvakrát: `false` jednou `message` s parametrem nastaveným na `containsPii` a bez `true` osobních údajů a podruhé s parametrem nastaveným na a zpráva může obsahovat osobní údaje. V některých případech (pokud zpráva neobsahuje osobní údaje) bude zpráva stejná.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Ve výchozím nastavení protokolování MSAL nezachytí žádné identifikovatelné osobní údaje nebo identifikovatelné informace organizace.
Povolení protokolování identifikovatelných osobních údajů nebo identifikovatelných organizačních informací:

```java
Logger.getInstance().setEnablePII(true);
```

Zakázání protokolování osobních dat a dat organizace:

```java
Logger.getInstance().setEnablePII(false);
```

Ve výchozím nastavení je protokolování do logcatu zakázáno. Chcete-li povolit:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Povolte protokolování v MSAL.js (JavaScript) předáním objektu protokolování během konfigurace pro vytvoření `UserAgentApplication` instance. Tento objekt protokolování má následující vlastnosti:

- `localCallback`: instance zpětného volání, kterou může vývojář poskytnout ke spotřebě a publikování protokolů vlastním způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.
- `level`(volitelné): konfigurovatelná úroveň protokolu. Podporované úrovně protokolu `Error`jsou: `Info`, `Verbose` `Warning`, a . Výchozí formát je `Info`.
- `piiLoggingEnabled`(volitelné): pokud je nastavena na hodnotu true, protokoly osobní a organizační data. Ve výchozím nastavení je to nepravdivé, aby vaše aplikace nezaznamenávala osobní údaje. Protokoly osobních údajů se nikdy nezapisují do výchozích výstupů, jako je Console, Logcat nebo NSLog.
- `correlationId`(volitelné): jedinečný identifikátor, který se používá k mapování požadavku s odpovědí pro účely ladění. Výchozí hodnota je RFC4122 verze 4 guid (128 bitů).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL pro protokolování iOS a macOS-ObjC

Nastavte zpětné volání pro zachycení protokolování MSAL a začlenit jej do protokolování vlastní aplikace. Podpis zpětného volání vypadá takto:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Například:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Osobní údaje

Ve výchozím nastavení msal nezachycuje ani nezaznamenává žádné osobní údaje (PII). Knihovna umožňuje vývojářům aplikací zapnout prostřednictvím vlastnosti ve třídě MSALLogger. Zapnutím `pii.Enabled`aplikace přebírá odpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování regulačních požadavků.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Úrovně protokolování

Chcete-li nastavit úroveň protokolování při protokolování pomocí msal pro iOS a macOS, použijte jednu z následujících hodnot:

|Úroveň  |Popis |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat veškeré protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi klíčenky |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Například:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Protokolovat formát zprávy

Část zprávy msal protokolu zprávy je ve formátu`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Například:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování id korelace a časová razítka jsou užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediné spolehlivé místo pro jejich načtení je ze zpráv protokolování MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL pro protokolování iOS a macOS-Swift

Nastavte zpětné volání pro zachycení protokolování MSAL a začlenit jej do protokolování vlastní aplikace. Podpis (reprezentované v Objective-C) pro zpětné volání vypadá takto:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Například:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Osobní údaje

Ve výchozím nastavení msal nezachycuje ani nezaznamenává žádné osobní údaje (PII). Knihovna umožňuje vývojářům aplikací zapnout prostřednictvím vlastnosti ve třídě MSALLogger. Zapnutím `pii.Enabled`aplikace přebírá odpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování regulačních požadavků.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Úrovně protokolování

Chcete-li nastavit úroveň protokolování při protokolování pomocí msal pro iOS a macOS, použijte jednu z následujících hodnot:

|Úroveň  |Popis |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat veškeré protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi klíčenky |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Například:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Protokolovat formát zprávy

Část zprávy msal protokolu zprávy je ve formátu`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Například:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování id korelace a časová razítka jsou užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediné spolehlivé místo pro jejich načtení je ze zpráv protokolování MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL pro java protokolování

MSAL pro Java umožňuje používat knihovnu protokolování, kterou již používáte s vaší aplikací, pokud je kompatibilní s SLF4J. MSAL pro Javu používá [Simple Logging Facade pro Javu](http://www.slf4j.org/) (SLF4J) jako jednoduchou fasádu nebo abtrakci pro různé rámce protokolování, jako je [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) a [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J umožňuje uživateli připojit požadované rozhraní protokolování v době nasazení.

Chcete-li například použít logback jako architekturu protokolování ve vaší aplikaci, přidejte závislost při hlásání do souboru Maven pom pro vaši aplikaci:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Pak přidejte konfigurační soubor zpětného odpojitsení:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J se automaticky váže k odpojit v době nasazení. Protokoly MSAL budou zapsány do konzoly.

Pokyny k vytvoření vazby na jiné architektury protokolování naleznete v [příručce SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Osobní a organizační informace

Ve výchozím nastavení protokolování MSAL nezachycuje ani nezaznamenává žádná osobní nebo organizační data. V následujícím příkladu je protokolování osobních nebo organizačních dat ve výchozím nastavení vypnuto:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Zapněte protokolování osobních a `logPii()` organizačních dat nastavením tvůrce klientských aplikací. Pokud zapnete protokolování osobních nebo organizačních dat, musí vaše aplikace převzít odpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování jakýchkoli regulačních požadavků.

V následujícím příkladu je povoleno protokolování osobních nebo organizačních dat:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL pro protokolování pythonu

Protokolování v MSAL Pythonu používá standardní `logging.info("msg")` mechanismus protokolování Pythonu, například Můžete nakonfigurovat protokolování MSAL následujícím způsobem (a vidět to v akci v [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Povolit protokolování ladění pro všechny moduly

Ve výchozím nastavení je protokolování v libovolném skriptu Pythonu vypnuto. Pokud chcete povolit protokolování ladění pro všechny moduly v celém skriptu Pythonu, použijte:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Ticho pouze protokolování MSAL

Chcete-li ztlumit pouze protokolování knihovny MSAL a současně povolit protokolování ladění ve všech ostatních modulech ve skriptu Pythonu, vypněte protokolovací nástroj používaný msal pythonem:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Osobní a organizační data v Pythonu

MSAL pro Python nezaznamenává osobní data ani data organizace. Neexistuje žádná vlastnost pro zapnutí nebo vypnutí osobních nebo organizačních dat.

Standardní protokolování Pythonu můžete použít k protokolování, co chcete, ale jste zodpovědní za bezpečné zpracování citlivých dat a dodržování regulačních požadavků.

Další informace o protokolování v Pythonu najdete v [jazyce HowTO pro protokolování](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)v Pythonu .

---
