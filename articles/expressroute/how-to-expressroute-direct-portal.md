---
title: 'Azure ExpressRoute: Konfigurace ExpressRoute Direct: portál'
description: Tato stránka vám pomůže nakonfigurovat ExpressRoute Direct pomocí portálu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: dac9df23209fa93b8060ad80d58fd66f7a356ecd
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651475"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Vytvoření ExpressRoute s přímým přístupem pomocí Azure Portal

V tomto článku se dozvíte, jak vytvořit ExpressRoute s přímým přístupem pomocí Azure Portal.
ExpressRoute Direct umožňuje přímo se připojit k globální síti Microsoftu v umístěních partnerských vztahů, které jsou strategické distribuce na celém světě. Další informace najdete v článku o [ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Ověřte, jestli je ve vašem předplatném zaregistrovaný poskytovatel prostředků **Microsoft. Network** . Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků.

1. Přístup k nastavením předplatného, jak je popsáno v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).
1. V rámci vašeho předplatného pro **poskytovatele prostředků**ověřte, že poskytovatel **Microsoft. Network** zobrazuje **registrovaný** stav. Pokud poskytovatel prostředků Microsoft. Network není uveden v seznamu registrovaných zprostředkovatelů, přidejte ho.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Vytvořit ExpressRoute Direct

1. V nabídce [Azure Portal](https://portal.azure.com) nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. Na **nové** stránce v poli ***Hledat na Marketplace*** zadejte **ExpressRoute Direct**a potom vyberte **ENTER** , abyste se dostali do výsledků hledání.

1. Z výsledků vyberte **ExpressRoute Direct**.

1. Na stránce **ExpressRoute Direct** vyberte **vytvořit** a otevřete stránku **vytvořit ExpressRoute Direct** .

1. Začněte tím, že na stránce **základy** vyplníte pole.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Stránka základy":::

    * **Předplatné**: předplatné Azure, které chcete použít k vytvoření nového ExpressRoute Direct. Okruhy prostředků ExpressRoute Direct a ExpressRoute musí být ve stejném předplatném.
    * **Skupina prostředků**: Skupina prostředků Azure, ve které se vytvoří nový prostředek s přímým přístupem ExpressRoute. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Oblast**: veřejná oblast Azure, ve které se prostředek vytvoří.
    * **Name**(název): název nového prostředku ExpressRoute Direct.

1. Dále vyplňte pole na stránce **Konfigurace** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Stránka základy":::

    * **Umístění partnerského vztahu**: umístění partnerského vztahu, ke kterému se připojíte přímo k ExpressRoute prostředku. Další informace o umístěních partnerských vztahů najdete v [ExpressRoute umístěních](expressroute-locations-providers.md).
   * **Šířka pásma**: šířka pásma párování portů, kterou chcete rezervovat. ExpressRoute Direct podporuje možnosti šířky pásma 10 GB a 100 GB. Pokud požadovaná šířka pásma není v zadaném umístění partnerského vztahu k dispozici, [otevřete žádost o podporu v Azure Portal](https://aka.ms/azsupt).
   * **Zapouzdření**: ExpressRoute Direct podporuje zapouzdření QinQ i Dot1Q.
     * Pokud je vybraná možnost QinQ, bude se každému okruhu ExpressRoute dynamicky přiřazovat značka S-a bude jedinečný v celém prostředku ExpressRoute Direct.
     *  Každé označení C na okruhu musí být v okruhu jedinečné, ale ne přes ExpressRoute Direct.
     * Pokud je vybraná možnost zapouzdření Dot1Q, musíte spravovat jedinečnost značky C (VLAN) napříč celým prostředkem ExpressRoute Direct.
     >[!IMPORTANT]
     >ExpressRoute Direct může být jenom jeden typ zapouzdření. Po přímém vytvoření ExpressRoute se zapouzdření nedá změnit.
     >

1. Zadejte všechny značky prostředku a pak vyberte **zkontrolovat + vytvořit** a ověřte nastavení prostředků ExpressRoute Direct.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Stránka základy":::

1. Vyberte **Vytvořit**. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Vygenerovat písmeno autorizace (LOA)

V tuto chvíli není na portálu k dispozici generování dopisu o autorizaci. K získání písmene autorizace použijte **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** nebo **[Azure CLI](expressroute-howto-expressroute-direct-cli.md#authorization)** .

## <a name="change-admin-state-of-links"></a><a name="state"></a>Změnit stav Správce odkazů

Tento proces by měl být použit k provedení testu vrstvy 1, čímž se zajistí, že každé připojení mezi jednotlivými směrovači je u primárních a sekundárních zařízení správně opraveno.

1. Na stránce **Přehled** prostředků ExpressRoute Direct vyberte v části **odkazy** možnost **Link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Stránka základy" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Přepněte nastavení **Stav Správce** na **povoleno**a pak vyberte **Uložit**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Stránka základy":::

    >[!IMPORTANT]
    >Fakturace bude zahájena, když je na obou odkazech povolen stav správce.
    >

1. Opakujte stejný postup pro **link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Vytvoření okruhu

Ve výchozím nastavení můžete vytvořit 10 okruhů v rámci předplatného, kde je prostředek ExpressRoute Direct. Toto číslo lze zvýšit prostřednictvím podpory. Zodpovídáte za sledování zřízené i využité šířky pásma. Zřízená šířka pásma je součet šířky pásma všech okruhů v prostředku ExpressRoute Direct. Využitá šířka pásma je fyzické využití základních fyzických rozhraní.

* K dispozici jsou další šířky pásma okruhů, které je možné využít na ExpressRoute přímo pro podporu scénářů uvedených výše. Jsou to: 40 GB/s a 100 GB/s.

* SkuTier může být Local, Standard nebo Premium.

* SkuFamily musí být jenom MeteredData. V ExpressRoute Direct se nepodporuje neomezený počet.

Následující kroky vám pomůžou vytvořit okruh ExpressRoute z pracovního postupu ExpressRoute Direct. Pokud byste místo toho mohli vytvořit okruh pomocí pracovního postupu pravidelného okruhu, i když nebudete mít k dispozici žádné výhody použití pracovních postupů pravidelného okruhu pro tuto konfiguraci. Přečtěte si téma [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md).

1. V části **Nastavení** ExpressRoute Direct vyberte **okruhy**a pak vyberte **+ Přidat**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Stránka základy" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Nakonfigurujte nastavení na stránce **Konfigurace** .

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Stránka základy":::

1. Zadejte všechny značky prostředků, vyberte **zkontrolovat + vytvořit** , aby bylo možné hodnoty před vytvořením prostředku ověřit.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Stránka základy":::

1. Vyberte **Vytvořit**. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. 

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute Direct najdete v [přehledu](expressroute-erdirect-about.md).
