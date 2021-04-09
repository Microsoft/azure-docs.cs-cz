---
title: Ukládání a používání Service Fabricch tajných klíčů aplikací pro síť Azure
description: Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Zde je postup ukládání a správy tajných kódů s aplikacemi Service Fabric sítě.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625528"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabricová tajná aplikace sítě

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

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
