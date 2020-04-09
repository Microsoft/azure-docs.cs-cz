---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878237"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Následující vlastnosti jsou `extensionBundle`k dispozici v :

| Vlastnost | Popis |
| -------- | ----------- |
| id | Obor názvů pro rozšíření Microsoft Azure Functions. |
| version | Verze balíčku k instalaci. Zaběhu Funkce vždy vybere maximální přípustnou verzi definovanou rozsahem verze nebo intervalem. Výše uvedená hodnota verze umožňuje všechny verze balíčku od 1.0.0 do, ale bez hodnoty 2.0.0. Další informace naleznete v [zápisu intervalu pro určení rozsahů verzí](/nuget/reference/package-versioning#version-ranges). |

Přírůstek verzí balíčků jako balíčky v balíčku se mění. Změny hlavních verzí nastanou, když balíčky v přírůstek svazku hlavní verze. Hlavní změny verzí v balíčku se obvykle shodují se změnou hlavní verze runtime Funkce.  

Aktuální sada rozšíření nainstalovaných ve výchozí sadě je uvedena v tomto [souboru extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
