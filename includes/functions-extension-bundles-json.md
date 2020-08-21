---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689543"
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