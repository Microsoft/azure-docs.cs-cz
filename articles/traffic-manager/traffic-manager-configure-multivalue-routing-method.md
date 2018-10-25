---
title: Konfigurace metody směrování s více hodnotami provozu ve službě Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vysvětluje postup konfigurace Traffic Manageru směrovat provoz do koncových bodů A/AAAA.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: d9a0342332c2483b859e916b50fd05c9cb1e3395
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986759"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurace metody směrování s více hodnotami v Traffic Manageru

Tento článek popisuje, jak konfigurovat metodu směrování provozu více hodnotami. **Hodnot** provoz metodu směrování, která umožňuje vrátit několik koncových bodů v pořádku a zvyšuje spolehlivost aplikace, protože klienti mají další možnosti to chcete zkusit znovu, aniž by bylo nutné provést další vyhledávání DNS. Routing vícehodnotový je povolená jenom pro profily, které mají všechny své koncové body určené pomocí adresy IPv4 nebo IPv6. Po přijetí dotaz pro tento profil všechny koncové body v dobrém stavu se vrací na základě konfigurovatelných maximální návratový počet zadaných. 

>[!NOTE]
> V tuto chvíli přidat koncové body pomocí adresy IPv4 nebo IPv6 se podporuje jenom pro koncové body typu **externí** a proto routing vícehodnotový je také podporována pouze pro tyto koncové body.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pro profil Traffic Manageru.
1. V levém podokně webu Azure portal, vyberte **skupiny prostředků**.
2. V **skupiny prostředků**, nahoře na stránce vybrat **přidat**.
3. V **název skupiny prostředků**, zadejte název *myResourceGroupTM1*. Pro **umístění skupiny prostředků**vyberte **USA – východ**a pak vyberte **OK**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, která přesměruje uživatelský provoz tak, že je pošlete na koncový bod s nejnižší latenci.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V **vytvořit profil Traffic Manageru**, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **vytvořit**:
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte **hodnot** metodu směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte *myResourceGroupTM1*. |
    | Umístění                | Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |
  
    ![Vytvoření profilu Traffic Manageru](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dvě IP adresy jako externí koncové body do profilu služby Traffic Manager více hodnot, který jste vytvořili v předchozím kroku.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Externí koncový bod                                   |
    | Název           | myEndpoint1                                        |
    | Plně kvalifikovaný název domény (FQDN) nebo IP adresa           | Zadejte veřejnou IP adresu koncového bodu, který chcete přidat k tomuto profilu Traffic Manageru                         |
    |        |           |

4. Opakujte kroky 2 a 3 a přidat jiný koncový bod s názvem *myEndpoint2*, pro **plně kvalifikovaný název domény (FQDN) nebo IP adresa**, zadejte veřejnou IP adresu na druhém koncový bod.
5.  Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Další postup

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metody směrování podle výkonu](traffic-manager-configure-performance-routing-method.md)
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).


