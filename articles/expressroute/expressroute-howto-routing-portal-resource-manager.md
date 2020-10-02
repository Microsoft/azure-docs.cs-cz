---
title: 'Azure ExpressRoute: konfigurace partnerského vztahu'
description: Tento článek popisuje kroky pro vytvoření a zřízení privátního partnerského vztahu ExpressRoute a Microsoft. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: ccbd9645ac7d331c06e528298b3a45a184c6cc49
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652223"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování Azure Resource Managerho okruhu ExpressRoute (ARM) pomocí Azure Portal. Můžete také kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejné partnerské vztahy](about-public-peering.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy a partnerské vztahy Microsoftu (veřejný partnerský vztah Azure se pro nové okruhy už nepoužívá). Partnerské vztahy se dají konfigurovat v libovolném pořadí, ve kterém si zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu věnovaném [doménám směrování ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerském vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Aby bylo možné nakonfigurovat partnerské vztahy, musí být okruh ExpressRoute ve stavu zřizování a povoleno. 
* Pokud máte v úmyslu použít sdílený klíč nebo hodnotu hash MD5, nezapomeňte použít tuto hodnotu na obou stranách tunelu a omezit počet alfanumerických znaků na maximálně 25. Speciální znaky nejsou podporovány. 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), poskytovatel připojení nakonfiguruje a spravuje směrování za vás. 

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP se poraďte se svým poskytovatelem služeb.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras. Další informace najdete v tématu [Konfigurace filtru tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, zkontrolujte **stav poskytovatele** a ujistěte se, že je okruh zcela zřízený poskytovatelem připojení.

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil partnerský vztah Microsoftu. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte následujícími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

   [![Snímek obrazovky, který zobrazuje stránku s přehledem pro ukázkový okruh ExpressRoute, se červeným rámečkem, jehož stav poskytovatele je nastaven na hodnotu není zajištěno.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Stav poskytovatele okruhu: zřízený**

   [![Snímek obrazovky, který zobrazuje stránku s přehledem ukázkového okruhu ExpressRoute se červeným rámečkem, který zvýrazní stav poskytovatele nastaven na "zřízené".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. U primárních i sekundárních propojení musíte použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Volitelné –** Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na partnerské vztahy jako číslo, můžete zadat číslo AS, na které jsou registrované.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.
3. Můžete vybrat partnerský vztah, který chcete nakonfigurovat, jak je znázorněno v následujícím příkladu. Vyberte řádek partnerského vztahu Microsoftu.

   [![Vybrat řádek partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Vybrat řádek partnerského vztahu Microsoftu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Nakonfigurujte partnerský vztah Microsoftu. Po zadání všech parametrů **uložte** konfiguraci. Příklad konfigurace znázorňuje následující obrázek:

   ![Konfigurace peeringu s Microsoftem](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "vyžadováno ověření". 
>
> Pokud se zobrazí zpráva potřebná pro ověření, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace podle entity, která je uvedena jako vlastník předpony v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevřením lístku podpory, jak je uvedeno níže. 
>

   Pokud se váš okruh dostane do stavu "vyžadováno ověření", je nutné otevřít lístek podpory, který zobrazí důkaz vlastnictví předpon pro náš tým podpory. Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

   ![Vyžadování ověření – lístek podpory](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po úspěšném přijetí konfigurace se zobrazí podobné informace jako na následujícím obrázku:

   ![Stav partnerského vztahu: nakonfigurováno](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stav partnerského vztahu: nakonfigurováno")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Vlastnosti partnerského vztahu Microsoftu si můžete zobrazit tak, že vyberete řádek pro partnerský vztah.

[![Zobrazit vlastnosti partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Zobrazení vlastností")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah, který chcete upravit, a pak upravit vlastnosti partnerského vztahu a uložit změny.

![Vybrat řádek partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat kliknutím na ikonu Odstranit, jak je znázorněno na následujícím obrázku:

![Odstranit partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Privátní partnerský vztah Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. 

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil privátní partnerské vztahy Azure. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud vám ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu pokračujte dalšími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

   [![Snímek obrazovky se stránkou s přehledem ukázkového okruhu ExpressRoute se červeným rámečkem, který je nastaven na hodnotu nezřízeno.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Stav poskytovatele okruhu: zřízený**

   [![Snímek obrazovky se stránkou s přehledem pro ukázkový okruh ExpressRoute se červeným rámečkem, který má na stav poskytovatele hodnotu zřízený.](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. U primárních i sekundárních propojení musíte použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS s výjimkou čísla od 65515 do 65520 (včetně).
   * Při nastavování privátního partnerského vztahu musíte směrovat trasy z místního hraničního směrovače do Azure přes protokol BGP.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.
3. Vyberte řádek privátního partnerského vztahu Azure, jak je znázorněno v následujícím příkladu:

   [![Výběr řádku privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Výběr řádku privátního partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Nakonfigurujte soukromý partnerský vztah. Po zadání všech parametrů **uložte** konfiguraci.

   ![konfigurace privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po úspěšném přijetí konfigurace se zobrazí něco podobného jako v následujícím příkladu:

   ![uložený soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

[![Zobrazit vlastnosti privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Zobrazit vlastnosti privátního partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu. Po aktualizaci uložte změny.

![aktualizace privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat tak, že vyberete ikonu Odstranit, jak je znázorněno na následujícím obrázku:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 
> 

![Odstranit soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Další kroky

Další krok [propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
