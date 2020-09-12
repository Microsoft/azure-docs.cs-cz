---
title: 'Azure ExpressRoute: přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu'
description: V tomto článku se dozvíte, jak přesunout veřejné partnerské vztahy na partnerský vztah Microsoftu na ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 5671be5e54a9bb789e349a4da6d0f1809807d974
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401414"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Přesun veřejného partnerského vztahu do partnerskému vztahu Microsoftu

Tento článek vám pomůže přesunout konfiguraci veřejného partnerského vztahu do partnerského vztahu Microsoftu bez výpadku. ExpressRoute podporuje použití partnerských vztahů s filtry tras pro služby Azure PaaS, jako je Azure Storage a Azure SQL Database. Teď potřebujete k přístupu ke službám PaaS a SaaS Microsoftu jenom jednu doménu směrování. Pomocí filtrů tras můžete selektivně inzerovat předpony služby PaaS do oblastí Azure, které chcete používat.

Veřejný partnerský vztah Azure má 1 IP adresu NAT přidruženou ke každé relaci protokolu BGP. Partnerský vztah Microsoftu umožňuje nakonfigurovat vlastní přidělení NAT a také použít filtry tras pro inzerování selektivních předpon. Veřejný partnerský vztah je jednosměrná služba, pomocí které se připojení vždy zahajuje z vaší sítě WAN až po Microsoft Azure služby. Služby Microsoft Azure Services nebudou moct iniciovat připojení k síti přes tuto doménu směrování.

Po povolení veřejného partnerského vztahu se můžete připojit ke všem službám Azure. Neumožňujeme selektivně vybrat služby, pro které budeme inzerovat trasy. Zatímco partnerský vztah Microsoftu je obousměrné připojení, které umožňuje iniciovat připojení z Microsoft Azure služby spolu s vaší sítí WAN. Další informace o doménách směrování a partnerských vztahů najdete v tématu [okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Pokud se chcete připojit k partnerskému vztahu Microsoftu, musíte nastavit a spravovat překlad adres (NAT). Poskytovatel připojení může nastavit a spravovat překlad adres (NAT) jako spravovanou službu. Pokud plánujete přístup k Azure PaaS a službám Azure SaaS na partnerském vztahu Microsoftu, je důležité správně změnit velikost fondu IP adres NAT. Další informace o překladu adres (NAT) pro ExpressRoute najdete v tématu [požadavky na překlad adres (NAT) pro partnerský vztah Microsoftu](expressroute-nat.md#nat-requirements-for-microsoft-peering). Když se připojíte k Microsoftu prostřednictvím Azure ExpressRoute (partnerský vztah Microsoftu), máte několik odkazů na Microsoft. Jedno propojení je vaše stávající připojení k internetu a druhé je prostřednictvím ExpressRoute. Část provozu do Microsoftu může procházet přes internet, ale vracet se prostřednictvím ExpressRoute, nebo naopak.

![Obousměrné připojení](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Fond IP adres NAT inzerovaný Microsoftu nesmí být inzerovaný do internetu. Tím by došlo k přerušení připojení k jiným službám Microsoftu.

Před konfigurací partnerského vztahu Microsoftu se můžete podívat na [asymetrické směrování s několika síťovými cestami](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) , které vám umožní mít upozornění asymetrického směrování.

* Pokud používáte veřejný partnerský vztah a aktuálně máte pravidla sítě IP pro veřejné IP adresy, které se používají pro přístup k [Azure Storage](../storage/common/storage-network-security.md) nebo [Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md), musíte se UJISTIT, že fond IP adres NAT nakonfigurovaný s partnerským vztahem Microsoftu je obsažený v seznamu veřejných IP adres pro účet Azure Storage nebo účet Azure SQL.<br>
* Aby bylo možné přejít na partnerský vztah Microsoftu bez výpadků, postupujte podle kroků v tomto článku v uvedeném pořadí.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. vytvoření partnerského vztahu Microsoftu

Pokud se partnerský vztah Microsoftu nevytvořil, vytvořte partnerský vztah Microsoftu pomocí některého z následujících článků. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro váš okruh povolil partnerský vztah Microsoftu.

Pokud je vrstva 3 spravovaná pomocí, před pokračováním je nutné provést následující informace:

* Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.<br>
* Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první IP adresu, kterou pro svůj směrovač používá druhá dostupná IP adresa.<br>
* Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. U primárních i sekundárních propojení musíte použít stejné ID sítě VLAN.<br>
* Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.<br>
* Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.<br>
* Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.

* **Volitelné** – ASN zákazníka: Pokud vytváříte předpony, které nejsou registrované k partnerským vztahům jako číslo, můžete zadat číslo as, na které jsou registrované.<br>
* **Volitelné** – hodnota hash MD5, pokud se rozhodnete ji použít.

Podrobné pokyny k povolení partnerského vztahu Microsoftu najdete v následujících článcích:

* [Vytvoření partnerského vztahu Microsoftu pomocí Azure Portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu pomocí Azure PowerShellu](expressroute-howto-routing-arm.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu pomocí Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. ověření partnerského vztahu Microsoftu je povolené.

Ověřte, že je povolen partnerský vztah Microsoftu a že inzerované veřejné předpony jsou v nakonfigurovaném stavu.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. konfigurace a připojení filtru tras k okruhu

Ve výchozím nastavení nový partnerský vztah Microsoftu neinzeruje žádné předpony, dokud se do okruhu nepřipojí filtr tras. Když vytvoříte pravidlo filtru tras, můžete zadat seznam komunit služby pro oblasti Azure, které chcete využívat pro služby Azure PaaS. To vám umožní pružně filtrovat trasy podle vašich požadavků, jak je znázorněno na následujícím snímku obrazovky:

![Sloučit veřejné partnerské vztahy](./media/how-to-move-peering/routefilter.jpg)

Filtry tras nakonfigurujte pomocí některého z následujících článků:

* [Konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí Azure Portal](how-to-routefilter-portal.md)<br>
* [Konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurace filtrů tras pro partnerský vztah Microsoftu pomocí Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Odstraňte veřejný partnerský vztah

Po ověření, že je nakonfigurován partnerský vztah Microsoftu, a předpony, které chcete spotřebovat, jsou správně inzerovány v partnerském vztahu Microsoftu, a pak můžete veřejné partnerské vztahy odstranit. K odstranění veřejného partnerského vztahu použijte některý z následujících článků:

* [Odstranění veřejného partnerského vztahu Azure pomocí Azure PowerShell](about-public-peering.md#powershell)
* [Odstranění veřejného partnerského vztahu Azure pomocí rozhraní příkazového řádku](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. zobrazení partnerských vztahů
  
Zobrazí se seznam všech okruhů ExpressRoute a partnerských vztahů v Azure Portal. Další informace najdete v tématu [zobrazení podrobností partnerského vztahu Microsoftu](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
