---
title: Profily vnořených Traffic Manager v Azure
titleSuffix: Azure Traffic Manager
description: Tento článek vysvětluje funkci "vnořené profily" v Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185576"
---
# <a name="nested-traffic-manager-profiles"></a>Vnořené profily Traffic Manageru

Traffic Manager zahrnuje škálu metod směrování provozu, které vám umožní určit, jak má Traffic Manager zvolit, jaký koncový bod by měl přijímat přenosy od každého koncového uživatele. Další informace najdete v tématu [Traffic Manager metody směrování provozu](traffic-manager-routing-methods.md).

Každý profil Traffic Manager Určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují výkonnější směrování provozu než směrování poskytované jedním Traffic Manager profilem. Můžete vnořovat profily Traffic Manager, abyste mohli kombinovat výhody více než jedné metody směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manager pro podporu většího a složitějšího nasazení aplikací.

Následující příklady ilustrují použití vnořených profilů Traffic Manager v různých scénářích.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Příklad 1: kombinování směrování provozu Performance a Weight

Předpokládejme, že jste nasadili aplikaci v následujících oblastech Azure: Západní USA, Západní Evropa a Východní Asie. K distribuci provozu do oblasti, která je nejblíže uživateli, slouží Traffic Manager způsob směrování provozu "Performance".

![Profil jednoho Traffic Manager][4]

Nyní předpokládejme, že chcete otestovat aktualizaci služby před tím, než ji navedete do širšího provozu. Pro přesměrování malého procenta provozu do testovacího nasazení chcete použít metodu směrování "váženého" provozu. Testovací nasazení se nastavuje společně s existujícím provozním nasazením v Západní Evropa.

V jednom profilu nemůžete kombinovat jak vážené, tak výkonové směrování. Pro podporu tohoto scénáře vytvoříte profil Traffic Manager pomocí dvou koncových bodů Západní Evropa a vážené metody směrování provozu. Dále přidáte tento profil podřízeného objektu jako koncový bod do profilu nadřazených objektů. Nadřazený profil stále používá metodu směrování provozu a obsahuje další globální nasazení jako koncové body.

Následující diagram znázorňuje tento příklad:

![Vnořené profily Traffic Manageru][2]

V této konfiguraci provoz směrované přes nadřazený profil distribuuje provoz napříč oblastmi normálně. V rámci Západní Evropa vnořený profil distribuuje provoz do produkčních a testovacích koncových bodů podle přiřazených vah.

Pokud nadřazený profil používá metodu směrování provozu Performance, musí být každému koncovému bodu přiřazeno umístění. Umístění se přiřadí při konfiguraci koncového bodu. Vyberte oblast Azure, která je nejblíže vašemu nasazení. Oblasti Azure jsou hodnoty umístění podporované tabulkou latence Internetu. Další informace najdete v tématu [Traffic Manager metoda "Performance Traffic-Routing"](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Příklad 2: monitorování koncových bodů ve vnořených profilech

Traffic Manager aktivně sleduje stav každého koncového bodu služby. Pokud koncový bod není v pořádku, Traffic Manager přesměruje uživatele do alternativních koncových bodů, aby se zachovala dostupnost vaší služby. Toto chování monitorování a převzetí služeb při selhání se vztahuje na všechny metody směrování provozu. Další informace najdete v tématu [Traffic Manager monitorování koncového bodu](traffic-manager-monitoring.md). Monitorování koncového bodu funguje pro vnořené profily jinak. U vnořených profilů nadřazený profil neprovádí kontroly stavu přímo u podřízeného objektu. Místo toho se k výpočtu celkového stavu podřízeného profilu používá stav koncových bodů podřízeného profilu. Tyto informace o stavu se šíří v hierarchii vnořeného profilu. Nadřazený profil pomocí tohoto agregovaného stavu určí, jestli se má směrovat provoz do podřízeného profilu. Úplné podrobnosti o monitorování stavu vnořených profilů najdete v [nejčastějších dotazech](traffic-manager-FAQs.md#traffic-manager-nested-profiles) .

Návrat k předchozímu příkladu Předpokládejme, že produkční nasazení v Západní Evropa neproběhne úspěšně. Ve výchozím nastavení profil "Child" směruje veškerý provoz do testovacího nasazení. Pokud testovací nasazení také neproběhne úspěšně, nadřazený profil určí, že podřízený profil by neměl přijímat přenosy, protože všechny podřízené koncové body nejsou v pořádku. Nadřazený profil pak distribuuje provoz do ostatních oblastí.

![Převzetí služeb při selhání vnořeného profilu (výchozí chování)][3]

S tímto uspořádáním možná budete spokojeni. Nebo se může stát, že veškerý provoz pro Západní Evropa nyní nachází do testovacího nasazení místo omezeného provozu podmnožiny. Bez ohledu na stav nasazení testu chcete převzít služby při selhání do ostatních oblastí, když produkční nasazení v Západní Evropa selže. Pokud chcete povolit toto převzetí služeb při selhání, můžete zadat parametr MinChildEndpoints při konfiguraci podřízeného profilu jako koncového bodu v nadřazeném profilu. Parametr určuje minimální počet dostupných koncových bodů v podřízeném profilu. Výchozí hodnota je 1. V tomto scénáři nastavíte hodnotu MinChildEndpoints na 2. Pod touto prahovou hodnotou nadřazený profil posuzuje, že celý podřízený profil nebude k dispozici a směruje provoz do ostatních koncových bodů.

Tato konfigurace je znázorněna na následujícím obrázku:

![Převzetí služeb při selhání vnořeného profilu s ' MinChildEndpoints ' = 2][4]

> [!NOTE]
> Metoda "Priorita" provoz-směrování distribuuje veškerý provoz do jednoho koncového bodu. Proto je pro podřízený profil malý účel v nastavení MinChildEndpoints, než je 1.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Příklad 3: prioritní oblasti převzetí služeb při selhání ve směrování provozu výkonu

Výchozím chováním pro metodu směrování provozu Performance (výkon) je, že pokud máte koncové body v různých geografických umístěních, budou koncoví uživatelé směrováni do nejbližšího koncového bodu z hlediska nejnižší latence sítě.

Předpokládejme však, že dáváte přednost převzetí služeb při selhání Západní Evropa provozu Západní USA a pouze přímý provoz do jiných oblastí, pokud oba koncové body nejsou k dispozici. Toto řešení můžete vytvořit pomocí podřízeného profilu s metodou "Priorita" přenosu provozu.

![Směrování provozu výkonu s preferenčním převzetím služeb při selhání][6]

Vzhledem k tomu, že koncový bod Západní Evropa má vyšší prioritu než koncový bod Západní USA, veškerý provoz se pošle do Západní Evropaho koncového bodu, pokud jsou oba koncové body online. Pokud se Západní Evropa nepovede, provoz se přesměruje na Západní USA. U vnořeného profilu je provoz směrován na Východní Asie pouze v případě, že dojde k selhání obou Západní Evropa a Západní USA.

Tento vzor můžete opakovat pro všechny oblasti. Nahradí všechny tři koncové body v nadřazeném profilu třemi podřízenými profily, přičemž každý z nich poskytuje sekvenci převzetí služeb při selhání s určenou prioritou.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Příklad 4: řízení směrování provozu výkonu mezi několika koncovými body ve stejné oblasti

Předpokládejme, že se v profilu, který má více než jeden koncový bod v konkrétní oblasti, používá metoda směrování provozu Performance. Ve výchozím nastavení jsou přenosy směrované do této oblasti rovnoměrně rozloženy ve všech dostupných koncových bodech v této oblasti.

![Směrování provozu v oblasti výkonu pro distribuci provozu v oblasti (výchozí chování)][7]

Místo přidávání více koncových bodů v Západní Evropa jsou tyto koncové body uzavřeny v samostatném podřízeném profilu. Podřízený profil se přidá do nadřazeného objektu jako jediný koncový bod v Západní Evropa. Nastavení v podřízeném profilu můžou řídit distribuci provozu pomocí Západní Evropa tím, že v rámci této oblasti povolíte směrování na základě priority nebo váženého provozu.

![Směrování provozu výkonu s vlastní distribucí provozu v oblasti][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Příklad 5: nastavení monitorování na základě koncového bodu

Předpokládejme, že používáte Traffic Manager k plynulé migraci provozu ze zastaralého místního webu do nové cloudové verze hostované v Azure. Pro starší verzi webu chcete použít identifikátor URI domovské stránky k monitorování stavu lokality. Ale pro novou cloudovou verzi implementujete vlastní stránku monitorování (cesta '/monitor.aspx '), která obsahuje další kontroly.

![Monitorování koncového bodu Traffic Manager (výchozí chování)][9]

Nastavení monitorování v profilu Traffic Manager platí pro všechny koncové body v rámci jednoho profilu. U vnořených profilů můžete pro jednotlivé lokality použít jiný podřízený profil a definovat různá nastavení monitorování.

![Traffic Manager monitorování koncového bodu s nastavením pro koncový bod][10]

## <a name="faqs"></a>Nejčastější dotazy

* [Návody nakonfigurovat vnořené profily?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Kolik vrstev vnoření podporuje Traffic Manager?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Můžu ve stejném profilu Traffic Manager kombinovat jiné typy koncových bodů s vnořenými podřízenými profily?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Jak model fakturace platí pro vnořené profily?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Je pro vnořené profily dopad na výkon?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Jak Traffic Manager počítá stav vnořeného koncového bodu v nadřazeném profilu?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Další kroky

Další informace o [profilech Traffic Manager](traffic-manager-overview.md)

Informace o tom, jak [vytvořit profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)

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