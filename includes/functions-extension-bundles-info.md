---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201938"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Během spuštění hostitel stáhne a nainstaluje [rozšíření vazby úložiště](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) a další rozšíření vazby společnosti Microsoft. K této instalaci dochází, protože rozšíření vazby jsou ve výchozím nastavení povolena v souboru *host.json* s následujícími vlastnostmi:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Pokud narazíte na chyby související s rozšířenívazby, zkontrolujte, zda výše uvedené vlastnosti jsou k dispozici v *host.json*.
::: zone-end  