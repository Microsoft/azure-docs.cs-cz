---
title: Kurz – vytvoření a úprava okruhu pomocí ExpressRoute
description: V tomto kurzu se naučíte, jak vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: 58c35b094d21dc562e61b4819c0d8e063908392d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322115"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Kurz: vytvoření a úprava okruhu ExpressRoute

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

V tomto kurzu se dozvíte, jak vytvořit okruh ExpressRoute pomocí Azure Portal a modelu nasazení Azure Resource Manager. Můžete také kontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření okruhu ExpressRoute
> * Získat aktuální stav okruhu
> * Úprava okruhu
> * Zrušení zřízení a odstranění okruhu

## <a name="before-you-begin"></a>Než začnete

* Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .
* Ujistěte se, že máte přístup k [Azure Portal](https://portal.azure.com).
* Ujistěte se, že máte oprávnění k vytváření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na správce účtu.
* Než začnete tento postup lépe pochopit, můžete si [video prohlédnout](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Přihlaste se k Azure Portal

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Vytvořte nový okruh ExpressRoute.

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. Ujistěte se, že tuto operaci provedete, když je poskytovatel připojení připravený k zřízení okruhu.

Okruh ExpressRoute můžete vytvořit tak, že vyberete možnost vytvoření nového prostředku. 

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Vyberte **sítě**  >  **ExpressRoute**, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Vytvoření okruhu ExpressRoute":::

2. Po kliknutí na **ExpressRoute**se zobrazí stránka **vytvořit ExpressRoute** . Zadejte **skupinu prostředků**, **oblast**a  **název** okruhu. Pak klikněte na **Další: >konfigurace **.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurace skupiny prostředků a oblasti":::

3. Když vyplníte hodnoty na této stránce, ujistěte se, že zadáváte správnou úroveň SKU (Local, Standard nebo Premium) a model fakturace měření dat (bez omezení nebo měření).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurace okruhu":::
    
    * **Typ portu** určuje, jestli se připojujete k poskytovateli služeb nebo přímo do globální sítě Microsoftu v umístění partnerského vztahu.
    * **Vytvořit nový nebo importovat z klasického** nastavení určuje, jestli se vytvoří nový okruh, nebo pokud migrujete klasický okruh na ARM.
    * **Poskytovatel** je služba sítě Internet, od které budete požadovat službu.
    * **Umístění partnerského vztahu** je fyzické umístění, kde se s Microsoftem pracujete jako partnerský vztah.

    > [!IMPORTANT]
    > Umístění partnerského vztahu indikuje [fyzické umístění](expressroute-locations.md) , kde se s Microsoftem pracujete. Toto není **propojeno s vlastností** "umístění", která odkazuje na geografickou oblast, kde se nachází poskytovatel síťových prostředků Azure. I když se netýkají, je vhodné zvolit poskytovatele síťových prostředků geograficky blízko umístění partnerského vztahu okruhu.

    * **SKU** určuje, jestli je povolený místní ExpressRoute, ExpressRoute Standard nebo doplněk ExpressRoute Premium. Můžete zadat **místní** SKU a získat místní skladovou položku ( **Standard** ), abyste získali standardní SKU nebo **prémii** pro doplněk Premium.
    * **Model fakturace** určuje typ fakturace. Můžete určit **měření** pro měřený datový tarif a **neomezený** pro neomezený datový tarif. Všimněte si, že můžete změnit typ fakturace z **měření** na **neomezeno**.

    > [!IMPORTANT]
    > Nemůžete změnit typ z **neomezené** na **měřený**.

    * Možnost **Povolení klasického provozu** umožní propojení klasických virtuálních sítí s okruhem.

### <a name="3-view-the-circuits-and-properties"></a>3. zobrazení okruhů a vlastností

**Zobrazit všechny okruhy**

Můžete zobrazit všechny okruhy, které jste vytvořili výběrem možnosti **všechny služby > sítě > okruhy ExpressRoute** v nabídce na levé straně.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Nabídka okruhu ExpressRoute":::

Tady se zobrazí všechny okruhy ExpressRoute vytvořené v rámci předplatného.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Seznam okruhů ExpressRoute":::

**Zobrazit vlastnosti**

Vlastnosti okruhu můžete zobrazit tak, že ho vyberete. Na stránce **Přehled** pro váš okruh se klíč služby zobrazí v poli klíč služby. Chcete-li dokončit proces zajišťování, přečtěte si klíč služby pro váš okruh a poskytněte mu poskytovatele služby. Klíč služby je specifický pro váš okruh.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Zobrazení vlastností":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. zaslat klíč služby poskytovateli připojení pro zřizování

Na této stránce **stav poskytovatele** poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. **Stav okruhu** poskytuje stav na straně Microsoftu. Další informace o stavech zřizování okruhů najdete v článku [věnovaném pracovním](expressroute-workflows.md#expressroute-circuit-provisioning-states) postupům.

Při vytváření nového okruhu ExpressRoute je okruh v následujícím stavu:

Stav poskytovatele: **není zřízené** .<BR>
Stav okruhu: **povoleno**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Zahájit proces zřizování":::

Když poskytovatel připojení v procesu zapíná za vás, změní se okruh na následující stav:

Stav poskytovatele: **zřizování**<BR>
Stav okruhu: **povoleno**

Aby bylo možné používat okruh ExpressRoute, musí být v následujícím stavu:

Stav poskytovatele: **zajištěno**<BR>
Stav okruhu: **povoleno**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. pravidelně kontroluje stav a stav klíče okruhu.

Výběrem této možnosti můžete zobrazit vlastnosti okruhu, který vás zajímá. Než budete pokračovat, zkontrolujte **stav poskytovatele** a ujistěte se, že byl přesunut do stavu **zřizování** .

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Stav okruhu a poskytovatele":::

### <a name="6-create-your-routing-configuration"></a>6. Vytvořte konfiguraci směrování.

Podrobné pokyny najdete v článku věnovaném [konfiguraci směrování okruhů ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) k vytváření a úpravám partnerských vztahů okruhů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby pro připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. propojení virtuální sítě k okruhu ExpressRoute

Potom propojte virtuální síť se svým okruhem ExpressRoute. Při práci s modelem nasazení Správce prostředků použijte [propojení virtuálních sítí s okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Získání stavu okruhu ExpressRoute

Stav okruhu můžete zobrazit tak, že ho vyberete a zobrazíte stránku Přehled.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute

Můžete upravit některé vlastnosti okruhu ExpressRoute, aniž by to mělo vliv na připojení. Můžete upravit šířku pásma, SKU a model fakturace a na stránce **Konfigurace** povolte klasické operace. Informace o omezeních a omezeních najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

Bez výpadků můžete provádět následující úlohy:

* Povolí nebo zakáže doplněk ExpressRoute Premium pro okruh ExpressRoute.
* Zvyšte šířku pásma okruhu ExpressRoute, pokud je na portu k dispozici kapacita.

  > [!IMPORTANT]
  > Downgrade šířky pásma okruhu není podporována.

* Změňte plán měření z *měřených dat* na *neomezená data*.

  > [!IMPORTANT]
  > Změna plánu měření z **neomezených dat** na **měřená data** není podporována.

* Můžete povolit nebo zakázat *operace klasických operací*.
  > [!IMPORTANT]
  > Je možné, že bude nutné znovu vytvořit okruh ExpressRoute, pokud na stávajícím portu není dostatečná kapacita. Pokud v daném umístění není k dispozici žádná další kapacita, nemůžete upgradovat okruh.
  >
  > I když můžete plynule upgradovat šířku pásma, nemůžete snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrading Bandwidth vyžaduje, abyste zrušili zřízení okruhu ExpressRoute a pak znovu zřídili nový okruh ExpressRoute.
  >
  > Vypnutí operace doplňku Premium může selhat, pokud používáte prostředky, které jsou větší, než je povoleno pro standardní okruh.

Chcete-li upravit okruh ExpressRoute, klikněte na položku **Konfigurace**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Upravit okruh":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.

> [!NOTE]
>* Před zrušením zřízení musíte odpojit *všechny virtuální sítě* od okruhu ExpressRoute. Pokud tato operace neproběhne úspěšně, ověřte, zda jsou pro daný okruh připojeny nějaké virtuální sítě.
>* Pokud poskytovatel služby zrušil zřízení okruhu (stav zřizování poskytovatele služeb je nastavený na **není zřízený**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit tak, že vyberete ikonu **Odstranit** . 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Odstranit okruh":::

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu pokračujte následujícími dalšími kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)