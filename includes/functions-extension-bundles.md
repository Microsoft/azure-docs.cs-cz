---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "68639133"
---
Nejjednodušší způsob, jak nainstalovat rozšíření vazby, je povolit [sady rozšíření](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Pokud povolíte sady, automaticky se nainstaluje předdefinovaná sada balíčků rozšíření.

Pokud chcete povolit sady rozšíření, otevřete soubor host. JSON a aktualizujte jeho obsah tak, aby odpovídal následujícímu kódu:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```