---
ms.openlocfilehash: 0b0d2ead4a10d037b09c2a532eb21372ffd0cb82
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64512146"
---
Chcete-li odkazovat na Azure Functions 2.x výchozí vazby, otevřete *host.json* obsah souboru a aktualizovat tak, aby odpovídala následující kód.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```