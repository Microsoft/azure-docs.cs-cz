---
title: 'Kurz: Konfigurace geografického směrování provozu pomocí Azure Traffic Manager'
description: V tomto kurzu se dozvíte, jak nakonfigurovat metodu geografického směrování provozu pomocí Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188662"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Kurz: Konfigurace metody geografického směrování provozu pomocí Traffic Manager

Metoda geografického směrování provozu umožňuje směrovat provoz do konkrétních koncových bodů na základě geografického umístění, ve kterém požadavky pocházejí. V tomto kurzu se dozvíte, jak vytvořit profil Traffic Manager s touto metodou směrování a nakonfigurovat koncové body pro příjem provozu z konkrétních geografických oblastí.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořte profil Traffic Manager s geografickým směrováním.
> - Konfigurace vnořeného koncového bodu.
> - Použijte profil Traffic Manager.
> - Odstranit profil Traffic Manager.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Vytvořit profil Traffic Manager

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com).

1. Na levé straně vyberte **+ vytvořit prostředek** . Vyhledejte **profil Traffic Manager** a vyberte **vytvořit**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Vytvořit profil Traffic Manager":::

1. Na stránce *vytvořit profil Traffic Manager* definujte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Název            | Zadejte název profilu. Tento název musí být v rámci zóny trafficmanager.net jedinečný. Pokud chcete získat přístup k profilu Traffic Manager, použijte název DNS `<profilename>.trafficmanager.net` . |    
    | Metoda směrování  | Vyberte **geografické**. |
    | Předplatné    | Vyberte své předplatné. |
    | Skupina prostředků   | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků, do které chcete tento profil umístit. Pokud se rozhodnete vytvořit novou skupinu prostředků, zadejte umístění skupiny prostředků pomocí rozevíracího seznamu *umístění skupiny prostředků* . Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manager, který se globálně nasazuje. |

1. Vyberte **vytvořit** a nasaďte profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Vytvoření stránky profilu Traffic Manager":::

## <a name="add-endpoints"></a>Přidat koncové body

1. V seznamu vyberte profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager zeměpisný seznam":::

1. V části *Nastavení* vyberte **koncové body** a vyberte **+ Přidat** pro přidání nového koncového bodu.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Přidat koncové body":::

1. Vyberte nebo zadejte následující nastavení: 

    | Nastavení                | Hodnota                                              |
    | ---                    | ---                                                |
    | Typ                   | Vyberte typ koncového bodu. Pro geografické profily směrování používané v produkčním prostředí důrazně doporučujeme používat vnořené typy koncových bodů obsahující podřízený profil s více než jedním koncovým bodem. Další informace najdete v tématu [Nejčastější dotazy týkající se metod směrování geografického provozu](traffic-manager-FAQs.md). |    
    | Name                   | Zadejte název pro identifikaci tohoto koncového bodu. |
    | Typ cílového prostředku   | Vyberte typ prostředku pro cíl. |
    | Cílový prostředek        | Vyberte prostředek ze seznamu. |

    > [!Note]
    > Některá pole na této stránce závisí na typu koncového bodu, který přidáváte:
    > 1. Pokud přidáváte koncový bod Azure, vyberte **cílový typ prostředku** a **cíl** na základě prostředku, do kterého chcete směrovat provoz.
    > 1. Pokud přidáváte **externí** koncový bod, zadejte **plně kvalifikovaný název domény (FQDN)** pro váš koncový bod.
    > 1. Pokud přidáváte **vnořený koncový bod**, vyberte **cílový prostředek** , který odpovídá podřízenému profilu, který chcete použít, a určete **minimální počet podřízených koncových bodů**.

1. V části *geografické mapování* můžete pomocí rozevíracího seznamu přidat oblasti, ze kterých chcete odeslat provoz do tohoto koncového bodu. Je nutné přidat alespoň jednu oblast. Můžete mít namapované více oblastí.

1. Opakujte poslední krok u všech koncových bodů, které chcete přidat pod tento profil, a pak vyberte **Uložit**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Přidání koncového bodu služby Traffic Manager":::

## <a name="use-the-traffic-manager-profile"></a>Použít profil Traffic Manager

1.  Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části, a v zobrazených výsledcích vyberte profil Traffic Manageru.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Vyhledat profil Traffic Manageru":::

1. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. Název můžou použít všichni klienti (například tak, že se k němu přejde pomocí webového prohlížeče), aby se směroval do správného koncového bodu, který určuje typ směrování. V případě geografického směrování se Traffic Manager prohlíží na zdrojové IP adrese příchozího požadavku a určí oblast, ze které pochází. Pokud je tato oblast namapovaná na koncový bod, do ní se směruje provoz. Pokud tato oblast není namapovaná na koncový bod, Traffic Manager vrátí odpověď na dotaz na DATA.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Přehled geografických Traffic Manager":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete profil Traffic Manager, najděte profil a vyberte **Odstranit profil**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Odstranit profil Traffic Manager":::

## <a name="next-steps"></a>Další kroky

Další informace o metodě geografického směrování najdete v tématech:

> [!div class="nextstepaction"]
> [Metoda směrování geografického provozu](traffic-manager-routing-methods.md#geographic)
