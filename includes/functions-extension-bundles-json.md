---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381833"
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

Aktuální sada rozšíření nainstalovaných ve výchozí sadě je uvedena v tomto [souboru extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
