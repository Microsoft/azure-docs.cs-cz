---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
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

V objektu `extensionBundle` jsou k dispozici následující vlastnosti:

| Vlastnost | Popis |
| -------- | ----------- |
| id | Obor názvů pro sady rozšíření Microsoft Azure Functions |
| version | Verze sady, která se má nainstalovat. Modul runtime služby Functions vždy vybere maximální přípustnou verzi definovanou rozsahem nebo intervalem verzí. Výše uvedená hodnota verze povoluje všechny verze sady od 1.0.0 až po, ale ne včetně 2.0.0. Další informace najdete v části věnované [zápisu intervalů pro zadávání rozsahů verzí](/nuget/reference/package-versioning#version-ranges). |

Verze sady se zvyšují s ohledem na změny balíčků v sadě. Ke změnám hlavní verze dochází, když se zvýší hlavní verze balíčků v sadě. Změny hlavní verze sady se obvykle časově shodují se změnami hlavní verze modulu runtime služby Functions.  

Aktuální seznam rozšíření, která instaluje výchozí sada, najdete v tomto [souboru extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
