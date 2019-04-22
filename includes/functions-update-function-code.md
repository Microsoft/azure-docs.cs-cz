---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59736985"
---
## <a name="update-the-function"></a>Aktualizace funkce

Ve výchozím nastavení šablona vytvoří funkci, která při zasílání požadavků vyžaduje klíč funkce. Aby bylo v Azure testování funkce snazší, budete muset aktualizovat funkci a povolit tak anonymní přístup. Způsob provedení této změny závisí na vašem jazyku projektu funkce.

### <a name="c"></a>C\#

Otevřete kódovací soubor MyHttpTrigger.cs, který je vaší novou funkcí, aktualizujte atribut **AuthorizationLevel** v definici funkce na hodnotu `Anonymous` a provedené změny uložte.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

V textovém editoru otevřete soubor function.json nové funkce, aktualizujte **authLevel** vlastnost **vazby** k `anonymous`a uložte provedené změny.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Nyní můžete funkci v Azure vyvolat, aniž byste museli zadat klíč funkce. Klíč funkce není při místním spuštění potřeba.
