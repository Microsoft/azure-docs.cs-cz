---
title: Vzdálené připojení k uzlu clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak se vzdáleně připojit k instanci škálovací sady (uzel clusteru Service Fabric).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110919"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci virtuálního počítače škálovací sady nebo uzel clusteru.
V Service Fabric cluster běží v Azure, každý typ uzlu clusteru, který definujete [nastaví samostatné škálovací sadu virtuálních počítačů](service-fabric-cluster-nodetypes.md).  Vám může vzdálené připojení k instancím konkrétní škálovací sady (uzlech clusteru).  Na rozdíl od virtuálních počítačů s jednou instancí instancí škálovací sady nemají své vlastní virtuální IP adresy. To může být náročné, pokud chcete pro IP adresu a port, který můžete použít pro vzdálené připojení ke konkrétní instanci.

Pokud chcete zjistit IP adresu a port, který můžete použít pro vzdálené připojení ke konkrétní instanci, proveďte následující kroky.

1. Získejte příchozích pravidel NAT pro protokol RDP (Remote Desktop).

    Každý typ uzlu definována ve vašem clusteru má obvykle své vlastní virtuální IP adresa a nástroj pro vyrovnávání zatížení vyhrazenou. Nástroje pro vyrovnávání zatížení pro typ uzlu je ve výchozím nastavení název v následujícím formátu: *LB-{název clusteru} – {typ uzlu}*; například *LB-clusteru FrontEnd*. 
    
    Na stránce pro nástroj pro vyrovnávání zatížení na webu Azure portal vyberte **nastavení** > **pravidla příchozího překladu adres**: 

    ![Pravidla příchozího překladu adres nástroje pro vyrovnávání zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Následující snímek obrazovky ukazuje příchozích pravidel NAT pro typ uzlu s názvem front-endu: 

    ![Pravidla příchozího překladu adres nástroje pro vyrovnávání zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Pro každý uzel, IP adresa bude zobrazovat v **cílové** sloupci **cílové** sloupec obsahuje instance ve škálovací sadě a **služby** sloupec obsahuje číslo portu. Pro připojení ke vzdálené porty jsou přiděleny k jednotlivým uzlům ve vzestupném pořadí od port 3389.

    Pravidla příchozího překladu adres v můžete také vyhledat `Microsoft.Network/loadBalancers` šablony Resource Manageru pro váš cluster.
    
2. Pokud chcete potvrdit portu pro příchozí spojení na cílový port mapování pro uzel, klikněte na jeho pravidlo a podívejte se na **cílit na port** hodnotu. Na následujícím snímku obrazovky vidíte příchozí pravidlo NAT pro **front-endu (Instance 1)** uzlu v předchozím kroku. Všimněte si, že 3390 sice číslo portu (příchozí) cílový port mapují na port 3389, port pro službu protokolu RDP na cíl.  

    ![Cílový port mapování](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Ve výchozím nastavení v případě clusterů Windows cílový port je port 3389, který se mapuje na službu RDP na cílový uzel. Pro clustery s Linuxem cílový port je port 22, který se mapuje na služby Secure Shell (SSH).

3. Vzdálené připojení ke konkrétní uzel (instance škálovací sady). Můžete použít uživatelské jméno a heslo, které jste nastavili při vytváření clusteru nebo žádné jiné přihlašovací údaje, které jste nakonfigurovali. 

    Následující snímek obrazovky ukazuje použití pro připojení k připojení ke vzdálené ploše **front-endu (Instance 1)** uzlu v clusteru s Windows:
    
    ![Připojení ke vzdálené ploše](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    V uzlech systému Linux můžete připojit pomocí protokolu SSH (následující příklad opakovaně používá stejnou IP adresu a port pro zkrácení):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Další kroky v následujících článcích:
* Zobrazit [Přehled funkce "Nasazení kdekoli" a porovnání s využitím clusterů Azure managed](service-fabric-deploy-anywhere.md).
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) v clusteru virtuální počítače po nasazení
* [Změnit uživatelské jméno admin a heslo](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

