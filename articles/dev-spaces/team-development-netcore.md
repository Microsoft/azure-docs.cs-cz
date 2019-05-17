---
title: Vývoj v týmu Azure Dev prostory pomocí .NET Core a VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s '
ms.openlocfilehash: 608f95234af68bad0b3c961baf61d3476d2126ea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765150"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Změně kódu
Přejděte do okna nástroje VS Code pro `mywebapi` a kód upravit, pokud chcete `string Get(int id)` metoda ve `Controllers/ValuesController.cs`, například:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
