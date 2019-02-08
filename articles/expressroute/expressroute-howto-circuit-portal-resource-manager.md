---
title: 'Vytvoření a úprava okruhu ExpressRoute – portál: Azure | Dokumentace Microsoftu'
description: Vytvoření, zřízení, ověřte, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc;ganesr
ms.custom: seodec18
ms.openlocfilehash: 3abdeff3c3f1a4069130ed7c8d49d485feea4093
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894713"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Vytvoření a úprava okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vám pomůže vytvořit okruh ExpressRoute pomocí modelu nasazení Azure Resource Manageru a webu Azure portal. Můžete také zkontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.


## <a name="before-you-begin"></a>Před zahájením
* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Ujistěte se, že máte přístup k [webu Azure portal](https://portal.azure.com).
* Ujistěte se, že máte oprávnění k vytvoření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na svého správce účtu.
* Je možné [zobrazení videa](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před zahájením Chcete-li lépe pochopit kroky.

## <a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Přihlášení k webu Azure Portal
V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Vytvoření nového okruhu ExpressRoute
> [!IMPORTANT]
> Váš okruh ExpressRoute se účtují od okamžiku, kdy vydáním klíče služby. Ujistěte se provést tuto operaci, pokud poskytovatel připojení je připraveno ke zřízení okruhu.
> 
> 

1. Okruh ExpressRoute můžete vytvořit tak, že vyberete možnost vytvořit nový prostředek. Klikněte na tlačítko **vytvořit prostředek** > **sítě** > **ExpressRoute**, jak je znázorněno na následujícím obrázku:

  ![Vytvoření okruhu ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Po kliknutí na **ExpressRoute**, zobrazí se vám **okruh ExpressRoute vytvořit** stránky. Při plnění hodnoty na této stránce Ujistěte se, že jste zadali správnou úroveň skladové položky (Standard nebo Premium) a data monitorování míry využívání model fakturace (neomezený počet nebo Metered).

  ![Nakonfigurujte úroveň skladové položky a měření dat.](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Úroveň** Určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat **standardní** získat standardní SKU nebo **Premium** pořídit si doplněk premium.
  * **Data monitorování míry využívání** Určuje typ fakturace. Můžete zadat **Metered** pro tarif podle objemu dat a **Unlimited** pro tarif s neomezenými daty. Všimněte si, že můžete změnit typ fakturace z **Metered** k **Unlimited**.
    > [!IMPORTANT] Nelze změnit typ z **Unlimited** k **Metered**.
  * **Umístění partnerského vztahu** je fyzického umístění, kde se partnerský vztah Microsoftu.

    > [!IMPORTANT]
    > Určuje umístění partnerského vztahu [fyzické umístění](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. Toto je **není** propojené s "Umístění" vlastnost, která odkazuje na zeměpisné oblasti, kde se nachází poskytovatel síťových prostředků Azure. Zatímco nesouvisí, je vhodné zvolit poskytovatel síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Zobrazit okruhů a vlastnosti
**Zobrazit všechny okruhy**

Můžete zobrazit všechny okruhy vytvořené tak, že vyberete **všechny prostředky** v levé nabídce.

![Zobrazení okruhy](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Zobrazení vlastností**

Vlastnosti okruhu můžete zobrazit tak, že ho vyberete. Na **přehled** stránky pro váš okruh, klíč služby se zobrazí v poli klíče služby. Musíte zkopírovat klíč služby pro váš okruh a předat ji dolů poskytovatele služeb k dokončení procesu zřizování. Klíč služby okruhů je specifický pro váš okruh.

![Zobrazení vlastností](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Poslat klíči služby svého poskytovatele připojení pro zřizování
Na této stránce **stav poskytovatele** poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. **Stav okruhu** poskytuje stav na straně Microsoftu. Další informace o zřizování stavy okruhu, najdete v článku [pracovních postupů](expressroute-workflows.md#expressroute-circuit-provisioning-states) článku.

Při vytváření nového okruhu ExpressRoute, je okruh v následujícím stavu:

Stav poskytovatele: Není zajišťováno<BR>
Stav okruhu: Povoleno

![Zahájení procesu zřizování](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Když probíhá proces jeho povolení pro vás poskytovatel připojení okruhu změní na následující stav:

Stav poskytovatele: Zřizování<BR>
Stav okruhu: Povoleno

Abyste mohli použít okruhu ExpressRoute musí být v následujícím stavu:

Stav poskytovatele: Zajištěno<BR>
Stav okruhu: Povoleno

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Pravidelně kontrolovat stav a stav okruhu klávesy
Můžete zobrazit vlastnosti okruhu, který vás zajímá, vyberte ho. Zkontrolujte **stav poskytovatele** a ujistěte se, že ji přesunula do **zřízená** předtím, než budete pokračovat.

![Stav okruhu a zprostředkovatele](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Vytvořte vlastní konfiguraci směrování
Podrobné pokyny najdete [konfigurace směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) článek k vytvoření a úprava okruhu partnerských vztahů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IP sítě VPN, např. MPLS), svého poskytovatele připojení, konfiguruje a spravuje směrování za vás.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Propojení virtuální sítě k okruhu ExpressRoute
V dalším kroku propojení virtuální sítě pro váš okruh ExpressRoute. Použití [propojení virtuálních sítí s okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) článek při práci s modelem nasazení Resource Manager.

## <a name="status"></a>Při získávání stavu okruhu ExpressRoute
Stav okruhu můžete zobrazit tak, že ji vyberete a zobrazení na stránce s přehledem. 

## <a name="modify"></a>Úprava okruhu ExpressRoute
Můžete upravit některé vlastnosti okruhu ExpressRoute bez dopadu na připojení. Můžete upravit šířku pásma, SKU, model fakturace a povolit klasické operace **konfigurace** stránky. Informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). 

Můžete provádět následující úlohy došlo k výpadku:

* Povolit nebo zakázat doplněk ExpressRoute Premium pro váš okruh ExpressRoute.
* Zvětšete šířku pásma okruhu ExpressRoute zadaný na portu je k dispozici kapacita.
  > [!IMPORTANT] Šířku pásma okruhu downgradu není podporován. 
* Změna plánu měření z *měření podle objemu dat* k *neomezená Data*.
  > [!IMPORTANT] Změna plánu měření z neomezená Data na měření podle objemu dat se nepodporuje.
* Můžete povolit nebo zakázat *povolit klasické operace*.
> [!IMPORTANT]
> Bude pravděpodobně nutné znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nemůže upgradovat okruh.
>
> I když můžete bez problémů upgradovat šířku pásma, nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrade šířky pásma je potřeba zrušit zřízení okruhu ExpressRoute a pak znova nezajistíte nového okruhu ExpressRoute.
> 
> Zákaz operace doplněk Premium může selhat, pokud používáte prostředky, které jsou větší než co je povolený pro standardní okruh.
> 
> 

Úprava okruhu ExpressRoute, klikněte na tlačítko **konfigurace**.

![Úprava okruhu](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute
Váš okruh ExpressRoute můžete odstranit tak, že vyberete **odstranit** ikonu. Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud tato operace se nezdaří, zkontrolujte, zda jsou propojeny žádné virtuální sítě k okruhu.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená** , musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Pokud poskytovatel služeb okruh zruší (poskytovatel služeb Stav zřizování je nastavena na **nezřízeno**), můžete odstranit okruh. Tím se zastaví účtování okruhu.

## <a name="next-steps"></a>Další postup
Po vytvoření váš okruh, pokračujte následující další kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](expressroute-howto-linkvnet-arm.md)
