---
title: Kurz – Konfigurace směrování geografického provozu pomocí Azure Traffic Manageru
description: Tento kurz vysvětluje, jak nakonfigurovat metodu směrování geografického provozu pomocí Azure Traffic Manageru
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938793"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Kurz: Konfigurace metody směrování geografického provozu pomocí Traffic Manageru

Metoda směrování geografického provozu umožňuje směrovat provoz na konkrétní koncové body na základě geografické polohy, odkud požadavky pocházejí. Tento kurz ukazuje, jak vytvořit profil Traffic Manager s touto metodou směrování a nakonfigurovat koncové body pro příjem provozu z konkrétních zeměpisných oblastí.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu traffic managera

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/).
2. Klepněte **na tlačítko Vytvořit** > **profil** > správce provozu**sítě** > **sítě Vytvořit**.
4. V **profilu Vytvořit traffic manager**:
    1. Zadejte název svého profilu. Tento název musí být jedinečný v rámci zóny trafficmanager.net. Chcete-li získat přístup k profilu `<profilename>.trafficmanager.net`traffic manageru, použijte název DNS .
    2. Vyberte metodu **geografického** směrování.
    3. Vyberte předplatné, pod kterým chcete vytvořit tento profil.
    4. Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků k umístění tohoto profilu. Pokud se rozhodnete vytvořit novou skupinu prostředků, použijte rozevírací seznam **Umístění skupiny prostředků** k určení umístění skupiny prostředků. Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manager, který je nasazen globálně.
    5. Po klepnutí na tlačítko **Vytvořit**se profil Traffic Manageru vytvoří a nasadí globálně.

![Vytvoření profilu Traffic Manageru](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Přidání koncových bodů

1. Vyhledejte název profilu Traffic Manageru, který jste vytvořili na vyhledávacím panelu portálu, a při jeho zobrazení klikněte na výsledek.
2. Přejděte do**koncových bodů** **nastavení** -> ve Správci provozu.
3. Kliknutím na **Přidat** zobrazíte **přidat koncový bod**.
3. Klikněte na **Přidat** a v zobrazeném **doplňku Přidat** se doplňte takto:
4. Vyberte **Typ** v závislosti na typu přidávaného koncového bodu. Pro geografické směrovací profily používané v produkčním prostředí důrazně doporučujeme používat vnořené typy koncových bodů obsahující podřízený profil s více než jedním koncovým bodem. Další podrobnosti naleznete v [častých dotazech o metodách směrování geografického provozu](traffic-manager-FAQs.md).
5. Zadejte **Název**, podle kterého tento koncový bod rozpoznáte.
6. Některá pole na této stránce závisí na typu přidávaného koncového bodu:
    1. Pokud přidáváte koncový bod Azure, vyberte **typ cílového prostředku** a **cíl** na základě prostředku, na který chcete směrovat provoz
    2. Pokud přidáváte **externí** koncový bod, zadejte **plně kvalifikovaný název domény (Plně kvalifikovaný název domény)** pro koncový bod.
    3. Pokud přidáváte **vnořený koncový bod**, vyberte **cílový prostředek,** který odpovídá podřízenému profilu, který chcete použít, a zadejte **minimální počet podřízených koncových bodů**.
7. V části Geografické mapování použijte rozevírací rozbalovací položku k přidání oblastí, ze kterých chcete do tohoto koncového bodu odesílat přenosy. Je nutné přidat alespoň jednu oblast a můžete mít namapované více oblastí.
8. Tento postup opakujte pro všechny koncové body, které chcete přidat pod tento profil.

![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Použití profilu Traffic Manageru
1.  Na vyhledávacím panelu portálu vyhledejte název **profilu Traffic Manageru,** který jste vytvořili v předchozí části, a ve výsledcích zobrazených klikněte na profil správce provozu.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. To může být použito všemi klienty (například přechodem na něj pomocí webového prohlížeče) získat směrovány na správný koncový bod, jak je určeno typu směrování.  V případě geografického směrování se Traffic Manager podívá na zdrojovou IP adresu příchozího požadavku a určí oblast, ze které pochází. Pokud je tato oblast mapována na koncový bod, je provoz směrován do této oblasti. Pokud tato oblast není mapována na koncový bod, vrátí Traffic Manager odpověď na dotaz NODATA.

## <a name="next-steps"></a>Další kroky

- Další informace o [metodě směrování geografického provozu](traffic-manager-routing-methods.md#geographic).
- Přečtěte si, jak [otestovat nastavení traffic manageru](traffic-manager-testing-settings.md).
