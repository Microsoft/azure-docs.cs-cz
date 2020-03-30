---
title: 'Azure ExpressRoute: Přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu'
description: Tento článek ukazuje postup přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu na ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436836"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Přesun veřejného partnerského vztahu do partnerskému vztahu Microsoftu

Tento článek vám pomůže přesunout konfiguraci veřejného partnerského vztahu do partnerského vztahu Microsoftu bez prostojů. ExpressRoute podporuje použití partnerských vztahů s filtry tras pro služby Azure PaaS, jako je Azure Storage a Azure SQL Database. Teď potřebujete k přístupu ke službám PaaS a SaaS Microsoftu jenom jednu doménu směrování. Pomocí filtrů tras můžete selektivně inzerovat předpony služby PaaS do oblastí Azure, které chcete používat.

Veřejný partnerský vztah Azure má 1 IP adresu NAT přidruženou ke každé relaci Protokolu BGP. Partnerský vztah společnosti Microsoft umožňuje konfigurovat vlastní přidělení navádění na virtuální sítě a také použít filtry tras pro selektivní reklamy předponou. Veřejný partnerský vztah je jednosměrná služba, pomocí které je připojení vždy iniciovánz vaší sítě WAN do služeb Microsoft Azure. Služby Microsoft Azure nebudou moci inicializovat připojení do vaší sítě prostřednictvím této směrovací domény.

Po povolení veřejného partnerského vztahu se můžete připojit ke všem službám Azure. Neumožňujeme vám selektivně vybírat služby, na které inzerujeme trasy. Zatímco partnerský vztah Microsoftu je obousměrné připojení, kde lze iniciovat připojení ze služby Microsoft Azure spolu s vaší sítí WAN. Další informace o směrovacích doménách a partnerském vztahu naleznete v [tématu Okruhy ExpressRoute a směrovací domény](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Chcete-li se připojit k partnerské muzice Microsoftu, musíte nastavit a spravovat nat. Váš poskytovatel připojení může nastavit a spravovat nat jako spravovanou službu. Pokud plánujete přístup ke službám Azure PaaS a Azure SaaS v partnerské společnosti Microsoft, je důležité správně zvětšit fond IP adres NAT. Další informace o nat pro ExpressRoute naleznete požadavky [na NAT pro partnerský vztah Microsoftu](expressroute-nat.md#nat-requirements-for-microsoft-peering). Když se připojíte k Microsoftu prostřednictvím Azure ExpressRoute (partnerský vztah Microsoftu), máte několik odkazů na Microsoft. Jedno propojení je vaše stávající připojení k internetu a druhé je prostřednictvím ExpressRoute. Část provozu do Microsoftu může procházet přes internet, ale vracet se prostřednictvím ExpressRoute, nebo naopak.

![Obousměrné připojení](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Fond IP adres NAT inzerovaný Microsoftu nesmí být inzerovaný do internetu. Tím by došlo k přerušení připojení k jiným službám Microsoftu.

Odkazovat [na asymetrické směrování s více síťových cest](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) pro upozornění asymetrické směrování před konfigurací partnerského vztahu Microsoftu.

* Pokud používáte veřejný partnerský vztah a aktuálně máte pravidla sítě IP pro veřejné IP adresy, které se používají pro přístup k [Azure Storage](../storage/common/storage-network-security.md) nebo Azure [SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), musíte se ujistit, že fond IP ADRES NAT nakonfigurovaný s partnerským vztahem Microsoftu je zahrnut v seznamu veřejných IP adres pro účet úložiště Azure nebo účet Azure SQL.<br>
* Chcete-li přejít na partnerský vztah Microsoftu bez prostojů, použijte kroky v tomto článku v pořadí, v jakém jsou uvedeny.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Vytvoření partnerského vztahu Microsoftu

Pokud partnerský vztah společnosti Microsoft nebyl vytvořen, použijte k vytvoření partnerského vztahu Microsoftu některý z následujících článků. Pokud váš poskytovatel připojení nabízí služby spravované vrstvy 3, můžete požádat poskytovatele připojení o povolení partnerského vztahu Microsoftu pro váš okruh.

Pokud vrstvu 3 spravujete, je před pokračováním vyžadovány následující informace:

* Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.<br>
* Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.<br>
* Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Pro primární i sekundární odkazy je nutné použít stejné ID sítě VLAN.<br>
* Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.<br>
* Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.<br>
* Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.

* **Volitelné** - Číslo ASN zákazníka: Pokud inzerujete předpony, které nejsou registrovány na číslo AS partneringu, můžete zadat číslo AS, na které jsou registrovány.<br>
* **Volitelné** - Hash MD5, pokud se rozhodnete použít jeden.

Podrobné pokyny k povolení partnerského vztahu Microsoftu naleznete v následujících článcích:

* [Vytvoření partnerského vztahu Microsoftu pomocí portálu Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu pomocí Azure Powershellu](expressroute-howto-routing-arm.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu pomocí rozhraní příkazového příkazu Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Ověření partnerského vztahu Microsoftu je povoleno

Ověřte, zda je partnerský vztah microsoftu povolen a inzerované veřejné předpony jsou v nakonfigurovaném stavu.

* [Portál Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Konfigurace a připojení filtru trasy k obvodu

Ve výchozím nastavení nové partnerský vztah microsoftu neinzeruje žádné předpony, dokud není k okruhu připojen filtr trasy. Když vytvoříte pravidlo filtru trasy, můžete určit seznam komunit služeb pro oblasti Azure, které chcete využívat pro služby Azure PaaS. To vám poskytuje flexibilitu při filtrování tras podle vašeho požadavku, jak je znázorněno na následujícím snímku obrazovky:

![Sloučení veřejného partnerského vztahu](./media/how-to-move-peering/routefilter.jpg)

Nakonfigurujte filtry tras pomocí některého z následujících článků:

* [Konfigurace filtrů tras pro partnerský vztah Microsoftu pomocí portálu Azure](how-to-routefilter-portal.md)<br>
* [Konfigurace filtrů tras pro partnerský vztah Microsoftu pomocí Azure PowerShellu](how-to-routefilter-powershell.md)<br>
* [Konfigurace filtrů tras pro partnerský vztah Microsoftu pomocí rozhraní příkazového příkazu Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Odstranit veřejný partnerský vztah

Po ověření, že partnerský vztah Microsoftu je nakonfigurován a předpony, které chcete využívat, jsou správně inzerovány v partnerské společnosti Microsoft, můžete odstranit veřejný partnerský vztah. Chcete-li odstranit veřejný partnerský vztah, použijte některý z následujících článků:

* [Odstranění veřejného partnerského vztahu Azure pomocí Azure PowerShellu](about-public-peering.md#powershell)
* [Odstranění veřejného partnerského vztahu Azure pomocí příkazového příkazového příkazu](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Zobrazení partnerských vrstev
  
Seznam všech okruhů ExpressRoute a partnerských společností najdete na webu Azure Portal. Další informace naleznete [v tématu Zobrazení podrobností o partnerské partneři Microsoftu](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
