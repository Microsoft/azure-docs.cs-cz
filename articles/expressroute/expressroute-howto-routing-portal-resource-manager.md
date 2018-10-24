---
title: 'Postup konfigurace směrování (partnerského vztahu) pro ExpressRoute okruhu: Resource Manager: Azure | Dokumentace Microsoftu'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: cherylmc
ms.openlocfilehash: 7acfb71dd3edd798095472b8f863a658dccaa51b
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955180"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute

Tento článek pomůže při vytváření a správě konfigurace směrování pro okruh ExpressRoute v modelu nasazení Resource Manageru pomocí webu Azure portal. Můžete také zkontrolovat stav, update nebo delete a zrušit jejich zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejné partnerské vztahy](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 


## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený pro, abyste mohli spouštět rutiny v následujících částech.
* Pokud máte v plánu používat sdílený klíč s algoritmem MD5 hash, je potřeba použít na oba konce tunelu a omezit počet znaků, které mají maximálně 25.

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), svého poskytovatele připojení, konfiguruje a spravuje směrování za vás. 

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP, obraťte se na svého poskytovatele služeb.
> 
> 

Můžete nakonfigurovat jeden, dva nebo všechny tři partnerské vztahy (soukromý Azure, veřejný Azure a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrování domény a vztahy, naleznete v tématu [domény směrování ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Partnerský vztah Microsoftu

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu. Další informace najdete v tématu [Konfigurovat filtr tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nakonfigurujte okruh ExpressRoute. Než budete dál pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení partnerského vztahu Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

  ![seznam partnerský vztah Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
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

  ![Vyberte řádek partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Nakonfigurujte partnerský vztah Microsoftu. Následující obrázek ukazuje příklad konfigurace:

  ![Nakonfigurujte partnerský vztah Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Po zadání všech parametrů uložte konfiguraci.

  Pokud váš okruh dostane do "vyžaduje se ověření" stavu (jak je znázorněno na obrázku), musíte otevřít lístek podpory, abyste ukázali důkaz vlastnictví předpon našemu týmu podpory.

  ![Uložit konfiguraci partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

  Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Po konfigurace úspěšně přijatá, vypadá podobně jako na následujícím obrázku:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Chcete-li zobrazit podrobností partnerského vztahu Microsoftu

Vlastnosti veřejného partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat tak, že vyberete ikonu odstranit, jak je znázorněno na následujícím obrázku:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

  ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

  * Podsíť /30 pro primární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
  * Podsíť /30 pro sekundární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
  * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Vyberte řádek Azure privátní partnerský vztah, jak je znázorněno v následujícím příkladu:

  ![privátní](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Nakonfigurujte soukromý partnerský vztah. Následující obrázek ukazuje příklad konfigurace:

  ![Nakonfigurujte soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Po zadání všech parametrů uložte konfiguraci. Po konfigurace úspěšně přijatá, zobrazí se podobná následujícímu příkladu:

  ![uložit privátní partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>K zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

![Zobrazit soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu.

![Aktualizovat soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat tak, že vyberete ikonu odstranit, jak je znázorněno na následujícím obrázku:

> [!WARNING]
> Ujistěte se, že před spuštěním tohoto příkladu se odeberou všechny virtuální sítě a připojení ExpressRoute globální dosah. 
> 
> 

![Odstranění soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Veřejný partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci veřejného partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete dál pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

  ![seznam veřejného partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

  * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače. 
  * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * **Volitelné –** algoritmus hash MD5, pokud se rozhodnete použít.
3. Výběr Azure veřejného partnerského vztahu řádku, jak je znázorněno na následujícím obrázku:

  ![Vyberte řádek pro veřejný partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Nakonfigurujte veřejný partnerský vztah. Následující obrázek ukazuje příklad konfigurace:

  ![Nakonfigurujte veřejný partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Po zadání všech parametrů uložte konfiguraci. Po konfigurace úspěšně přijatá, zobrazí se podobná následujícímu příkladu:

  ![Uložit konfiguraci veřejného partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>K zobrazení podrobností veřejného partnerského vztahu Azure

Vlastnosti veřejného partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

![Zobrazit vlastnosti veřejného partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Chcete-li aktualizovat konfiguraci veřejného partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu.

![Vyberte řádek pro veřejný partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat tak, že vyberete ikonu odstranit, jak je znázorněno v následujícím příkladu:

![odstranění veřejného partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Další postup

Dalším krokem je [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
