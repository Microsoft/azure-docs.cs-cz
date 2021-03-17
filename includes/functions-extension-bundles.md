---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
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