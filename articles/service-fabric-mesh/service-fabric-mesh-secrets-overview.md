---
title: Ukládání a používání Service Fabricch tajných klíčů aplikací pro síť Azure Microsoft Docs
description: Ukládání a používání Service Fabricch tajných klíčů.
services: service-fabric-mesh
keywords: záleží
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: b5cfa93298222d914069b6ab63deb8ba8a9b59c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875345"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabricová tajná aplikace sítě
Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Service Fabric se může jednat o informace o citlivých textech, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by se měly bezpečně ukládat a přenášet.

![Přehled tajných klíčů][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Prostředky pro tajné klíče sítě
Tajný kód aplikace sítě se skládá z těchto součástí:
* Prostředek **tajných** kódů, což je kontejner, který ukládá text tajných kódů. Tajné kódy obsažené v  prostředku tajných kódů se ukládají a bezpečně odesílají.
* Jeden nebo více prostředků **tajných klíčů a hodnot** , které jsou uloženy v kontejneru prostředků **tajných** kódů. Jednotlivé zdroje tajných kódů **a hodnot** jsou rozlišené číslem verze.

## <a name="next-steps"></a>Další kroky 
Další informace o Service Fabricch tajných klíčů najdete v tématech:
- [Správa tajných klíčů pro aplikace Service Fabric sítě](service-fabric-mesh-howto-manage-secrets.md)
- [Seznámení s modelem Service Fabric prostředků](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
