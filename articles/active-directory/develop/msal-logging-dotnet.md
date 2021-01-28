---
title: Protokolování chyb a výjimek v MSAL.NET
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954830"
---
# <a name="logging-in-msalnet"></a>Protokolování v MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Konfigurace protokolování v MSAL.NET

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

> [!TIP]
 > Ukázky protokolování MSAL.NET a další informace najdete na [wikiwebu MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) .

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).