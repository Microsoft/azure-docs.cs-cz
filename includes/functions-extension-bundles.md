---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010447"
---
Nejjednodušší způsob, jak nainstalovat rozšíření vazby, je povolit [sady rozšíření](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Pokud povolíte sady, automaticky se nainstaluje předdefinovaná sada balíčků rozšíření.

Pokud chcete povolit sady rozšíření, otevřete host.jsv souboru a aktualizujte jeho obsah tak, aby odpovídal následujícímu kódu:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```