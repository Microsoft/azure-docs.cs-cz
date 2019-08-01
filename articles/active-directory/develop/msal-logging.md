---
title: Protokolování v aplikacích MSAL | Azure
description: Přečtěte si o protokolování aplikací MSAL (Microsoft Authentication Library).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4288ff4aba216a214d10c56ba448fc03e13b81f2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693931"
---
# <a name="logging"></a>Protokolování
Aplikace Microsoft Authentication Library (MSAL) umožňují generovat zprávy protokolu, které mohou pomáhat diagnostikovat problémy a poskytnout podrobné informace. Aplikace může nakonfigurovat protokolování s několika řádky kódu a mít vlastní kontrolu nad úrovní podrobností a bez ohledu na to, jestli se protokolují osobní údaje a data organizace. Doporučuje se nastavit zpětné volání protokolování MSAL a poskytnout uživatelům možnost odesílat protokoly, pokud mají problémy s ověřováním.

## <a name="logging-levels"></a>Úrovně protokolování

Protokolovací nástroj MSAL umožňuje zachytit několik úrovní podrobností:

- Chyba: Indikuje, že došlo k chybě, a vygenerovala se chyba. Slouží k ladění a identifikaci problémů.
- Upozornění: Události, které jsou otázky a aplikace, vyžadují více informací. V některých případech došlo k chybě nebo selhání, ale je určené pro diagnostiku a řešení problémů.
- Příjemce MSAL bude protokolovat události určené pro informativní účely, které nejsou nutně určené pro ladění.
- Podrobné Default (Výchozí). MSAL budou protokolovat velké množství informací a poskytnou úplné podrobnosti o chování knihovny.

## <a name="personal-and-organizational-data"></a>Osobní a organizační data
Protokolovací nástroj MSAL ve výchozím nastavení nezachycuje žádná citlivá osobní nebo organizační data. Knihovna poskytuje možnost povolit protokolování osobních a organizačních dat, pokud se rozhodnete k tomu.

## <a name="logging-in-msalnet"></a>Přihlášení MSAL.NET
V MSAL 3. x je protokolování nastaveno na aplikaci při vytváření aplikací pomocí `.WithLogging` modifikátoru tvůrce. Tato metoda přebírá volitelné parametry:

- *Úroveň* vám umožní rozhodnout, jakou úroveň protokolování chcete. Při jejich nastavení na chyby se zobrazí jenom chyby.
- *PiiLoggingEnabled* umožňuje protokolovat osobní a organizační data, pokud je nastavené na true. Ve výchozím nastavení je tato hodnota nastavena na false, aby vaše aplikace neprotokoloval osobní údaje.
- *LogCallback* je nastaven na delegáta, který provádí protokolování. Pokud má *PiiLoggingEnabled* hodnotu true, bude tato metoda přijímat zprávy dvakrát: jednou s parametrem *containsPii* se rovná false a zpráva bez osobních údajů a druhá s parametrem *containsPii* se rovná hodnotě true a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.
- *DefaultLoggingEnabled* umožňuje výchozí protokolování pro platformu. Ve výchozím nastavení má hodnotu false. Pokud ho nastavíte na true, používá se k trasování událostí v aplikacích Desktop/UWP NSLog v iOS a Logcat na Androidu.

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

 ## <a name="logging-in-msaljs"></a>Protokolování v MSAL. js

 Můžete povolit protokolování v MSAL. js předáním objektu protokolovacího nástroje během konfigurace pro vytvoření `UserAgentApplication` instance. Tento objekt protokolovacího nástroje má následující vlastnosti:

- *localCallback*: instance zpětného volání, kterou může vývojář poskytnout pro využívání a publikování protokolů vlastním způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.

- *úroveň* (volitelné): konfigurovatelná úroveň protokolu. Podporované úrovně protokolu jsou: Chyba, upozornění, informace, podrobný výpis. Výchozí hodnota je info.

- *piiLoggingEnabled* (volitelné): umožňuje protokolovat osobní a organizační data, pokud je nastavené na true. Ve výchozím nastavení je tato hodnota nastavená na false, aby vaše aplikace neprotokoloval osobní údaje. Protokoly osobních údajů se nikdy nezapisují do výchozích výstupů, jako je konzola, Logcat nebo NSLog. Výchozí nastavení je false.

- *ID korelace* (volitelné): jedinečný identifikátor, který slouží k mapování požadavku na odezvu pro účely ladění. Ve výchozím nastavení se RFC4122 identifikátor GUID verze 4 (128 bitů).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
