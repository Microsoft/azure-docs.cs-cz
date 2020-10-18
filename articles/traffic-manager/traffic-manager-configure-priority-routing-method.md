---
title: 'Kurz: Konfigurace směrování provozu s prioritou pomocí Azure Traffic Manager'
description: V tomto kurzu se dozvíte, jak nakonfigurovat metodu směrování provozu s prioritou v Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1be507f3676a5531855e3a8deb6801b1a5cb8e74
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166698"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody směrování provozu priority v Traffic Manager

V tomto kurzu se dozvíte, jak používat Azure Traffic Manager ke směrování provozu uživatelů do konkrétních koncových bodů pomocí metody priority směrování. V této metodě směrování definujete pořadí každého koncového bodu, který přejde do konfigurace profilu Traffic Manager. Provoz od uživatelů bude směrován do koncového bodu v pořadí, v jakém jsou uvedeny. Tato metoda směrování je užitečná, když chcete nakonfigurovat službu pro převzetí služeb při selhání. Primární koncový bod získá prioritní číslo 1 a bude obsluhovat všechny příchozí požadavky. Zatímco koncové body s nižší prioritou budou fungovat jako zálohy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořte profil Traffic Manager s prioritou směrování.
> - Přidat koncové body.
> - Nakonfigurujte prioritu koncových bodů.
> - Použijte profil Traffic Manager.
> - Odstranit profil Traffic Manager.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Konfigurace metody směrování provozu s prioritou
1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com).

1. Na levé straně vyberte **+ vytvořit prostředek** . Vyhledejte **profil Traffic Manager** a vyberte **vytvořit**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. Na stránce *vytvořit profil Traffic Manager* definujte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Název            | Zadejte název profilu. Tento název musí být v rámci zóny trafficmanager.net jedinečný. Pokud chcete získat přístup k profilu Traffic Manager, použijte název DNS `<profilename>.trafficmanager.net` . |    
    | Metoda směrování  | Vyberte **Priorita**. |
    | Předplatné    | Vyberte své předplatné. |
    | Skupina prostředků   | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků, do které chcete tento profil umístit. Pokud se rozhodnete vytvořit novou skupinu prostředků, zadejte umístění skupiny prostředků pomocí rozevíracího seznamu *umístění skupiny prostředků* . Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manager, který se globálně nasazuje. |

1. Vyberte **vytvořit** a nasaďte profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Vytvořit profil priority Traffic Manager":::

## <a name="add-endpoints"></a>Přidat koncové body

1. V seznamu vyberte profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. V části *Nastavení* vyberte **koncové body** a vyberte **+ Přidat** pro přidání nového koncového bodu.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. Vyberte nebo zadejte následující nastavení: 

    | Nastavení                | Hodnota                                              |
    | ---                    | ---                                                |
    | Typ                   | Vyberte typ koncového bodu. |    
    | Název                   | Zadejte název pro identifikaci tohoto koncového bodu. |
    | Typ cílového prostředku   | Vyberte typ prostředku pro cíl. |
    | Cílový prostředek        | Vyberte prostředek ze seznamu. |
    | Priorita               | Zadejte číslo priority pro tento koncový bod. 1 je nejvyšší priorita. |


1. Vyberte **Přidat** a přidejte koncový bod. Opakujte kroky 2 a 3 pro přidání dalších koncových bodů. Nezapomeňte nastavit příslušné číslo priority.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. Na stránce **koncové body** Zkontrolujte pořadí priority pro koncové body. Když vyberete metodu směrování přenosů **priority** , pořadí vybraných koncových bodů. Ověřte pořadí priorit koncových bodů.  Primární koncový bod je nahoře. Dvakrát ověřte pořadí, ve kterém je zobrazeno. Všechny požadavky budou směrovány do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, přenos dat automaticky převezme další koncový bod. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. Chcete-li změnit pořadí priority koncového bodu, vyberte koncový bod, změňte hodnotu priority a **výběrem možnosti Uložit uložte** nastavení koncového bodu.

## <a name="use-the-traffic-manager-profile"></a>Použít profil Traffic Manager

1.  Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části, a v zobrazených výsledcích vyberte profil Traffic Manageru.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Vytvořit profil priority Traffic Manager":::

1.  Na stránce Přehled **profilu Traffic Manager** se zobrazuje název DNS nově vytvořeného profilu Traffic Manager. Můžete je použít u všech klientů (například tak, že na ně přejdete pomocí webového prohlížeče), abyste se dostali ke správnému koncovému bodu, který určuje typ směrování. V tomto případě se všechny požadavky nasměrují do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, přenos dat automaticky převezme další koncový bod.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Vytvořit profil priority Traffic Manager":::

1. Jakmile profil Traffic Manager funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby odkazoval na název domény vaší společnosti na název domény Traffic Manager.

## <a name="clean-up-resource"></a>Vyčištění prostředku

Pokud nepotřebujete profil Traffic Manager, najděte profil a vyberte **Odstranit profil**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Vytvořit profil priority Traffic Manager":::

## <a name="next-steps"></a>Další kroky

Další informace o metodě prioritního směrování najdete v tématech:

> [!div class="nextstepaction"]
> [Priorita – Metoda směrování](traffic-manager-routing-methods.md#priority-traffic-routing-method)
