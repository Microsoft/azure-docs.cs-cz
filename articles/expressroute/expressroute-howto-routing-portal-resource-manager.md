---
title: 'Azure ExpressRoute: konfigurace partnerského vztahu'
description: Tento článek popisuje kroky pro vytvoření a zřízení privátního partnerského vztahu ExpressRoute a Microsoft. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.openlocfilehash: 5fb728cccd77d0cefd10c124cb7215dc3b880fe3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083527"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování Azure Resource Managerho okruhu ExpressRoute (ARM) pomocí Azure Portal. Můžete také zkontrolovat stav, update nebo delete a zrušit jejich zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejné partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy Azure a partnerský vztah Microsoftu (veřejný partnerský vztah Azure se pro nové okruhy už nepoužívá). Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu [informace o okruhech a partnerských](expressroute-circuit-peerings.md)vztazích.

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Aby bylo možné nakonfigurovat partnerské vztahy, musí být okruh ExpressRoute ve stavu zřizování a povoleno. 
* Pokud máte v úmyslu použít sdílený klíč nebo hodnotu hash MD5, nezapomeňte použít tuto hodnotu na obou stranách tunelu a omezit počet alfanumerických znaků na maximálně 25. Speciální znaky nejsou podporovány. 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), svého poskytovatele připojení, konfiguruje a spravuje směrování za vás. 

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP, obraťte se na svého poskytovatele služeb.
> 
> 

## <a name="msft"></a>Partnerský vztah Microsoftu

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu. Další informace najdete v tématu [Konfigurovat filtr tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, zkontrolujte **stav poskytovatele** a ujistěte se, že je okruh zcela zřízený poskytovatelem připojení.

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení partnerského vztahu Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte následujícími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Stav poskytovatele okruhu: zřízený**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Volitelné –** zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, do kterého jsou registrované.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Můžete vybrat partnerský vztah, který chcete nakonfigurovat, jak je znázorněno v následujícím příkladu. Vyberte řádek partnerského vztahu Microsoftu.

   [![Vybrat řádek partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Vybrat řádek partnerského vztahu Microsoftu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Nakonfigurujte partnerský vztah Microsoftu. Po zadání všech parametrů **uložte** konfiguraci. Příklad konfigurace znázorňuje následující obrázek:

   ![Nakonfigurujte partnerský vztah Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "vyžadováno ověření". 
>
> Pokud se zobrazí zpráva potřebná pro ověření, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace podle entity, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření pomocí Otevřete lístek podpory, jak je znázorněno níže. 
>

   Pokud se váš okruh dostane do stavu "vyžadováno ověření", je nutné otevřít lístek podpory, který zobrazí důkaz vlastnictví předpon pro náš tým podpory. Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

   ![Vyžadování ověření – lístek podpory](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po úspěšném přijetí konfigurace se zobrazí podobné informace jako na následujícím obrázku:

   ![Stav partnerského vztahu: nakonfigurováno](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stav partnerského vztahu: nakonfigurováno")]

### <a name="getmsft"></a>Chcete-li zobrazit podrobností partnerského vztahu Microsoftu

Vlastnosti partnerského vztahu Microsoftu si můžete zobrazit tak, že vyberete řádek pro partnerský vztah.

[![Zobrazit vlastnosti partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Zobrazení vlastností")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah, který chcete upravit, a pak upravit vlastnosti partnerského vztahu a uložit změny.

![Vybrat řádek partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat kliknutím na ikonu Odstranit, jak je znázorněno na následujícím obrázku:

![Odstranit partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. 

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud vám ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu pokračujte dalšími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Stav poskytovatele okruhu: zřízený**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Podsíť /30 pro sekundární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS s výjimkou čísla od 65515 do 65520 (včetně).
   * Při nastavování privátního partnerského vztahu musíte směrovat trasy z místního hraničního směrovače do Azure přes protokol BGP.
   * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Vyberte řádek privátního partnerského vztahu Azure, jak je znázorněno v následujícím příkladu:

   [![Výběr řádku privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Výběr řádku privátního partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Nakonfigurujte soukromý partnerský vztah. Po zadání všech parametrů **uložte** konfiguraci.

   ![Nakonfigurujte soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po konfigurace úspěšně přijatá, zobrazí se podobná následujícímu příkladu:

   ![uložený soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>K zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

[![Zobrazit vlastnosti privátního partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Zobrazit vlastnosti privátního partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu. Po aktualizaci uložte změny.

![Aktualizovat soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat tak, že vyberete ikonu odstranit, jak je znázorněno na následujícím obrázku:

> [!WARNING]
> Ujistěte se, že před spuštěním tohoto příkladu se odeberou všechny virtuální sítě a připojení ExpressRoute globální dosah. 
> 
> 

![Odstranění soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Veřejný partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci veřejného partnerského vztahu pro okruh ExpressRoute.

> [!Note]
> Veřejné partnerské vztahy Azure se pro nové okruhy zastaraly. Další informace najdete v tématu [partnerský vztah ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>K zobrazení podrobností veřejného partnerského vztahu Azure

Výběrem partnerského vztahu zobrazíte vlastnosti veřejného partnerského vztahu Azure.

### <a name="updatepublic"></a>Chcete-li aktualizovat konfiguraci veřejného partnerského vztahu Azure

Vyberte řádek pro partnerský vztah a pak upravte vlastnosti partnerského vztahu.

### <a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Odeberte konfiguraci partnerského vztahu tak, že vyberete ikonu Odstranit.

## <a name="next-steps"></a>Další kroky

Dalším krokem je [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
