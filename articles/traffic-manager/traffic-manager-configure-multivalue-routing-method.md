---
title: Konfigurace pro vícehodnotové směrování provozu – Azure Traffic Manager
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager pro směrování provozu do koncových bodů A/AAAA.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994923"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurace metody směrování s více hodnotami v Traffic Manager

Tento článek popisuje, jak nakonfigurovat metodu s více metodami směrování provozu. Metoda dopředného **směrování provozu umožňuje** vracet několik zdravých koncových bodů a pomáhá zvýšit spolehlivost aplikace, protože klienti mají více možností, jak opakovat bez nutnosti dalšího vyhledávání DNS. Směrování s více hodnotami je povolené jenom pro profily, které mají všechny své koncové body zadané pomocí adres IPv4 nebo IPv6. Při přijetí dotazu pro tento profil se vrátí všechny funkční koncové body na základě konfigurovatelného maximálního počtu vrácených hodnot. 

>[!NOTE]
> V tuto chvíli se přidávají koncové body pomocí adres IPv4 nebo IPv6 jenom pro koncové body typu **External** , takže směrování s více adresami se podporuje i pro tyto koncové body.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pro profil Traffic Manager.
1. V levém podokně Azure Portal vyberte **skupiny prostředků**.
2. V části **skupiny prostředků** v horní části stránky vyberte **Přidat**.
3. Do **název skupiny prostředků** zadejte název *myResourceGroupTM1*. V případě **umístění skupiny prostředků** vyberte **východní USA** a pak vyberte **OK**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil Traffic Manager, který směruje provoz uživatele odesláním do koncového bodu s nejnižší latencí.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **sítě**  >  **Traffic Manager profil**  >  **vytvořit**.
2. V části **vytvořit Traffic Manager profil** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **vytvořit**:
    
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu směrování s více **hodnotami** .                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte *myResourceGroupTM1*. |
    | Umístění                | Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
   |        |           | 
  
   ![Vytvoření profilu Traffic Manageru](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dvě IP adresy jako externí koncové body do profilu vícehodnotového Traffic Manager, který jste vytvořili v předchozím kroku.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Externí koncový bod                                   |
    | Name           | myEndpoint1                                        |
    | Plně kvalifikovaný název domény (FQDN) nebo IP adresa           | Zadejte veřejnou IP adresu koncového bodu, který chcete přidat do tohoto profilu Traffic Manager.                         |
    |        |           |

4. Opakováním kroků 2 a 3 přidejte další koncový bod s názvem *myEndpoint2*, pro **plně kvalifikovaný název domény (FQDN) nebo IP** adresu zadejte veřejnou IP adresu druhého koncového bodu.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

   ![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metodě směrování výkonu](traffic-manager-configure-performance-routing-method.md)
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).


