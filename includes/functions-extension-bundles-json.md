---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
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