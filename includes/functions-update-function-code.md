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
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964105"
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
