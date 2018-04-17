---
title: Vzdálené připojení k uzlu clusteru Azure Service Fabric | Microsoft Docs
description: Naučte se vzdáleně připojit k instanci sady škálování (Service Fabric uzel clusteru).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 68e3b8ae5bdaa3ad9f1c470294ef5c3bcf0c1893
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci sady škálování virtuálního počítače nebo uzlu clusteru
V Service Fabric clusteru běží v Azure, každý typ uzlu clusteru, který definujete [nastaví samostatné škálování virtuálních počítačů](service-fabric-cluster-nodetypes.md).  Vám může připojit vzdálené konkrétní škálování sadu instancí (nebo uzlech clusteru).  Na rozdíl od jedné instance virtuálních počítačů instancí sady škálování nemají své vlastní virtuální IP adresy. To může být náročné, když hledáte adresu IP a port, který můžete použít vzdálené připojení ke konkrétní instanci.

Pokud chcete vyhledat adresu IP a port, který můžete použít vzdálené připojení ke konkrétní instanci, proveďte následující kroky.

1. Najít virtuální IP adresy pro typ uzlu získáním příchozích pravidel NAT pro protokol RDP (Remote Desktop).

    Nejdřív získat hodnoty příchozích pravidel NAT, které byly definované jako součást definice prostředků pro `Microsoft.Network/loadBalancers`.
    
    Na portálu Azure, na stránce nástroje pro vyrovnávání zatížení vyberte **nastavení** > **pravidla příchozí NAT**. To vám dává IP adresu a port, který můžete použít se vzdáleně připojit k první měřítka nastavit instanci. 
    
    ![Nástroj pro vyrovnávání zatížení][LBBlade]
    
    Na následujícím obrázku, IP adresa a port jsou **104.42.106.156** a **3389**.
    
    ![Pravidla NAT][NATRules]

2. Najít port, který můžete použít se vzdáleně připojit k instanci sady konkrétní škálování nebo uzel.

    Škálování nastavit instancí mapy do uzlů. Pomocí informací o sadě škálování určete přesnou portu, který chcete použít.
    
    Porty jsou přiděleny ve vzestupném pořadí, který odpovídá instance sady škálování. Starší například typ uzlu front-endu následující tabulka uvádí porty pro jednotlivé instance pět uzlů. Použít stejná mapování k vaší instanci sady škálování.
    
    | **Instance sady škálování virtuálního počítače** | **Port** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Vzdáleně připojte k instanci sady konkrétní škálování.

    Následující obrázek ukazuje, připojte se k instanci sady škálování FrontEnd_1 pomocí připojení ke vzdálené ploše:
    
    ![Připojení ke vzdálené ploše][RDP]


Další kroky přečtěte si následující články:
* Najdete v článku [Přehled funkce "Nasazení odkudkoli" a porovnání s Azure spravované clustery](service-fabric-deploy-anywhere.md).
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Aktualizujte hodnoty rozsahu portu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) v clusteru virtuálních počítačů po nasazení
* [Změňte uživatelské jméno správce a heslo](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro cluster virtuálních počítačů

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
