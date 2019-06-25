---
title: Ukládání a používání Azure Service Fabric mřížky tajných klíčů aplikací | Dokumentace Microsoftu
description: Ukládání a používání služby prostředků infrastruktury sítě tajných kódů.
services: service-fabric-mesh
keywords: Tajné kódy
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60810646"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric mřížky tajných klíčů aplikací
Sítě pro Service Fabric podporuje tajné kódy jako prostředky Azure. Tajný klíč služby prostředků infrastruktury sítě může být libovolné citlivé textové informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které mají být uloženy a bezpečně přenášet.

![Přehled sítě tajných kódů][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Síť tajné kódy prostředků
Aplikace sítě tajný kód se skládá ze:
* A **tajných kódů** prostředků, což je kontejner, který uchovává tajné kódy text. Tajné kódy obsažené **tajných kódů** prostředků jsou uloženy a bezpečně přenášet.
* Jeden nebo více **tajné klíče/hodnoty** prostředky, které jsou uloženy v **tajných kódů** kontejner prostředků. Každý **tajné klíče/hodnoty** prostředků se liší podle čísla verze.

## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric mřížky tajné kódy, naleznete v tématu:
- [Správa tajných klíčů aplikací Service Fabric sítě](service-fabric-mesh-howto-manage-secrets.md)
- [Úvod do modelu prostředků služby Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
