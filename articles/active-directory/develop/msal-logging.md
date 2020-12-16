---
title: Přihlášení k MSAL aplikacím | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o protokolování aplikací MSAL (Microsoft Authentication Library).
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
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: b53a12db9203121d12a69c10aaa81bceab5c1754
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584251"
---
# <a name="logging-in-msal-applications"></a>Protokolování aplikací MSAL

Aplikace Microsoft Authentication Library (MSAL) generují zprávy protokolu, které mohou pomáhat diagnostikovat problémy. Aplikace může nakonfigurovat protokolování s několika řádky kódu a mít vlastní kontrolu nad úrovní podrobností a bez ohledu na to, jestli se protokolují osobní údaje a data organizace. Doporučujeme vytvořit MSAL zpětného volání protokolování a poskytnout uživatelům možnost Odeslat protokoly, když budou mít problémy s ověřením.

## <a name="logging-levels"></a>Úrovně protokolování

MSAL poskytuje několik úrovní podrobností protokolování:

- Chyba: indikuje, že došlo k chybě, a vygenerovala se chyba. Slouží k ladění a identifikaci problémů.
- Upozornění: došlo k chybě nebo selhání nutně, ale jsou určené pro diagnostiku a problémy.
- Informace: MSAL bude protokolovat události určené pro informativní účely, které nejsou nutně určené pro ladění.
- Verbose: výchozí. MSAL zaznamená všechny podrobnosti o chování knihovny.

## <a name="personal-and-organizational-data"></a>Osobní a organizační data

Protokolovací nástroj MSAL ve výchozím nastavení nezachytí žádná citlivá osobní nebo organizační data. Knihovna nabízí možnost povolit protokolování osobních a organizačních dat, pokud se rozhodnete tak učinit.

Podrobnosti o MSAL protokolování v konkrétním jazyce získáte výběrem karty odpovídajícího vašemu jazyku:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Protokolování v MSAL.NET

 > [!NOTE]
 > Ukázky protokolování MSAL.NET a další informace najdete na [wikiwebu MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) .

V MSAL 3. x je protokolování nastaveno na aplikaci při vytváření aplikací pomocí `.WithLogging` modifikátoru tvůrce. Tato metoda přebírá volitelné parametry:

- `Level` umožňuje rozhodnout, jakou úroveň protokolování chcete. Při jejich nastavení na chyby se zobrazí jenom chyby.
- `PiiLoggingEnabled` umožňuje protokolovat osobní a organizační data, pokud je nastaveno na hodnotu true. Ve výchozím nastavení je tato hodnota nastavena na false, aby vaše aplikace neprotokoloval osobní údaje.
- `LogCallback` je nastaven na delegáta, který provede protokolování. Pokud `PiiLoggingEnabled` je hodnota true, bude tato metoda přijímat zprávy dvakrát: jednou s `containsPii` parametrem se rovná false a zpráva bez osobních údajů a druhá s `containsPii` parametrem se rovná hodnotě true a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.
- `DefaultLoggingEnabled` povolí výchozí protokolování pro platformu. Ve výchozím nastavení má hodnotu false. Pokud ho nastavíte na true, používá se k trasování událostí v aplikacích Desktop/UWP NSLog v iOS a Logcat na Androidu.

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

## <a name="logging-in-msal-for-android-using-java"></a>Přihlášení MSAL pro Android pomocí Java

Zapněte při vytváření aplikace přihlášení vytvořením zpětného volání protokolování. Zpětné volání přebírá tyto parametry:

- `tag` je řetězec předaný do zpětného volání knihovny. Je spojena s položkou protokolu a lze ji použít k řazení zpráv protokolování.
- `logLevel` umožňuje rozhodnout, jakou úroveň protokolování chcete. Podporované úrovně protokolu jsou: `Error` , `Warning` , a `Info` `Verbose` .
- `message` je obsah položky protokolu.
- `containsPII` Určuje, jestli se mají protokolovat zprávy obsahující osobní údaje nebo data organizace. Ve výchozím nastavení je tato hodnota nastavená na false, aby vaše aplikace neprotokoloval osobní údaje. `containsPII`V případě `true` , tato metoda obdrží zprávy dvakrát: jednou s `containsPII` parametrem nastaveným na `false` a `message` bez osobních údajů a podruhé s `containsPii` parametrem nastaveným na `true` a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.

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

Protokolovací nástroj MSAL ve výchozím nastavení nezachytí žádné osobní údaje ani organizační údaje, které by se vám mohly identifikovat.
Postup povolení protokolování osobních identifikovatelných informací nebo organizačních informací:

```java
Logger.getInstance().setEnablePII(true);
```

Postup zakázání protokolování osobních údajů a dat organizace:

```java
Logger.getInstance().setEnablePII(false);
```

Ve výchozím nastavení je protokolování do Logcat zakázané. Povolení:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 Povolit protokolování v MSAL.js (JavaScript) předáním objektu protokolovacího nástroje během konfigurace pro vytvoření `UserAgentApplication` instance. Tento objekt protokolovacího nástroje má následující vlastnosti:

- `localCallback`: instance zpětného volání, kterou může vývojář poskytnout pro využívání a publikování protokolů vlastním způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.
- `level` (volitelné): konfigurovatelná úroveň protokolu. Podporované úrovně protokolu jsou: `Error` , `Warning` , a `Info` `Verbose` . Výchozí formát je `Info`.
- `piiLoggingEnabled` (volitelné): Pokud je nastavené na true, protokoluje osobní a organizační data. Ve výchozím nastavení je to false, aby vaše aplikace nehlásila osobní údaje. Protokoly osobních údajů se nikdy nezapisují do výchozích výstupů, jako je konzola, Logcat nebo NSLog.
- `correlationId` (volitelné): jedinečný identifikátor, který slouží k mapování požadavku na odezvu pro účely ladění. Ve výchozím nastavení se RFC4122 identifikátor GUID verze 4 (128 bitů).

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
            loggerCallback , {
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

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL pro iOS a macOS protokolování – ObjC

Nastavte zpětné volání pro zachycení protokolování MSAL a zahrňte ho do protokolování vlastní aplikace. Signatura zpětného volání vypadá takto:

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

Příklad:

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

Ve výchozím nastavení MSAL nezachytí ani neprotokoluje žádná osobní data (PII). Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím vlastnosti ve třídě MSALLogger. Když zapnete `pii.Enabled` , aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a následujících zákonných požadavků.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Úrovně protokolování

Pokud chcete nastavit úroveň protokolování při protokolování pomocí MSAL pro iOS a macOS, použijte jednu z následujících hodnot:

|Level  |Popis |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat všechna protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi řetězce klíčů |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Příklad:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formát zprávy protokolu

Část zprávy protokolu MSAL je ve formátu `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Příklad:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování ID korelace a časových razítek je užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediným spolehlivým místem, kde se dají načíst, je ze zpráv protokolu MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL pro iOS a protokolování macOS – SWIFT

Nastavte zpětné volání pro zachycení protokolování MSAL a zahrňte ho do protokolování vlastní aplikace. Signatura (reprezentovaná v cíli-C) pro zpětné volání vypadá takto:

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

Příklad:

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

Ve výchozím nastavení MSAL nezachytí ani neprotokoluje žádná osobní data (PII). Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím vlastnosti ve třídě MSALLogger. Když zapnete `pii.Enabled` , aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a následujících zákonných požadavků.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Úrovně protokolování

Pokud chcete nastavit úroveň protokolování při protokolování pomocí MSAL pro iOS a macOS, použijte jednu z následujících hodnot:

|Level  |Popis |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat všechna protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi řetězce klíčů |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Příklad:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formát zprávy protokolu

Část zprávy protokolu MSAL je ve formátu `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Příklad:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování ID korelace a časových razítek je užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediným spolehlivým místem, kde se dají načíst, je ze zpráv protokolu MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL pro protokolování Java

MSAL for Java umožňuje používat knihovnu protokolování, kterou už používáte s vaší aplikací, pokud je kompatibilní s SLF4J. MSAL for Java používá [jednoduché protokolování průčelí pro Java](http://www.slf4j.org/) (SLF4J) jako jednoduchou fasádu nebo abstrakci pro různá protokolovací rozhraní, jako je [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) a [log4j](https://logging.apache.org/log4j/2.x/). SLF4J umožňuje uživateli připojit se k požadovanému rozhraní protokolování v době nasazení.

Například pro použití Logback jako protokolovacího rozhraní v aplikaci přidejte závislost Logback do souboru Maven pom pro vaši aplikaci:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Pak přidejte konfigurační soubor Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J se automaticky váže k Logback v době nasazení. Protokoly MSAL se zapíší do konzoly.

Pokyny k vytvoření vazby na další protokolovací rozhraní naleznete v [příručce SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Osobní údaje a informace o organizaci

Ve výchozím nastavení protokolování MSAL nezachycuje ani neprotokoluje žádná osobní nebo organizační data. V následujícím příkladu je protokolování osobních nebo organizačních dat ve výchozím nastavení vypnuté:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Zapněte možnost protokolování osobních a organizačních dat podle nastavení `logPii()` v Tvůrci klientských aplikací. Pokud zapnete protokolování osobních nebo organizačních dat, vaše aplikace musí převzít zodpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování zákonných požadavků.

V následujícím příkladu je povoleno protokolování osobních nebo organizačních dat:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL pro protokolování v Pythonu

Přihlášení v MSAL Pythonu používá standardní mechanismus protokolování Pythonu, například `logging.info("msg")` můžete nakonfigurovat protokolování MSAL následujícím způsobem (a zobrazit ho v akci v [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Povolit protokolování ladění pro všechny moduly

Ve výchozím nastavení je protokolování v jakémkoli skriptu Pythonu vypnuté. Pokud chcete povolit protokolování ladění pro všechny moduly v celém skriptu Pythonu, použijte:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Protokolování pouze tichého MSAL

Chcete-li pouze tiché protokolování MSAL knihovny a povolit protokolování ladění ve všech ostatních modulech skriptu Python, vypněte protokolovací nástroj používaný MSAL Pythonem:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Osobní a organizační data v Pythonu

MSAL for Python neprotokoluje osobní data ani organizační data. Neexistuje žádná vlastnost pro zapnutí nebo vypnutí protokolování osobních nebo organizačních dat.

Pomocí standardního protokolování Pythonu se můžete přihlásit cokoli, co potřebujete, ale zodpovídáte za bezpečné zpracování citlivých dat a následujících zákonných požadavků.

Další informace o protokolování v Pythonu najdete v tématu věnovaném  [protokolování](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)v Pythonu.

---
