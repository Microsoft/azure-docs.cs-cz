---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
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

V nástroji jsou k dispozici následující vlastnosti `extensionBundle` :

| Vlastnost | Popis |
| -------- | ----------- |
| id | Obor názvů pro sady rozšíření funkcí Microsoft Azure Functions. |
| verze | Verze sady, která se má nainstalovat Modul runtime Functions vždy vybere maximální přípustnou verzi definovanou rozsahem verze nebo intervalem. Výše uvedená hodnota verze umožňuje všechny verze balíčku od 1.0.0 do až, ale ne včetně 2.0.0. Další informace najdete v části [interval zápisu pro určení rozsahů verzí](/nuget/reference/package-versioning#version-ranges). |

Verze sady prostředků se zvýší jako balíčky v rámci změny sady prostředků. K velkým změnám verze dojde, když se balíčky v sadě zvýší o hlavní verzi. Změny hlavní verze v sadě se obvykle shodují se změnou hlavní verze běhového modulu Functions.  

Aktuální sada rozšíření, která je nainstalovaná ve výchozím svazku, se zobrazí v tomto [extensions.jsv souboru](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
