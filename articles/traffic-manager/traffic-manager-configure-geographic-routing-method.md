---
title: Kurz – konfigurace geografického směrování provozu pomocí Azure Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat metodu geografického směrování provozu pomocí Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: duau
ms.openlocfilehash: 53773d7c616edec067e1ed1778b7ce6b500ee936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462611"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Kurz: Konfigurace metody geografického směrování provozu pomocí Traffic Manager

Metoda geografického směrování provozu umožňuje směrovat provoz do konkrétních koncových bodů na základě geografického umístění, ve kterém požadavky pocházejí. V tomto kurzu se dozvíte, jak vytvořit profil Traffic Manager s touto metodou směrování a nakonfigurovat koncové body pro příjem provozu z konkrétních geografických oblastí.

## <a name="create-a-traffic-manager-profile"></a>Vytvořit profil Traffic Manager

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/).
2. Klikněte na **vytvořit prostředek**  >  **sítě**  >  **Traffic Manager profil**  >  **vytvořit**.
4. V **profilu Create Traffic Manager**:
    1. Zadejte název profilu. Tento název musí být v rámci zóny trafficmanager.net jedinečný. Pokud chcete získat přístup k profilu Traffic Manager, použijte název DNS `<profilename>.trafficmanager.net` .
    2. Vyberte metodu **geografického** směrování.
    3. Vyberte předplatné, pod kterým chcete tento profil vytvořit.
    4. Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků, do které chcete tento profil umístit. Pokud se rozhodnete vytvořit novou skupinu prostředků, zadejte umístění skupiny prostředků pomocí rozevíracího seznamu **umístění skupiny prostředků** . Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manager, který se globálně nasazuje.
    5. Po kliknutí na **vytvořit**se profil Traffic Manager vytvoří a nasadí globálně.

![Vytvoření profilu Traffic Manageru](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Přidat koncové body

1. Vyhledejte název profilu Traffic Manager, který jste vytvořili v panelu hledání na portálu, a po zobrazení klikněte na výsledek.
2. Přejděte do **Nastavení**  ->  **koncové body** v Traffic Manager.
3. Klikněte na **Přidat** a v podokně **přidat koncový bod** , které se zobrazí, proveďte následující kroky:
4. V závislosti na typu koncového bodu, který chcete přidat, vyberte **typ** . Pro geografické profily směrování používané v produkčním prostředí důrazně doporučujeme používat vnořené typy koncových bodů obsahující podřízený profil s více než jedním koncovým bodem. Další podrobnosti najdete v tématu [Nejčastější dotazy týkající se metod směrování geografického provozu](traffic-manager-FAQs.md).
5. Zadejte **Název**, podle kterého tento koncový bod rozpoznáte.
6. Některá pole na této stránce závisí na typu koncového bodu, který přidáváte:
    1. Pokud přidáváte koncový bod Azure, vyberte **cílový typ prostředku** a **cíl** na základě prostředku, do kterého chcete směrovat provoz.
    2. Pokud přidáváte **externí** koncový bod, zadejte **plně kvalifikovaný název domény (FQDN)** pro váš koncový bod.
    3. Pokud přidáváte **vnořený koncový bod**, vyberte **cílový prostředek** , který odpovídá podřízenému profilu, který chcete použít, a určete **minimální počet podřízených koncových bodů**.
7. V části geografické mapování můžete pomocí rozevírací nabídky Přidat oblasti, ze kterých chcete odeslat provoz do tohoto koncového bodu. Musíte přidat alespoň jednu oblast a můžete mít namapované více oblastí.
8. Tento postup opakujte pro všechny koncové body, které chcete přidat pod tento profil.

![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Použít profil Traffic Manager
1.  Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části, a klikněte na profil Traffic Manageru v zobrazených výsledcích.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. Můžete je použít u všech klientů (například tak, že na ně přejdete pomocí webového prohlížeče), abyste se dostali ke správnému koncovému bodu, který určuje typ směrování.  V případě geografického směrování Traffic Manager prohlíží zdrojovou IP adresu příchozího požadavku a určí oblast, ze které pochází. Pokud je tato oblast namapovaná na koncový bod, do ní se směruje provoz. Pokud tato oblast není namapovaná na koncový bod, Traffic Manager vrátí odpověď na dotaz na DATA.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [metodě směrování geografického provozu](traffic-manager-routing-methods.md#geographic).
- Naučte se [Testovat nastavení Traffic Manager](traffic-manager-testing-settings.md).
