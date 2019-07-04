---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442278"
---
Nejjednodušší způsob, jak nainstalovat rozšíření vazby je umožnit [rozšíření sady](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Povolení sady předdefinovanou sadu rozšíření se automaticky nainstalované balíčky.

Chcete-li povolit rozšíření sady, otevřete *host.json* souborů a aktualizovat jeho obsah tak, aby odpovídala následující kód:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```