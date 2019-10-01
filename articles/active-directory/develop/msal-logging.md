---
title: Protokolování aplikací MSAL (Microsoft Authentication Library) | Azure
description: Přečtěte si o protokolování aplikací MSAL (Microsoft Authentication Library).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3235037d2b60322ab3e5c393c0a19b1a42bdc6c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678040"
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

## <a name="logging-in-msalnet"></a>Přihlášení MSAL.NET

 > [!NOTE]
 > Ukázky protokolování MSAL.NET a další informace najdete na [wikiwebu MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) .

V MSAL 3. x je protokolování nastaveno na aplikaci při vytváření aplikací pomocí modifikátoru tvůrce `.WithLogging`. Tato metoda přebírá volitelné parametry:

- `Level` vám umožní rozhodnout, jakou úroveň protokolování chcete. Při jejich nastavení na chyby se zobrazí jenom chyby.
- @no__t – 0 umožňuje protokolovat osobní a organizační data, pokud je nastavené na true. Ve výchozím nastavení je tato hodnota nastavena na false, aby vaše aplikace neprotokoloval osobní údaje.
- hodnota `LogCallback` je nastavena na delegáta, který provádí protokolování. Je-li hodnota `PiiLoggingEnabled` pravdivá, bude tato metoda přijímat zprávy dvakrát: jednou s parametrem `containsPii` se rovná false a zpráva bez osobních údajů a druhá s parametrem `containsPii` se rovná hodnotě true a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.
- @no__t – 0 povolí výchozí protokolování pro platformu. Ve výchozím nastavení má hodnotu false. Pokud ho nastavíte na true, používá se k trasování událostí v aplikacích Desktop/UWP NSLog v iOS a Logcat na Androidu.

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

## <a name="logging-in-msal-for-android-using-java"></a>Přihlášení MSAL pro Android pomocí Java

Zapněte při vytváření aplikace přihlášení vytvořením zpětného volání protokolování. Zpětné volání přebírá tyto parametry:

- `tag` je řetězec předaný do zpětného volání knihovnou. Je spojena s položkou protokolu a lze ji použít k řazení zpráv protokolování.
- `logLevel` vám umožní rozhodnout, jakou úroveň protokolování chcete. Podporované úrovně protokolu jsou: `Error`, `Warning`, `Info` a `Verbose`.
- `message` je obsah položky protokolu.
- `containsPII` určuje, zda budou zprávy obsahující osobní údaje nebo data organizace protokolovány. Ve výchozím nastavení je tato hodnota nastavená na false, aby vaše aplikace neprotokoloval osobní údaje. Pokud je `containsPII` `true`, bude tato metoda přijímat zprávy dvakrát: jednou s parametrem `containsPII` nastaveným na `false` a `message` bez osobních údajů a druhý čas s parametrem `containsPii` nastaveným na `true` a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.

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

## <a name="logging-in-msaljs"></a>Protokolování v MSAL. js

 Povolte protokolování v MSAL. js předáním objektu protokolovacího nástroje během konfigurace pro vytvoření instance `UserAgentApplication`. Tento objekt protokolovacího nástroje má následující vlastnosti:

- `localCallback`: instance zpětného volání, kterou může vývojář poskytnout pro využívání a publikování protokolů vlastním způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.
- `level` (volitelné): konfigurovatelná úroveň protokolu. Podporované úrovně protokolu jsou: `Error`, `Warning`, `Info` a `Verbose`. Výchozí hodnota je `Info`.
- `piiLoggingEnabled` (volitelné): Pokud je nastavena hodnota true, protokoluje osobní a organizační data. Ve výchozím nastavení je to false, aby vaše aplikace nehlásila osobní údaje. Protokoly osobních údajů se nikdy nezapisují do výchozích výstupů, jako je konzola, Logcat nebo NSLog.
- `correlationId` (volitelné): jedinečný identifikátor, který se používá k mapování požadavku na odezvu pro účely ladění. Ve výchozím nastavení se RFC4122 identifikátor GUID verze 4 (128 bitů).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “<Enter your client id>”,
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

## <a name="logging-in-msal-for-ios-and-macos"></a>Přihlášení MSAL pro iOS a macOS

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

Objective-C
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

SWIFT
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

### <a name="personal-identifiable-information-pii"></a>Osobní údaje (PII)

Ve výchozím nastavení MSAL nezachytí ani neprotokoluje žádné PII. Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím vlastnosti ve třídě MSALLogger. Když zapnete PII, aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a následujících zákonných požadavků.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

SWIFT
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Úrovně protokolování

Pokud chcete nastavit úroveň protokolování při protokolování pomocí MSAL pro iOS a macOS, použijte jednu z následujících hodnot:

|Obsah  |Popis |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat všechna protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi řetězce klíčů |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API       |

Příklad:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 SWIFT
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formát zprávy protokolu

Část zprávy protokolu MSAL je ve formátu `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`.

Příklad:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování ID korelace a časových razítek je užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediným spolehlivým místem, kde se dají načíst, je ze zpráv protokolu MSAL.
