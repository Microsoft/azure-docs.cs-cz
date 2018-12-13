---
title: Ukládání a používání Azure Service Fabric mřížky tajných klíčů aplikací | Dokumentace Microsoftu
description: Ukládání a používání služby prostředků infrastruktury sítě tajných kódů.
services: service-fabric-mesh
keywords: tajné kódy
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893496"
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
