---
title: Vnořené profily Traffic Manageru v Azure
titleSuffix: Azure Traffic Manager
description: Tento článek vysvětluje funkci Vnořené profily ve službě Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938569"
---
# <a name="nested-traffic-manager-profiles"></a>Vnořené profily Traffic Manageru

Traffic Manager obsahuje řadu metod směrování provozu, které umožňují řídit, jak Traffic Manager volí koncový bod by měl přijímat provoz od každého koncového uživatele. Další informace naleznete v tématu [Traffic Manager traffic-routing methods](traffic-manager-routing-methods.md).

Každý profil traffic manageru určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují složitější směrování provozu než směrování poskytované jedním profilem traffic manageru. Profily Traffic Manageru můžete vnořit a kombinovat tak výhody více než jedné metody směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manageru pro podporu větších a složitějších nasazení aplikací.

Následující příklady ilustrují, jak používat vnořené profily Traffic Manageru v různých scénářích.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Příklad 1: Kombinace směrování provozu "Výkon" a Vážený provoz

Předpokládejme, že jste nasadili aplikaci v následujících oblastech Azure: Západní USA, Západní Evropa a Východní Asie. K distribuci provozu do oblasti, která je uživateli nejblíže, se používá metoda směrování provozu Traffic Manager.

![Jeden profil traffic manageru][4]

Nyní předpokládejme, že chcete otestovat aktualizaci služby před jeho širším rozšířením. Chcete použít "vážený" traffic-routing metoda přímé malé procento provozu na testovací nasazení. Testovací nasazení nastavíte společně s existujícím nasazením v produkčním prostředí v západní Evropě.

V jednom profilu nelze kombinovat vážené i výkonnostní směrování. Pro podporu tohoto scénáře vytvořte profil Traffic Manager pomocí dvou koncových bodů západní Evropy a metody směrování vážených traffic-routing. Dále přidáte tento profil podřízené jako koncový bod do profilu "nadřazený". Nadřazený profil stále používá metodu směrování provozu výkonu a obsahuje další globální nasazení jako koncové body.

Následující diagram znázorňuje tento příklad:

![Vnořené profily Traffic Manageru][2]

V této konfiguraci provoz směrovaný prostřednictvím nadřazeného profilu distribuuje provoz mezi oblastmi normálně. V rámci západní Evropy vnořený profil distribuuje provoz do produkčních a testovacích koncových bodů podle přiřazených vah.

Pokud nadřazený profil používá metodu směrování provozu "Výkon", musí být každému koncovému bodu přiřazeno umístění. Umístění je přiřazeno při konfiguraci koncového bodu. Zvolte oblast Azure, která je nejblíže vašemu nasazení. Oblasti Azure jsou hodnoty umístění podporované tabulkou latence Internetu. Další informace naleznete v tématu [Traffic Manager 'Performance' traffic-routing method](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Příklad 2: Monitorování koncových bodů ve vnořených profilech

Traffic Manager aktivně sleduje stav každého koncového bodu služby. Pokud koncový bod není v pořádku, Traffic Manager přesměruje uživatele na alternativní koncové body zachovat dostupnost služby. Toto chování sledování koncového bodu a převzetí služeb při selhání platí pro všechny metody směrování provozu. Další informace naleznete v tématu [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Monitorování koncových bodů funguje odlišně pro vnořené profily. U vnořených profilů nadřazený profil neprovádí kontroly stavu dítěte přímo. Místo toho se k výpočtu celkového stavu podřízeného profilu používá stav koncových bodů podřízeného profilu. Tyto informace o stavu jsou rozšířeny do hierarchie vnořených profilů. Nadřazený profil používá tento agregovaný stav k určení, zda má být provoz na podřízený profil nasměrován. Podrobné informace o sledování stavu vnořených profilů najdete v [nejčastějších](traffic-manager-FAQs.md#traffic-manager-nested-profiles) dotazech.

Vrátíme-li se k předchozímu příkladu, předpokládejme, že nasazení v produkčním prostředí v západní Evropě se nezdaří. Ve výchozím nastavení profil podřízené ho přesměruje veškerý provoz na testovací nasazení. Pokud se nezdaří také testovací nasazení, nadřazený profil určuje, že podřízený profil by neměl přijímat přenosy, protože všechny podřízené koncové body nejsou v pořádku. Potom nadřazený profil distribuuje provoz do jiných oblastí.

![Převzetí služeb při selhání vnořený profil (výchozí chování)][3]

S touhle dohodou bys mohl být spokojený. Nebo se můžete obávat, že veškerý provoz pro západní Evropu nyní přejde na testovací nasazení namísto omezeného provozu podmnožiny. Bez ohledu na stav testovacího nasazení chcete přepojit převzetí služeb při selhání do jiných oblastí, když se nezdaří nasazení v produkčním prostředí v západní Evropě. Chcete-li povolit toto převzetí služeb při selhání, můžete zadat parametr "MinChildEndpoints" při konfiguraci podřízeného profilu jako koncového bodu v nadřazeném profilu. Parametr určuje minimální počet dostupných koncových bodů v podřízeném profilu. Výchozí hodnota je "1". V tomto scénáři nastavíte hodnotu MinChildEndpoints na 2. Pod touto prahovou hodnotou nadřazený profil považuje celý podřízený profil za nedostupný a směruje provoz na ostatní koncové body.

Následující obrázek znázorňuje tuto konfiguraci:

![Převzetí služeb při selhání vnořeného profilu s body MinChildEndpoints = 2][4]

> [!NOTE]
> Metoda směrování provozu Priority distribuuje veškerý provoz do jednoho koncového bodu. Proto je malý účel v MinChildEndpoints nastavení jiné než '1' pro podřízený profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Příklad 3: Prioritní oblasti převzetí služeb při selhání ve směrování provozu "Výkon"

Výchozí chování pro metodu směrování provozu "Výkon" je, když máte koncové body v různých geografických umístěních, koncoví uživatelé jsou směrovány do "nejbližší" koncový bod z hlediska nejnižší latence sítě.

Předpokládejme však, že dáváte přednost převzetí služeb při selhání v západní Evropě na západní USA a pouze přímý provoz do jiných oblastí, pokud oba koncové body nejsou k dispozici. Toto řešení můžete vytvořit pomocí podřízeného profilu s metodou směrování provozu "Priorita".

![Směrování provozu "Výkon" s preferenčním převzetím služeb při selhání][6]

Vzhledem k tomu, že koncový bod západní Evropa má vyšší prioritu než koncový bod západní USA, veškerý provoz je odeslán do koncového bodu západní Evropy, když jsou oba koncové body online. Pokud západní Evropa selže, její provoz směřuje do západních USA. S vnořeným profilem je provoz směrován do východní Asie pouze v případě, že západní Evropa i západní USA selžou.

Tento vzor můžete opakovat pro všechny oblasti. Nahraďte všechny tři koncové body v nadřazeném profilu třemi podřízenými profily, z nichž každý poskytuje pořadí převzetí služeb při selhání.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Příklad 4: Řízení směrování provozu "Výkon" mezi více koncovými body ve stejné oblasti

Předpokládejme, že metoda směrování provozu "Výkon" se používá v profilu, který má více než jeden koncový bod v určité oblasti. Ve výchozím nastavení je provoz směrovaný do této oblasti rovnoměrně rozložen mezi všechny dostupné koncové body v této oblasti.

![Distribuce přenosů v oblasti (výchozí chování)][7]

Namísto přidání více koncových bodů v západní Evropě jsou tyto koncové body uzavřeny v samostatném podřízeném profilu. Podřízený profil je přidán do nadřazeného jako jediný koncový bod v západní Evropě. Nastavení na podřízeném profilu můžete řídit distribuci provozu se západní Evropou povolením směrování provozu na základě priority nebo vážený provoz v rámci této oblasti.

![Směrování provozu "Výkon" s vlastní distribucí provozu v oblasti][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Příklad 5: Nastavení monitorování podle koncových bodů

Předpokládejme, že používáte Traffic Manager k hladké migraci provozu z původního místního webu na novou cloudovou verzi hostovoji v Azure. Pro starší web chcete použít identifikátor URI domovské stránky ke sledování stavu webu. Ale pro novou verzi cloudovou platformu implementujete vlastní stránku monitorování (cesta /monitor.aspx), která obsahuje další kontroly.

![Sledování koncového bodu Správce provozu (výchozí chování)][9]

Nastavení monitorování v profilu Traffic Manager u platnou položku pro všechny koncové body v rámci jednoho profilu. U vnořených profilů můžete definovat různá nastavení monitorování pomocí jiného podřízeného profilu na web.

![Sledování koncového bodu Traffic Manageru s nastavením koncového bodu][10]

## <a name="faqs"></a>Nejčastější dotazy

* [Jak nakonfiguruji vnořené profily?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Kolik vrstev hnízdění podporuje Traffic Manger?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Můžu kombinovat jiné typy koncových bodů s vnořenými podřízenými profily ve stejném profilu Traffic Manageru?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Jak se fakturační model vztahuje na vnořené profily?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Má vnořené profily dopad na výkon?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Jak Traffic Manager vypočítá vnořený koncový bod v nadřazeném profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Další kroky

Další informace o [profilech Traffic Manageru](traffic-manager-overview.md)

Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
