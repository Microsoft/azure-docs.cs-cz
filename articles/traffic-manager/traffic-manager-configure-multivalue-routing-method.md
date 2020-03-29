---
title: Konfigurace směrování provozu s více hodnotami – Azure Traffic Manager
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager směrovat provoz do koncových bodů A/AAAA.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938781"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurace metody směrování MultiValue ve Správci provozu

Tento článek popisuje, jak nakonfigurovat metodu směrování provozu MultiValue. Metoda směrování provozu **více hodnot** umožňuje vrátit více koncových bodů v pořádku a pomáhá zvýšit spolehlivost aplikace, protože klienti mají více možností, jak opakovat bez nutnosti provést další vyhledávání DNS. Směrování multivalue je povoleno pouze pro profily, které mají všechny své koncové body určené pomocí adres IPv4 nebo IPv6. Při přijetí dotazu pro tento profil jsou vráceny všechny koncové body v pořádku na základě zadaného konfigurovatelného maximálního počtu vrácených dat. 

>[!NOTE]
> V tuto chvíli je přidání koncových bodů pomocí adres IPv4 nebo IPv6 podporováno pouze pro koncové body typu **Externí,** a proto je směrování MultiValue podporováno pouze pro tyto koncové body.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pro profil traffic manageru.
1. V levém podokně portálu Azure vyberte **skupiny prostředků**.
2. Ve **skupinách zdrojů**vyberte v horní části stránky **možnost Přidat**.
3. Do **názvu skupiny prostředků**zadejte název *myResourceGroupTM1*. V **umístění skupiny zdrojů**vyberte možnost Východní **USA**a pak vyberte **OK**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil Traffic Manager, který směruje provoz uživatelů jejich odesláním do koncového bodu s nejnižší latencí.

1. V levém horním rohu obrazovky vyberte Vytvořit**profil** >  **správce** > provozu**sítě** > **Vytvořit**.
2. V **části Vytvořit profil Traffic Manageru**zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Vytvořit**:
    
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Name (Název)                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu **vícehodnotového** směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte *myResourceGroupTM1*. |
    | Umístění                | Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
   |        |           | 
  
   ![Vytvoření profilu Traffic Manageru](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dvě adresy IP jako externí koncové body do profilu aplikace MultiValue Traffic Manager, který jste vytvořili v předchozím kroku.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Externí koncový bod                                   |
    | Name (Název)           | myEndpoint1                                        |
    | Plně kvalifikovaný název domény (FQDN) nebo IP           | Zadejte veřejnou IP adresu koncového bodu, který chcete přidat do tohoto profilu Traffic Manageru.                         |
    |        |           |

4. Opakováním kroků 2 a 3 přidejte další koncový bod s názvem *myEndpoint2*, pro **plně kvalifikovaný název domény (Plně kvalifikovaný název domény) nebo IP**, zadejte veřejnou IP adresu druhého koncového bodu.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

   ![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metodě směrování výkonu](traffic-manager-configure-performance-routing-method.md)
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).


