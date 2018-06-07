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
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 28424f9a7a0f77882ee3360c5599549303075c18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642569"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci sady škálování virtuálního počítače nebo uzlu clusteru
V Service Fabric clusteru běží v Azure, každý typ uzlu clusteru, který definujete [nastaví samostatné škálování virtuálních počítačů](service-fabric-cluster-nodetypes.md).  Vám může vzdálené připojení k instancím sadu konkrétní škálování (uzly).  Na rozdíl od jedné instance virtuálních počítačů instancí sady škálování nemají své vlastní virtuální IP adresy. To může být náročné, když hledáte adresu IP a port, který můžete použít vzdálené připojení ke konkrétní instanci.

Pokud chcete vyhledat adresu IP a port, který můžete použít vzdálené připojení ke konkrétní instanci, proveďte následující kroky.

1. Získáte příchozí pravidla NAT pro protokol RDP (Remote Desktop).

    Každý typ uzlu definována v clusteru má obvykle své vlastní virtuální IP adresy a nástroj pro vyrovnávání zatížení vyhrazené. Ve výchozím nastavení, nástroj pro vyrovnávání zatížení pro typ uzlu jmenuje v následujícím formátu: *LB-{název clusteru}-{typ uzlu}*, například *LB-mycluster front-end*. 
    
    Na stránce pro nástroj pro vyrovnávání zatížení na portálu Azure, vyberte možnost **nastavení** > **pravidla příchozí NAT**: 

    ![Pravidla pro příchozí NAT nástroje pro vyrovnávání zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Následující snímek obrazovky ukazuje příchozích pravidel NAT pro typ uzlu s názvem front-endu: 

    ![Pravidla pro příchozí NAT nástroje pro vyrovnávání zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Pro každý uzel, IP adresa se zobrazí v **cílové** sloupec, **cíl** sloupec poskytne instanci sady škálování a **služby** sloupec poskytuje číslo portu. Pro připojení ke vzdálené porty jsou přiděleny do každého uzlu ve vzestupném pořadí od portu 3389.

    Můžete také vyhledat příchozí NAT pravidla v `Microsoft.Network/loadBalancers` část šablony Resource Manageru pro váš cluster.
    
2. Potvrďte portu pro příchozí spojení na cílový port mapování pro uzel, klikněte na jeho pravidla a podívejte se na **cíle port** hodnotu. Následující snímek obrazovky ukazuje příchozí pravidlo NAT pro **front-endu (Instance 1)** uzlu v předchozím kroku. Všimněte si, že 3390 sice číslo portu (příchozí) cílový port je namapovaná na port 3389, port pro službu protokolu RDP na cílový.  

    ![Cílový port mapování](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Ve výchozím nastavení pro Windows clusterů cílový port je port 3389, která se mapuje na službu protokolu RDP na cílovém uzlu. Pro Linux clusterů cílový port je port 22, který se mapuje na službu Secure Shell (SSH).

3. Vzdáleně připojit k uzlu konkrétní (škálování nastavte instance). Můžete použít uživatelské jméno a heslo, které jste nastavili při vytváření clusteru nebo jiné pověření, které jste nakonfigurovali. 

    Následující snímek obrazovky ukazuje použití připojení ke vzdálené ploše pro připojení k **front-endu (Instance 1)** uzlu v clusteru se systémem Windows:
    
    ![Připojení ke vzdálené ploše](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Na Linux se můžete připojit pomocí protokolu SSH (následující příklad opětovně používá stejnou IP adresu a port pro jako stručný výtah):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Další kroky přečtěte si následující články:
* Najdete v článku [Přehled funkce "Nasazení odkudkoli" a porovnání s Azure spravované clustery](service-fabric-deploy-anywhere.md).
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Aktualizujte hodnoty rozsahu portu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) v clusteru virtuálních počítačů po nasazení
* [Změňte uživatelské jméno správce a heslo](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro cluster virtuálních počítačů

