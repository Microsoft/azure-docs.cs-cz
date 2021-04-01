---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201938"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Během spouštění hostitel stáhne a nainstaluje [rozšíření vazby úložiště](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) a další rozšíření Microsoft Binding Extensions. K této instalaci dochází, protože rozšíření vazby jsou ve výchozím nastavení povolena v *host.jsv* souboru s následujícími vlastnostmi:
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
> Pokud narazíte na chyby související s rozšířeními vazby, ověřte, že jsou v *host.jsk* dispozici výše uvedené vlastnosti.
::: zone-end  