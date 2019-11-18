---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129065"
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

V `extensionBundle`jsou k dispozici následující vlastnosti:

| Vlastnost | Popis |
| -------- | ----------- |
| id | Obor názvů pro sady rozšíření funkcí Microsoft Azure Functions. |
| version | Verze sady, která se má nainstalovat Modul runtime Functions vždy vybere maximální přípustnou verzi definovanou rozsahem verze nebo intervalem. Výše uvedená hodnota verze umožňuje všechny verze balíčku od 1.0.0 do až, ale ne včetně 2.0.0. Další informace najdete v části [interval zápisu pro určení rozsahů verzí](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Verze sady prostředků se zvýší jako balíčky v rámci změny sady prostředků. K velkým změnám verze dojde, když se balíčky v sadě zvýší o hlavní verzi. Změny hlavní verze v sadě se obvykle shodují se změnou hlavní verze běhového modulu Functions.  

Aktuální sada rozšíření nainstalovaná ve výchozím svazku je v tomto [souboru Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
