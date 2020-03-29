---
title: Vzdálené připojení k uzlu clusteru Azure Service Fabric
description: Zjistěte, jak se vzdáleně připojit k instanci škálovací sady (uzel clusteru Service Fabric).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458317"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci škálovací sady virtuálního počítače nebo k uzlu clusteru
V clusteru Service Fabric spuštěném v Azure každý typ uzlu clusteru, který definujete, [nastaví samostatné měřítko virtuálního počítače](service-fabric-cluster-nodetypes.md).  Můžete se vzdáleně připojit ke konkrétním instancím škálovací sady (uzly clusteru).  Na rozdíl od virtuálních počítačů s jednou instancí instance škálovací sady instance nemají své vlastní virtuální IP adresy. To může být náročné, když hledáte IP adresu a port, který můžete použít ke vzdálenému připojení k určité instanci.

Chcete-li najít adresu IP a port, pomocí kterých se můžete vzdáleně připojit ke konkrétní instanci, proveďte následující kroky.

1. Získejte příchozí pravidla NAT pro protokol RDP (RDP).

    Každý typ uzlu definovaný v clusteru má obvykle vlastní virtuální IP adresu a vyhrazený nástroj pro vyrovnávání zatížení. Ve výchozím nastavení je systém vyrovnávání zatížení pro typ uzlu pojmenován v následujícím formátu: *LB-{cluster-name}-{node-type}*; například *LB-mycluster-FrontEnd*. 
    
    Na stránce pro vyrovnávání zatížení na webu Azure Portal vyberte **Nastavení** > **příchozích pravidel NAT**: 

    ![Pravidla příchozího nat účtu správce zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Následující snímek obrazovky ukazuje příchozí pravidla NAT pro typ uzlu s názvem FrontEnd: 

    ![Pravidla příchozího nat účtu správce zatížení](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Pro každý uzel se adresa IP zobrazí ve sloupci **CÍL,** sloupec **TARGET** zobrazí instanci škálovací sady a sloupec **SERVICE** zobrazí číslo portu. Pro vzdálené připojení jsou porty přiděleny každému uzlu ve vzestupném pořadí začínajícím portem 3389.

    Pravidla příchozího nasíťového prodeje najdete také v `Microsoft.Network/loadBalancers` části šablony Správce prostředků pro váš cluster.
    
2. Chcete-li potvrdit příchozí port mapování cílového portu pro uzel, můžete kliknout na jeho pravidlo a podívat se na hodnotu **cílového portu.** Následující snímek obrazovky ukazuje pravidlo příchozího NAT pro uzel **FrontEnd (instance 1)** v předchozím kroku. Všimněte si, že i když (vstupní) číslo portu je 3390, cílový port je mapován na port 3389, port pro službu RDP na cíl.  

    ![Mapování cílového portu](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Ve výchozím nastavení je pro clustery Windows cílový port port 3389, který se mapuje na službu RDP v cílovém uzlu. Pro clustery Linux je cílovým portem port 22, který se mapuje na službu Secure Shell (SSH).

3. Vzdáleně se připojujte ke konkrétnímu uzlu (instance škálovací sady). Uživatelské jméno a heslo, které jste nastavili při vytváření clusteru, můžete použít pomocí uživatelského jména a hesla nebo jiných pověření, která jste nakonfigurovali. 

    Následující snímek obrazovky ukazuje připojení k uzlu **FrontEnd (Instance 1)** v clusteru Windows pomocí připojení ke vzdálené ploše:
    
    ![Připojení ke vzdálené ploše](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Na linuxových uzlech se můžete připojit k SSH (následující příklad opakovaně používá stejnou IP adresu a port pro stručnost):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Další kroky naleznete v následujících článcích:
* Podívejte se na [přehled funkce "Nasadit kdekoli" a porovnání s clustery spravovanými azure](service-fabric-deploy-anywhere.md).
* Informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na virtuálních počítačích clusteru po nasazení
* [Změna uživatelského jména a hesla správce](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

