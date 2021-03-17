---
title: 'Azure ExpressRoute: požadavky na překlad adres (NAT) pro okruhy'
description: Tato stránka obsahuje podrobné požadavky pro konfiguraci a správu překladu adres (NAT) pro okruhy ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569641"
---
# <a name="expressroute-nat-requirements"></a>Požadavky na překlad adres (NAT) služby ExpressRoute
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, budete muset nastavit a spravovat překlady adres (NAT). Někteří poskytovatelé připojení nabízejí nastavení a správu NAT jako spravovanou službu. Zeptejte se svého poskytovatele připojení, jestli tuto službu nabízí. Pokud ne, je nutné splnit požadavky popsané dál. 

Přehled různých domén směrování získáte na stránce [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md). Pokud chcete splnit požadavky na veřejné IP adresy pro veřejný partnerský vztah Azure a partnerský vztah Microsoftu, doporučujeme mezi vaší sítí a Microsoftem zřídit překlad adres NAT. Tato část poskytuje podrobný popis infrastruktury NAT, kterou je potřeba nastavit.

## <a name="nat-requirements-for-microsoft-peering"></a>Požadavky NAT pro partnerský vztah Microsoftu
Cesta partnerského vztahu Microsoftu vám umožní připojit se ke cloudovým službám Microsoftu, které nejsou podporované prostřednictvím cesty veřejného partnerského vztahu Azure. Seznam služeb zahrnuje Microsoft 365 služby, jako je Exchange Online, SharePoint Online a Skype pro firmy. Microsoft v partnerském vztahu Microsoftu očekává podporu obousměrného připojení. Přenosy určené cloudovým službám Microsoftu musí být před jejich vstupem do sítě Microsoftu platné IPv4 adresy přeložené pomocí překladu SNAT. Přenosy určené do vaší sítě z cloudových služeb Microsoftu musí být před vstupem do vaší sítě z internetu přeložené pomocí překladu SNAT, aby se zabránilo [asymetrickému směrování](expressroute-asymmetric-routing.md). Následující obrázek poskytuje podrobný přehled toho, jak by se měl nastavit NAT pro partnerský vztah Microsoftu.

![Diagram vysoké úrovně, jak by se měl nastavit překlad adres (NAT) pro partnerský vztah Microsoftu](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Přenosy pocházející z vaší sítě určené do Microsoftu
* Je nutné zajistit, aby provoz vstupující do cesty partnerského vztahu Microsoftu měl platnou veřejnou IPv4 adresu. Microsoft musí být schopný ověřit vlastníka fondu IPv4 adres NAT proti regionálnímu registru RIR nebo registru IRR. Kontrola bude provedená na základě čísla AS s partnerským vztahem a adres IP použitých pro NAT. Informace o registrech směrování najdete na stránce [Požadavky na směrování služby ExpressRoute](expressroute-routing.md).
* Adresy IP použité pro nastavení veřejného partnerského vztahu Azure a jiné okruhy ExpressRoute nesmí být inzerované Microsoftu prostřednictvím relace protokolu BGP. Neexistuje žádné omezení délky předpon adres IP pro NAT inzerovaných prostřednictvím tohoto partnerského vztahu.
  
  > [!IMPORTANT]
  > Fond IP adres NAT inzerovaný Microsoftu nesmí být inzerovaný do internetu. Tím by došlo k přerušení připojení k jiným službám Microsoftu.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Přenosy pocházející z Microsoftu určené do vaší sítě
* Některé scénáře vyžadují, aby Microsoft zahájil připojení ke koncovým bodům služby hostovaným v rámci vaší sítě. Typický příklad tohoto scénáře by byl připojením k serverům ADFS hostovaným ve vaší síti z Microsoft 365. V takových případech musíte nechat uniknout příslušné předpony z vaší sítě do partnerského vztahu Microsoftu. 
* Přenosy z Microsoftu je nutné před vstupem z internetu do koncových bodů služby v rámci vaší sítě přeložit pomocí překladu SNAT, aby se zabránilo [asymetrickému směrování](expressroute-asymmetric-routing.md). Požadavky **a odpovědi** s cílovou IP adresou, která odpovídá trase obdržené prostřednictvím ExpressRoute, se vždy odešlou přes ExpressRoute. K asymetrickému směrování dochází v případě, že je požadavek obdržen přes internet a odpověď odeslaná přes ExpressRoute. Překlad příchozích přenosů z Microsoftu před vstupem z internetu do vaší sítě pomocí překladu SNAT tento problém řeší tím, že vynutí odeslání přenosu s odpovědí zpět do internetu.

![Asymetrické směrování s ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Požadavky NAT pro veřejný partnerský vztah Azure

> [!NOTE]
> Veřejný partnerský vztah Azure není pro nové okruhy k dispozici.
> 

Cesta veřejného partnerského vztahu Azure vám umožňuje připojení ke všem službám, které jsou hostovány v Azure, přes jejich veřejné IP adresy. Sem patří služby uvedené v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) a všechny služby hostované nezávislými dodavateli softwaru v Microsoft Azure. 

> [!IMPORTANT]
> Připojení ke službám Microsoft Azure ve veřejném partnerském vztahu je vždycky iniciováno z vaší sítě do sítě Microsoftu. Proto relace nelze inicializovat ze služeb Microsoft Azure směrem k vaší síti přes ExpressRoute. Pokud dojde k takovému pokusu, pakety odeslané na tyto inzerované IP adresy použijí internet místo ExpressRoute.
> 

Přenosy určené do Microsoft Azure ve veřejném partnerském vztahu musí být před jejich vstupem do sítě Microsoftu platné IPv4 adresy přeložené pomocí překladu SNAT. Následující obrázek poskytuje základní přehled o způsobu nastavení překladu síťových adres NAT, aby splňoval předchozí požadavek.

![Diagram vysoké úrovně, jak může být nastavení překladu adres (NAT) před jejich vstupem na platné veřejné IPv4 adresy před tím, než vstoupí do sítě Microsoftu.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Inzerování tras a fondu IP adres NAT
Je nutné zajistit, aby provoz vstupující do cesty veřejného partnerského vztahu Azure měl platnou veřejnou IPv4 adresu. Microsoft musí být schopný ověřit vlastnictví fondu IPv4 adres NAT proti regionálnímu registru RIR nebo registru IRR. Kontrola bude provedená na základě čísla AS s partnerským vztahem a adres IP použitých pro NAT. Informace o registrech směrování najdete na stránce [Požadavky na směrování služby ExpressRoute](expressroute-routing.md).

Neexistují žádná omezení délky předpon adres IP pro NAT inzerovaných prostřednictvím tohoto partnerského vztahu. Musíte monitorovat fond NAT a zajistit, že nemáte nedostatek relací NAT.

> [!IMPORTANT]
> Fond IP adres NAT inzerovaný Microsoftu nesmí být inzerovaný do internetu. Tím by došlo k přerušení připojení k jiným službám Microsoftu.
> 
> 

## <a name="next-steps"></a>Další kroky
* Přečtěte si požadavky pro [směrování](expressroute-routing.md) a [technologii QoS](expressroute-qos.md).
* Informace o pracovním postupu najdete v tématu [Pracovní postupy zřizování okruhů ExpressRoute a stavy okruhu](expressroute-workflows.md).
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování](expressroute-howto-routing-portal-resource-manager.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)

