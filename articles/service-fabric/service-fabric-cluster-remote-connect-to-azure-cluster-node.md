---
title: Vzdálené připojení k uzlu clusteru Azure Service Fabric
description: Naučte se vzdáleně připojit k instanci sady škálování (Service Fabric uzel clusteru).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268090"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci sady škálování virtuálního počítače nebo uzlu clusteru
V clusteru Service Fabric spuštěném v Azure každý typ uzlu clusteru, který definujete, [Nastaví samostatné škálování virtuálního počítače](service-fabric-cluster-nodetypes.md).  Můžete se vzdáleně připojit ke konkrétním instancím sady škálování (uzly clusteru).  Na rozdíl od virtuálních počítačů s jednou instancí nemají instance sady škálování vlastní virtuální IP adresy. To může být náročné, pokud hledáte IP adresu a port, které můžete použít ke vzdálenému připojení k určité instanci.

Pokud chcete najít IP adresu a port, které můžete použít ke vzdálenému připojení k určité instanci, proveďte následující kroky.

1. Získat pravidla příchozího překladu adres (NAT) pro protokol RDP (Remote Desktop Protocol) (RDP)

    Každý typ uzlu definovaný v clusteru má obvykle vlastní virtuální IP adresu a vyhrazený Nástroj pro vyrovnávání zatížení. Ve výchozím nastavení má nástroj pro vyrovnávání zatížení pro typ uzlu název v následujícím formátu: *9,1-{cluster-Name}-{Node-Type}*; například: *9,1-mycluster-front-endu*. 
    
    Na stránce nástroje pro vyrovnávání zatížení v Azure Portal vyberte **Nastavení**  >  **pravidla příchozího překladu adres (NAT)**: 

    ![Snímek obrazovky se stránkou nástroje pro vyrovnávání zatížení v Azure Portal. V nabídce vlevo vyberte v části nastavení možnost pravidla příchozího překladu adres (NAT).](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Na následujícím snímku obrazovky vidíte příchozí pravidla překladu adres (NAT) pro typ uzlu s názvem front-end: 

    ![Snímek obrazovky zobrazující pravidla příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení. Pro každé pravidlo je uveden název, verze protokolu IP, cíl, cíl a služba.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Pro každý uzel se IP adresa zobrazí ve sloupci **cíl** , **cílový** sloupec vytvoří instanci sady škálování a sloupec **služby** poskytne číslo portu. Pro vzdálené připojení se porty přiřazují každému uzlu ve vzestupném pořadí počínaje portem 3389.

    Pravidla příchozího překladu adres (NAT) najdete taky v `Microsoft.Network/loadBalancers` části šablony Správce prostředků pro svůj cluster.
    
2. Pokud chcete potvrdit příchozí port pro mapování portu pro uzel, můžete kliknout na jeho pravidlo a podívat se na hodnotu **cílového portu** . Následující snímek obrazovky ukazuje pravidlo příchozího překladu adres (NAT) pro uzel **front-end (instance 1)** v předchozím kroku. Všimněte si, že i když je číslo portu (příchozí) 3390, je cílový port namapován na port 3389 a port pro službu RDP v cíli.  

    ![Mapování cílového portu](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Ve výchozím nastavení jsou u clusterů Windows cílový port port 3389, který se mapuje na službu RDP na cílovém uzlu. U clusterů se systémem Linux je cílovým portem port 22, který se mapuje na službu Secure Shell (SSH).

3. Vzdáleně se připojte ke konkrétnímu uzlu (instance sady škálování). Můžete použít uživatelské jméno a heslo, které jste nastavili při vytváření clusteru nebo jakýchkoli jiných přihlašovacích údajů, které jste nakonfigurovali. 

    Následující snímek obrazovky ukazuje použití Připojení ke vzdálené ploše pro připojení k uzlu **front-end (instance 1)** v clusteru Windows:
    
    ![Připojení ke vzdálené ploše](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    V uzlech se systémem Linux se můžete připojit pomocí SSH (Následující příklad znovu používá stejnou IP adresu a port pro zkrácení):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Další postup najdete v následujících článcích:
* Podívejte se na [Přehled funkce nasazení kamkoli a porovnání s clustery spravovanými Azure](service-fabric-deploy-anywhere.md).
* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na virtuálních počítačích clusteru po nasazení
* [Změna uživatelského jména a hesla správce](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

