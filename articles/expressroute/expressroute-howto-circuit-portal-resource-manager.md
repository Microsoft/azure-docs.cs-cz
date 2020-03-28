---
title: Kurz - Vytvoření a úprava okruhu pomocí ExpressRoute
description: V tomto kurzu se dozvíte, jak vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74813147"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Kurz: Vytvoření a úprava okruhu ExpressRoute

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vám pomůže vytvořit okruh ExpressRoute pomocí portálu Azure a modelu nasazení Azure Resource Manageru. Můžete také zkontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

## <a name="before-you-begin"></a>Než začnete

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)
* Ujistěte se, že máte přístup k [portálu Azure](https://portal.azure.com).
* Ujistěte se, že máte oprávnění k vytváření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na správce účtu.
* Můžete [zobrazit video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před začátkem, abyste lépe porozuměli krokům.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Přihlášení k portálu Azure

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Vytvoření nového okruhu ExpressRoute

> [!IMPORTANT]
> Okruh ExpressRoute se účtuje od okamžiku, kdy je vydán servisní klíč. Ujistěte se, že provedete tuto operaci, když je poskytovatel připojení připraven zřídit okruh.

Okruh ExpressRoute můžete vytvořit výběrem možnosti pro vytvoření nového prostředku. 

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**. Vyberte **síť** > **ExpressRoute**, jak je znázorněno na následujícím obrázku:

   ![Vytvoření okruhu ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Po klepnutí na tlačítko **ExpressRoute**se zobrazí stránka **Vytvořit okruh ExpressRoute.** Při vyplňování hodnot na této stránce se ujistěte, že jste zadali správnou úroveň sku (standard nebo premium) a fakturační model měření dat (neomezený nebo měřený).

   ![Konfigurace úrovně Skladové třídy a měření dat](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Úroveň** určuje, zda je povolen standard ExpressRoute nebo doplněk premium ExpressRoute. Můžete zadat **Standard** získat standardní Skladovou položku nebo **Premium** pro premium add-on.
   * **Měření dat** určuje typ fakturace. Můžete zadat **měřeno** pro datový tarif účtovaný účtárna a **Neomezený** pro neomezený datový tarif. Všimněte si, že můžete změnit typ fakturace z **měřeno** na **neomezené**.

     > [!IMPORTANT]
     > Typ z Neomezené nelze změnit na **Měřeno** . **Metered**

   * **Umístění partnerského vztahu** je fyzické umístění, kde spolupracujete se společností Microsoft.

     > [!IMPORTANT]
     > Umístění partnerského vztahu označuje [fyzické umístění,](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. To **není** propojena s vlastností "Umístění", která odkazuje na zeměpisné oblasti, kde je umístěn zprostředkovatele síťových prostředků Azure. I když nejsou ve spojení, je vhodné zvolit poskytovatele síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.

### <a name="3-view-the-circuits-and-properties"></a>3. Zobrazení obvodů a vlastností

**Zobrazit všechny obvody**

Všechny okruhy, které jste vytvořili, můžete zobrazit výběrem **možnosti Všechny prostředky** v nabídce na levé straně.

![Zobrazit obvody](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Zobrazit vlastnosti**

Vlastnosti okruhu můžete zobrazit jeho výběrem. Na stránce **Přehled** okruhu se v poli klíč služby zobrazí klíč služby. Je nutné zkopírovat klíč služby pro váš okruh a předat jej poskytovateli služeb k dokončení procesu zřizování. Servisní klíč obvodu je specifický pro váš obvod.

![Zobrazení vlastností](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Odešlete klíč služby svému poskytovateli připojení pro zřizování

Na této stránce **poskytuje stav zprostředkovatele** informace o aktuálním stavu zřizování na straně poskytovatele služeb. **Stav okruhu** poskytuje stav na straně společnosti Microsoft. Další informace o stavech zřizování okruhů naleznete v článku [Pracovní postupy.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

Stav poskytovatele: Nezřízeno<BR>
Stav okruhu: Povoleno

![Zahájit proces zřizování](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Okruh se změní na následující stav, když je poskytovatel připojení v procesu povolení pro vás:

Stav zprostředkovatele: Zřizování<BR>
Stav okruhu: Povoleno

Abyste mohli použít okruh ExpressRoute, musí být v následujícím stavu:

Stav poskytovatele: Zřízený<BR>
Stav okruhu: Povoleno

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Pravidelně kontrolujte stav a stav klíče obvodu

Vlastnosti okruhu, který vás zajímá, můžete zobrazit jeho výběrem. Zkontrolujte **stav zprostředkovatele** a ujistěte se, že se přesunul do **Zřízena,** než budete pokračovat.

![Stav okruhu a zprostředkovatele](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Vytvoření konfigurace směrování

Podrobné pokyny naleznete v článku konfigurace [směrování okruhů ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) a vytvořte a upravte partnerský vztah okruhu.

> [!IMPORTANT]
> Tyto pokyny platí pouze pro okruhy, které jsou vytvořeny s poskytovateli služeb, které nabízejí služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí služby spravované vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení konfiguruje a spravuje směrování za vás.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Propojení virtuální sítě s okruhem ExpressRoute

Dále propojte virtuální síť s okruhem ExpressRoute. Při práci s modelem nasazení Správce prostředků použijte článek [Propojení virtuálních sítí s okruhy ExpressRoute.](expressroute-howto-linkvnet-arm.md)

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Získání stavu okruhu ExpressRoute

Stav okruhu můžete zobrazit tak, že jej vyberete a zobrazíte stránku Přehled.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute

Můžete upravit určité vlastnosti okruhu ExpressRoute bez ovlivnění připojení. Můžete upravit šířku pásma, skladovou položku, fakturační model a povolit klasické operace na stránce **Konfigurace.** Informace o omezeních a omezeních naleznete v [nejčastějších dotazech k expresní trase](expressroute-faqs.md).

Můžete provádět následující úkoly bez prostoje:

* Povolte nebo zakažte doplněk ExpressRoute Premium pro okruh ExpressRoute.
* Zvyšte šířku pásma okruhu ExpressRoute za předpokladu, že je na portu k dispozici kapacita.

  > [!IMPORTANT]
  > Snížení šířky pásma okruhu není podporováno.

* Změňte plán měření z *měřených dat* na *neomezená data*.

  > [!IMPORTANT]
  > Změna plánu měření z neomezených dat na data účtovaná nejsou podporována.

* Můžete povolit a zakázat *povolit klasické operace*.
  > [!IMPORTANT]
  > Bude pravděpodobně muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečná kapacita na existující port. Okruh nelze upgradovat, pokud v tomto umístění není k dispozici žádná další kapacita.
  >
  > I když můžete bez problémů upgradovat šířku pásma, nelze snížit šířku pásma okruhu ExpressRoute bez přerušení. Snížení šířky pásma vyžaduje zrušení zřízení okruhu ExpressRoute a následné opětovné zřízení nového okruhu ExpressRoute.
  >
  > Zakázání operace doplňku Premium může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.

Chcete-li upravit okruh ExpressRoute, klepněte na tlačítko **Konfigurace**.

![Upravit obvod](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Okruh ExpressRoute můžete odstranit výběrem ikony **odstranění.** Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace nezdaří, zkontrolujte, zda jsou všechny virtuální sítě propojeny s okruhem.
* Pokud je stav zřizování zprostředkovatele okruhů ExpressRoute **Provisioning** nebo **Provisioned,** musíte spolupracovat s poskytovatelem služeb, abyste zrušili zřízení okruhu na jejich straně. Budeme i nadále rezervovat zdroje a účtovat vám, dokud poskytovatel služeb dokončí zrušení zřízení okruhu a upozorní nás.
* Pokud poskytovatel služeb zrušil okruh (stav zřizování poskytovatele služeb je nastaven na **Není zřízeno**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu pokračujte následujícími kroky:

* [Vytvoření a úprava směrování okruhu ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
