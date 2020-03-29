---
title: Ukládání a používání tajných klíčů sítě Azure Service Fabric
description: Service Fabric Mesh podporuje tajné kódy jako prostředky Azure. Tady je postup, jak ukládat a spravovat tajné klíče pomocí aplikací Service Fabric Mesh.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277626"
---
# <a name="service-fabric-mesh-application-secrets"></a>Tajné klíče aplikací Service Fabric Mesh
Service Fabric Mesh podporuje tajné kódy jako prostředky Azure. Tajný klíč sítě Service Fabric může být jakékoli citlivé textové informace, jako jsou připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by měly být uloženy a přenášeny bezpečně.

![Přehled tajných kódů sítě][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Prostředky tajných kódů sítě
Tajný klíč síťové sítě se skládá z:
* A **Secrets** prostředek, což je kontejner, který ukládá tajné klíče textu. Tajné klíče obsažené v prostředku **tajné klíče** jsou uloženy a přenášeny bezpečně.
* Jeden nebo více prostředků **tajných kódů a hodnot,** které jsou uloženy v kontejneru prostředků **tajné klíče.** Každý prostředek **Tajné klíče a hodnoty** se vyznačuje číslem verze.

## <a name="next-steps"></a>Další kroky 
Další informace o tajných službách Service Fabric Mesh Secrets naleznete v tématu:
- [Správa tajných klíčů síťových aplikací service fabric](service-fabric-mesh-howto-manage-secrets.md)
- [Úvod do modelu prostředků prostředků prostředků infrastruktury služeb](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
