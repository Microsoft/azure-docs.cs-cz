---
title: 'Azure ExpressRoute: přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu'
description: Tento článek ukazuje postup přesunutí váš veřejný partnerský vztah Microsoftu na ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436836"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Přesunout veřejného partnerského vztahu k partnerskému vztahu Microsoftu

Tento článek pomáhá přesunout konfiguraci veřejného partnerského vztahu Microsoftu partnerského vztahu bez výpadků. ExpressRoute podporuje použití partnerských vztahů s filtry tras pro služby Azure PaaS, jako je Azure Storage a Azure SQL Database. Teď potřebujete k přístupu ke službám PaaS a SaaS Microsoftu jenom jednu doménu směrování. Pomocí filtrů tras můžete selektivně inzerovat předpony služby PaaS do oblastí Azure, které chcete používat.

Veřejný partnerský vztah Azure má 1 IP adresu NAT přidruženou ke každé relaci protokolu BGP. Partnerský vztah Microsoftu umožňuje nakonfigurovat vlastní přidělení NAT a také použít filtry tras pro inzerování selektivních předpon. Veřejný partnerský vztah je jednosměrná služba, pomocí které se připojení vždy zahajuje z vaší sítě WAN až po Microsoft Azure služby. Služby Microsoft Azure, nebude možné navázat připojení do sítě přes tuto doménu směrování.

Po povolení veřejného partnerského vztahu můžete připojit ke všem službám Azure. Jsme není umožňují selektivně vyberte služby, u kterých jsme inzerování tras do. Zatímco partnerský vztah Microsoftu je obousměrné připojení, které umožňuje iniciovat připojení z Microsoft Azure služby spolu s vaší sítí WAN. Další informace o doménách směrování a partnerských vztahů najdete v tématu [okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

## <a name="before"></a>Než začnete

Pro připojení k partnerskému vztahu Microsoftu, budete muset nastavit a spravovat NAT. Váš poskytovatel připojení může nastavit a spravovat NAT jako spravovanou službu. Pokud máte v úmyslu přístup k Azure PaaS a Azure SaaS services na partnerský vztah Microsoftu, je důležité pro nastavení velikosti fondu adres IP pro NAT správně. Další informace o NAT pro ExpressRoute najdete v článku [požadavky NAT pro partnerský vztah Microsoftu](expressroute-nat.md#nat-requirements-for-microsoft-peering). Když se připojíte k Microsoftu prostřednictvím Azure ExpressRoute (partnerský vztah Microsoftu), máte několik odkazů na Microsoft. Jedno propojení je vaše stávající připojení k internetu a druhé je prostřednictvím ExpressRoute. Část provozu do Microsoftu může procházet přes internet, ale vracet se prostřednictvím ExpressRoute, nebo naopak.

![Obousměrné připojení](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Fond IP adres NAT inzerovaný Microsoftu nesmí být inzerovaný do internetu. Tím by došlo k přerušení připojení k jiným službám Microsoftu.

Před konfigurací partnerského vztahu Microsoftu se můžete podívat na [asymetrické směrování s několika síťovými cestami](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) , které vám umožní mít upozornění asymetrického směrování.

* Pokud používáte veřejný partnerský vztah a aktuálně máte pravidel sítě protokolu IP pro veřejné IP adresy, které se používají pro přístup k [služby Azure Storage](../storage/common/storage-network-security.md) nebo [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), budete potřebovat, abyste měli jistotu, že fond IP adres NAT nakonfigurovat s Microsoftem partnerského vztahu je součástí seznamu veřejné IP adresy pro účet služby Azure storage nebo účet služby Azure SQL.<br>
* Aby bylo možné přesunout do partnerského vztahu bez výpadků Microsoftu, postupujte podle kroků v tomto článku v pořadí, ve kterém jsou uvedeny.

## <a name="create"></a>1. vytvoření partnerského vztahu Microsoftu

Pokud ještě nebyl vytvořen partnerský vztah Microsoftu, použijte některý z následujících článků vytvoření partnerského vztahu Microsoftu. Pokud spravované nabídek poskytovatele připojení vrstvy 3 služby, požádejte poskytovatele připojení, povolte partnerského vztahu Microsoftu pro váš okruh.

Pokud je vrstva 3 spravovaná pomocí, před pokračováním je nutné provést následující informace:

* Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.<br>
* Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě bude přiřadit nedodržíte první IP adresu směrovače jak společnost Microsoft používá druhou IP použitelný pro jeho směrovače.<br>
* Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Odkazy na primární a sekundární musí používat stejné ID sítě VLAN.<br>
* Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.<br>
* Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.<br>
* Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.

* **Volitelné** – ASN zákazníka: Pokud vytváříte předpony, které nejsou registrované k partnerským vztahům jako číslo, můžete zadat číslo as, na které jsou registrované.<br>
* **Volitelné** – hodnota hash MD5, pokud se rozhodnete ji použít.

Podrobné pokyny k povolení partnerského vztahu Microsoftu najdete v následujících článcích:

* [Vytvoření partnerského vztahu Microsoftu pomocí webu Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu s využitím Azure Powershellu](expressroute-howto-routing-arm.md#msft)<br>
* [Vytvoření partnerského vztahu Microsoftu pomocí rozhraní příkazového řádku Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. ověření partnerského vztahu Microsoftu je povolené.

Ověřte, že partnerského vztahu Microsoftu je povoleno a inzerované veřejné předpony jsou ve stavu nakonfigurované.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. konfigurace a připojení filtru tras k okruhu

Ve výchozím nastavení nový partnerský vztah Microsoftu neinzeruje žádné předpony, dokud se do okruhu nepřipojí filtr tras. Když vytvoříte pravidlo filtru tras, můžete zadat seznam komunit služby pro oblasti Azure, které chcete využívat pro služby Azure PaaS. To vám umožní pružně filtrovat trasy podle vašich požadavků, jak je znázorněno na následujícím snímku obrazovky:

![Sloučit veřejného partnerského vztahu](./media/how-to-move-peering/routefilter.jpg)

Konfigurace filtrů směrování pomocí některého z následujících článků:

* [Konfigurace filtrů směrování pro partnerský vztah Microsoftu s využitím webu Azure portal](how-to-routefilter-portal.md)<br>
* [Konfigurace filtrů směrování pro partnerský vztah Microsoftu s využitím Azure Powershellu](how-to-routefilter-powershell.md)<br>
* [Konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Odstraňte veřejný partnerský vztah

Po ověření, že je nakonfigurován partnerský vztah Microsoftu a předpony, které chcete využívat jsou správně inzerované na partnerský vztah Microsoftu, pak můžete odstranit veřejný partnerský vztah. K odstranění veřejného partnerského vztahu, použijte některou z následujících článcích:

* [Odstranění veřejného partnerského vztahu Azure pomocí Azure Powershellu](about-public-peering.md#powershell)
* [Odstranění veřejného partnerského vztahu Azure pomocí rozhraní příkazového řádku](about-public-peering.md#cli)
  
## <a name="view"></a>5. zobrazení partnerských vztahů
  
Zobrazí se seznam všech okruhy ExpressRoute a partnerské vztahy na webu Azure Portal. Další informace najdete v tématu [podrobností partnerského vztahu Microsoftu zobrazení](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
