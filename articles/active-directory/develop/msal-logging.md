---
title: Protokolování v aplikacích MSAL | Azure
description: Další informace o protokolování v aplikacích Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544102"
---
# <a name="logging"></a>Protokolování
Aplikace Microsoft Authentication Library (MSAL) k vygenerování zprávy protokolu, které vám mohou pomoci diagnostikovat problémy a uveďte podrobnosti. Aplikace můžete nakonfigurovat protokolování pomocí pár řádků kódu a vlastní řídit úroveň podrobností a určuje, jestli se protokolují tato data osobních a podnikových. Doporučujeme nastavit protokolování MSAL zpětné volání a umožňují uživatelům odesílat protokoly, pokud jsou máte problémy s ověřováním.

## <a name="logging-levels"></a>Úroveň protokolování

Protokolovací nástroj pro MSAL umožňuje několik úrovní podrobností bude zachycení:

- Chyba: Označuje, něco se pokazilo a byla vygenerována chyba. Používá se pro ladění a identifikovat problémy.
- Upozornění: Události, které jsou otázku a aplikace potřebuje na další informace. Existuje ještě nutně byly chybě nebo selhání, ale určená pro diagnostiku a přesné určení problémy.
- Informace: Knihovna MSAL se budou protokolovat události určené k informačním účelům, ne tedy nutně určený pro ladění.
- verbose: Default (Výchozí). Knihovna MSAL protokolovat velké množství informací a uvádí úplné informace o jaké chování knihovny.

## <a name="personal-and-organizational-data"></a>Osobních a podnikových dat
Ve výchozím nastavení protokolovač MSAL nezachytává vysoce citlivých údajů osobní nebo organizace. Knihovna poskytuje možnost Povolit protokolování osobních a podnikových dat, pokud se rozhodnete tak učinit.

## <a name="logging-in-msalnet"></a>Protokolování v MSAL.NET
V MSAL 3.x protokolování je nastaveno na aplikaci na vytvoření aplikace s použitím `.WithLogging` modifikátor Tvůrce. Tato metoda přebírá volitelné parametry:

- *Úroveň* umožňuje rozhodnout, jaké úroveň protokolování. Nastavení na chyby bude pouze dojde k chybám
- *PiiLoggingEnabled* umožňuje protokolu osobních a podnikových dat v případě nastavenou na hodnotu true. Ve výchozím nastavení to je nastavena na hodnotu false, tak, aby vaše aplikace neprotokoluje osobní údaje.
- *LogCallback* je nastavena na delegáta, která provádí protokolování. Pokud *PiiLoggingEnabled* má hodnotu true, tato metoda bude přijímat zprávy dvakrát: jednou pro *containsPii* rovná parametru hodnotu false a zprávu bez osobních údajů a podruhé s *containsPii* rovná parametru na hodnotu true a zpráva může obsahovat osobní údaje. V některých případech (když zpráva neobsahuje osobní údaje) zprávy budou stejné.
- *DefaultLoggingEnabled* umožňuje je výchozí protokolování pro platformu. Ve výchozím nastavení je false. Pokud nastavíte na hodnotu true, je používá trasování událostí v Desktopu/UPW aplikacích NSLog v Iosu a logcat v Androidu.

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
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Protokolování v MSAL.js

 Můžete povolit přihlášení MSAL.js předáním objektu protokolovací nástroj během konfigurace pro vytvoření `UserAgentApplication` instance. Tento objekt protokolovací nástroj má následující vlastnosti:

- *localCallback*: instance zpětného volání, můžete poskytuje vývojářům zpracovávejte a publikujte protokoly vlastní způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.

- *úroveň* (volitelné): úroveň konfigurovat protokolování. Podporovaný protokol úrovně jsou: Chyba, upozornění, informace, Verbose. Výchozí hodnota je informace.

- *piiLoggingEnabled* (volitelné): umožňuje protokolu osobních a podnikových dat v případě nastavenou na hodnotu true. Ve výchozím nastavení to je nastavena na hodnotu false, aby aplikace neprotokoluje osobní údaje. Protokoly osobních údajů se nikdy zapisují do výchozí výstupů, jako je konzola, Logcat nebo NSLog. Výchozí je nastavena na hodnotu false.

- *ID korelace* (volitelné): Jedinečný identifikátor, slouží k mapování žádosti s odpovědí pro účely ladění. Výchozí hodnota je identifikátor guid RFC4122 verze 4 (128 bitů).

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
