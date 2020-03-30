---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68639133"
---
Nejjednodušší způsob, jak nainstalovat rozšíření vazby je povolit [rozšíření svazky](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Pokud povolíte balíčky, automaticky se nainstaluje předdefinovaná sada balíčků rozšíření.

Chcete-li povolit balíčky rozšíření, otevřete soubor host.json a aktualizujte jeho obsah tak, aby odpovídal následujícímu kódu:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```