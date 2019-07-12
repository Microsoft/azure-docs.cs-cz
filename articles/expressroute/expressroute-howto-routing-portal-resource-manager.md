---
title: 'Konfigurace partnerského vztahu pro okruh – ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tento článek popisuje kroky pro vytvoření a zřízení ExpressRoute privátní partnerský vztah Microsoftu. Tento článek také ukazuje, jak zkontrolovat stav, aktualizace nebo odstranění partnerských vztahů pro okruh.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 08d8103c4b35148a87d347e31b11c7c8c968598b
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622332"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute

Tento článek pomůže při vytváření a správě konfigurace směrování pro okruh ExpressRoute Azure Resource Manageru (ARM), pomocí webu Azure portal. Můžete také zkontrolovat stav, update nebo delete a zrušit jejich zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejné partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Můžete nakonfigurovat soukromý Azure a partnerský vztah Microsoftu pro okruh ExpressRoute (veřejný partnerský vztah Azure je zastaralé pro nové okruhy). Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrování domény a vztahy, naleznete v tématu [o okruzích a partnerské vztahy](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Abyste mohli nakonfigurovat partnerský vztah (s), musí být okruh ExpressRoute ve stavu zřízený a povolený. 
* Pokud máte v plánu používat sdílený klíč s algoritmem MD5 hash, je potřeba použít na oba konce tunelu a omezit počet alfanumerických znaků na maximálně 25. Speciální znaky se nepodporují. 

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

1. Nakonfigurujte okruh ExpressRoute. Zkontrolujte **stav poskytovatele** zajistit, že je okruh plně zřízený poskytovatelem připojení než budete pokračovat dál.

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení partnerského vztahu Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, pokračujte však tyto kroky.

   **Okruh - stav poskytovatele: Není zajišťováno**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Stav poskytovatele: Není zajišťováno")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Okruh - stav poskytovatele: zřízené**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Stav poskytovatele = zřízená")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Je nutné zadat seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Volitelné –** zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, do kterého jsou registrované.
   * Název registru směrování: Můžete zadat RIR / IRR, kde AS jsou registrované předpony a číslo.
   * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Můžete vybrat partnerský vztah, který chcete nakonfigurovat, jak je znázorněno v následujícím příkladu. Vyberte řádek partnerského vztahu Microsoftu.

   [![Vyberte řádek partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "vyberte řádek partnerského vztahu Microsoftu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Nakonfigurujte partnerský vztah Microsoftu. **Uložit** konfigurace po zadání všech parametrů. Následující obrázek ukazuje příklad konfigurace:

   ![Nakonfigurujte partnerský vztah Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Pokud váš okruh dostane do "vyžaduje se ověření" stavu, je nutné otevřít lístek podpory, abyste ukázali důkaz vlastnictví předpon našemu týmu podpory. Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

   ![Vyžaduje se ověření - lístku podpory](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po konfigurace úspěšně přijatá, zobrazí se vám něco podobně jako na následujícím obrázku:

   ![Stav partnerského vztahu: Nakonfigurované](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "stav partnerského vztahu: Nakonfigurované")]

### <a name="getmsft"></a>Chcete-li zobrazit podrobností partnerského vztahu Microsoftu

Můžete zobrazit vlastnosti partnerského vztahu tak, že vyberete řádek pro partnerský vztah Microsoftu.

[![Zobrazit vlastnosti partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "zobrazit vlastnosti")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah, který chcete upravit, upravit vlastnosti partnerského vztahu a uložit provedené změny.

![Vyberte řádek pro partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat kliknutím na ikonu odstranit, jak je znázorněno na následujícím obrázku:

![Odstranit partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. 

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Ale pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, pokračujte v dalších krocích.

   **Okruh - stav poskytovatele: Není zajišťováno**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Stav poskytovatele Nezřízeno. =")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Okruh - stav poskytovatele: zřízené**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Stav poskytovatele = zřízené")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Podsíť /30 pro sekundární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Můžete použít soukromé číslo AS pro tento partnerský vztah s výjimkou číslo od 65515 do 65520, (včetně).
   * Trasy z vaší místní hraniční směrovač musí Inzerovat do Azure přes protokol BGP při nastavování soukromého partnerského vztahu.
   * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Výběr Azure privátního partnerského vztahu řádku, jak je znázorněno v následujícím příkladu:

   [![Vyberte řádek privátní partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "vyberte řádek privátní partnerský vztah")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Nakonfigurujte soukromý partnerský vztah. **Uložit** konfigurace po zadání všech parametrů.

   ![Nakonfigurujte soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po konfigurace úspěšně přijatá, zobrazí se podobná následujícímu příkladu:

   ![uložit soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>K zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

[![Zobrazit vlastnosti soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "zobrazit vlastnosti soukromého partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu. Po aktualizaci, uložte provedené změny.

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
> Veřejný partnerský vztah Azure pro nové okruhy je zastaralá. Další informace najdete v tématu [partnerský vztah ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>K zobrazení podrobností veřejného partnerského vztahu Azure

Zobrazte vlastnosti veřejného partnerského vztahu Azure výběrem partnerského vztahu.

### <a name="updatepublic"></a>Chcete-li aktualizovat konfiguraci veřejného partnerského vztahu Azure

Vyberte řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu.

### <a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Odeberte tak, že vyberete ikonu Odstranit konfiguraci partnerského vztahu.

## <a name="next-steps"></a>Další postup

Dalším krokem je [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
