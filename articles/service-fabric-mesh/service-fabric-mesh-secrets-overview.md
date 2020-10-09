---
title: Ukládání a používání Service Fabricch tajných klíčů aplikací pro síť Azure
description: Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Zde je postup ukládání a správy tajných kódů s aplikacemi Service Fabric sítě.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76277626"
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
