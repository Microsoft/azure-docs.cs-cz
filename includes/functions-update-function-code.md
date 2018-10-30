---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987971"
---
## <a name="update-the-function"></a>Aktualizace funkce

Ve výchozím nastavení šablona vytvoří funkci, která při zasílání požadavků vyžaduje klíč funkce. Aby bylo v Azure testování funkce snazší, budete muset aktualizovat funkci a povolit tak anonymní přístup. Způsob provedení této změny závisí na vašem jazyku projektu funkce.

### <a name="c"></a>C\#

Otevřete kódovací soubor MyHttpTrigger.cs, který je vaší novou funkcí, aktualizujte atribut **AuthorizationLevel** v definici funkce na hodnotu `anonymous` a provedené změny uložte.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Otevřete soubor nové funkce function.json, otevřete ho v textovém editoru, aktualizujte vlastnost **authLevel** v **bindings.httpTrigger** na `anonymous`a provedené změny uložte.

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
