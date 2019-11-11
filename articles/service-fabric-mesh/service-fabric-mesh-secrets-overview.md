---
title: Ukládání a používání Service Fabricch tajných klíčů aplikací pro síť Azure Microsoft Docs
description: Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Zde je postup ukládání a správy tajných kódů s aplikacemi Service Fabric sítě.
services: service-fabric-mesh
keywords: záleží
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72188517c237b170b709c48f16d3c131985f95d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686235"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabricová tajná aplikace sítě
Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Service Fabric se může jednat o informace o citlivých textech, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by se měly bezpečně ukládat a přenášet.

![Přehled tajných klíčů][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Prostředky pro tajné klíče sítě
Tajný kód aplikace sítě se skládá z těchto součástí:
* Prostředek **tajných** kódů, což je kontejner, který ukládá text tajných kódů. Tajné kódy obsažené v prostředku **tajných** kódů se ukládají a bezpečně odesílají.
* Jeden nebo více prostředků **tajných klíčů a hodnot** , které jsou uloženy v kontejneru prostředků **tajných** kódů. Jednotlivé zdroje **tajných kódů a hodnot** jsou rozlišené číslem verze.

## <a name="next-steps"></a>Další kroky 
Další informace o Service Fabricch tajných klíčů najdete v tématech:
- [Správa tajných klíčů pro aplikace Service Fabric sítě](service-fabric-mesh-howto-manage-secrets.md)
- [Seznámení s modelem Service Fabric prostředků](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
