---
title: Ukládání a používání Azure Service Fabric mřížky tajných klíčů aplikací | Dokumentace Microsoftu
description: Ukládání a používání služby prostředků infrastruktury sítě tajných kódů.
services: service-fabric-mesh
keywords: tajné kódy
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: b6db9b6cb2ccbf1c8d29edb817d35677109ae755
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768617"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric mřížky tajných klíčů aplikací
Sítě pro Service Fabric podporuje tajné kódy jako prostředky Azure. Tajný klíč služby prostředků infrastruktury sítě může být libovolné citlivé textové informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které mají být uloženy a bezpečně přenášet.

![Přehled sítě tajných kódů][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Síť tajné kódy prostředků
Aplikace sítě tajný kód se skládá ze:
* A **tajných kódů** prostředků, což je kontejner, který uchovává tajné kódy text. Tajné kódy obsažené **tajných kódů** prostředků jsou uloženy a bezpečně přenášet.
* Jeden nebo více **tajné klíče/hodnoty** prostředky, které jsou uloženy v **tajných kódů** kontejner prostředků. Každý **tajné klíče/hodnoty** prostředků se liší podle čísla verze.

## <a name="inline-or-stored-in-azure-key-vault"></a>Vložené nebo uložené v Azure Key Vault
- Aplikace sítě a prostředky služeb zahrnují Identity spravované služby (MSI) s Azure Active Directory (AAD) aby bylo možné přístup k tajným klíčům ve službě Azure Key Vault.
- Tajné klíče a certifikáty se nedají automaticky vrátí selhání se zásadami.

## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric mřížky tajné kódy, naleznete v tématu:
- [Správa tajných klíčů aplikací Service Fabric sítě](service-fabric-mesh-howto-manage-secrets.md)
- [Úvod do modelu prostředků služby Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
