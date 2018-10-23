---
title: Vnořené profily Traffic Manageru | Dokumentace Microsoftu
description: Tento článek vysvětluje funkce "vnořených profilů Azure Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: kumud
ms.openlocfilehash: 876305c7195a186671c30c4bdd9bb0c6b5331e9a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648594"
---
# <a name="nested-traffic-manager-profiles"></a>Vnořené profily Traffic Manageru

Traffic Manager zahrnuje celou řadu metod směrování provozu, které umožňují řídit, jak Traffic Manager vybere, který koncový bod by měl přijímat provoz z každému koncovému uživateli. Další informace najdete v tématu [metody směrování provozu Traffic Manageru](traffic-manager-routing-methods.md).

Každý profil služby Traffic Manager určuje jedinou metodu směrování provozu. Existují ale scénáře, které vyžadují složitější směrování provozu, než směrování poskytuje jeden profil Traffic Manageru. Je možné vnořovat profily Traffic Manageru zkombinovat výhod více než jednu metodu směrování provozu. Vnořené profily umožňují potlačit výchozí chování Traffic Managerem pro podporu větší a složitější nasazení aplikací.

Následující příklady ukazují, jak použít vnořené profily Traffic Manageru v různých scénářích.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Příklad 1: Kombinování "Výkonu" a "Vážená" směrování provozu

Předpokládejme, že jste nasadili aplikace v následujících oblastech Azure: západní USA, západní Evropa a východní Asie. Metody směrování provozu Traffic Manageru "Výkonu" použijete k distribuci provozu na oblast nejbližší uživatelům.

![Profil služby Traffic Manager jedním][4]

Předpokládejme, že chcete otestovat před distribucí více široce aktualizace do služby. Chcete použít 'vážená: metody směrování provozu ke směrování provozu do vaší testovací nasazení pro malé procento. Nastavení testu nasazení společně s existující nasazení do produkčního prostředí v oblasti západní Evropa.

Nelze kombinovat obou vážená a "výkonu – směrování provozu v jednom profilu. Pro podporu tohoto scénáře, vytvořte profil služby Traffic Manager pomocí dva koncové body západní Evropa a metodu směrování provozu "Vážená". V dalším kroku přidáte tento profil "podřízený" jako koncový bod do profilu "nadřazený". Profil nadřazené stále používá metodu směrování provozu výkonu a obsahuje jiné globální nasazení jako koncové body.

Následující diagram znázorňuje v tomto příkladu:

![Vnořené profily Traffic Manageru][2]

V této konfiguraci přenášená prostřednictvím profilu nadřazené distribuuje provoz napříč oblastmi normálně. V oblasti západní Evropa vnořeného profilu distribuuje provoz do produkčního prostředí a testování koncových bodů podle váhy přiřazené.

Používá-li profil nadřazené metody směrování provozu "Výkonu", musíte být přiřazeni umístění každého koncového bodu. Umístění je přiřazen, když konfigurujete koncový bod. Vyberte oblast Azure, které je nejblíže k nasazení. Oblastí Azure jsou hodnoty umístění podporován tabulka latence Internet. Další informace najdete v tématu [Traffic Manageru "Výkonu" metody směrování provozu](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Příklad 2: Monitorování koncových bodů ve vnořené profily

Traffic Manager aktivně monitoruje stav každého koncového bodu služby. Pokud koncový bod není v pořádku, Traffic Manager přesměruje uživatelům výjimek alternativní koncové body pro zachování dostupnosti vaší služby. Chování koncového bodu monitorování a převzetí služeb při selhání se vztahuje na všechny metody směrování provozu. Další informace najdete v tématu [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md). Monitorování koncových bodů funguje jinak vnořených profilů. Vnořené profily profil nadřazené neprovádí kontroly stavu pro podřízený přímo. Místo toho stavu koncových bodů podřízené profil se používá k výpočtu celkového stavu profilu podřízené. Informace o tomto stavu se šíří hierarchii vnořeného profilu. Nadřazené profil používá toto agregovaný stav k určení, jestli ke směrování provozu do podřízených profilu. Zobrazit [nejčastější dotazy k](traffic-manager-FAQs.md#traffic-manager-nested-profiles) úplné podrobnosti o monitorování stavu vnořených profilů.

Vrátí do předchozího příkladu předpokládejme, že selže nasazení do produkčního prostředí v oblasti západní Evropa. Ve výchozím nastavení směruje veškerý provoz do testovací nasazení profilu "podřízený". Pokud se nasazení testu také nezdaří, nadřazené profil určuje, že profil podřízené neměli přijímat provoz od všech podřízených koncových bodů nejsou v pořádku. Profil nadřazené pak distribuuje provoz do jiných oblastí.

![Vnořené profilu převzetí služeb při selhání (výchozí nastavení)][3]

Je možné, se toto uspořádání. Nebo může být, že veškerý provoz pro oblasti západní Evropa teď bude testovací nasazení namísto omezené dílčí provoz. Bez ohledu na stav nasazení testu chcete převzetí služeb při selhání do jiných oblastí při selhání nasazení do produkčního prostředí v oblasti západní Evropa. Pokud chcete povolit toto převzetí služeb při selhání, můžete zadat parametr "MinChildEndpoints" při konfiguraci profilu podřízené jako koncový bod v nadřazené profilu. Parametr určuje minimální počet dostupných koncových bodů v podřízených profilu. Výchozí hodnota je '1'. Pro tento scénář nastavte hodnotu MinChildEndpoints na 2. Pod touto prahovou hodnotou profil nadřazené považuje za celý podřízené profilu do nedostupný a přesměruje provoz do dalších koncových bodů.

Následující obrázek znázorňuje tuto konfiguraci:

![Vnořené převzetí služeb při selhání profilu 'MinChildEndpoints' = 2][4]

> [!NOTE]
> Metody směrování provozu "Priority" distribuuje veškerý provoz do jediného koncového bodu. V nastavení MinChildEndpoints jiné než '1' proto není trochu účel profilu podřízené.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Příklad 3: Seřazený podle priority převzetí služeb při selhání oblasti v směrování provozu "výkon.

Výchozí chování pro metody směrování provozu "Výkonu" je až budete mít koncové body v různých geografických lokalitách, které koncoví uživatelé jsou směrovány na "nejbližší" koncového bodu z hlediska nejnižší síťovou latencí.

Předpokládejme, ale dáváte přednost převzetí služeb při selhání oblasti západní Evropa provoz do oblasti západní USA a pouze směrovat provoz do jiných oblastí, když oba koncové body nejsou k dispozici. Můžete vytvořit toto řešení využívající profil podřízené pomocí metody směrování provozu "Priority".

![Směrování s přednostní převzetí služeb při selhání provozu 'výkon.][6]

Protože západní Evropa koncový bod má vyšší prioritu než koncový bod USA – Západ, všechny přenosy se odesílají do koncového bodu západní Evropa oba koncové body jsou online. Pokud se nezdaří západní Evropa, její provoz se směřuje do oblasti západní USA. Pomocí vnořeného profilu provoz se směřuje do východní Asie jenom v případě selhání oblasti západní Evropa a západní USA.

Tento vzor lze opakovat pro všechny oblasti. Nahraďte všechny tři koncové body v profilu nadřazené tři profily podřízené každý poskytuje seřazený podle priority převzetí služeb při selhání pořadí.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Příklad 4: Řízení "Výkonu" směrování provozu mezi více koncových bodů ve stejné oblasti

Předpokládejme, že "Výkonu" metody směrování provozu se používá v profilu, který má více než jeden koncový bod v konkrétní oblasti. Ve výchozím nastavení přenášená pro tuto oblast se rovnoměrně mezi všechny dostupné koncové body v dané oblasti.

![Směrování provozu v oblasti distribuce (výchozí chování) provozu "výkon.][7]

Místo přidání více koncových bodů v oblasti západní Evropa, tyto koncové body jsou uzavřeny v profilu samostatnou podřízenou. Profil podřízené se přidá do nadřazené jako jediný koncový bod v oblasti západní Evropa. Nastavení v profilu podřízené můžete řídit distribuci provozu s západní Evropa tím, že směrování provozu na základě priority nebo s poměrným v dané oblasti.

![Směrování s vlastní provozu v oblasti distribuce provozu "výkon.][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Příklad 5: Nastavení monitorování za koncový bod

Předpokládejme, že používáte Traffic Manageru hladce migrovat provoz ze starší verze v místním webovém serveru na novou verzi založené na cloudu hostovaná v Azure. Pro starší verze serveru chcete použít na domovské stránce identifikátor URI pro sledování stavu lokality. Pro novou cloudovou verzi, implementujete vlastní monitorování stránky, ale (cesta: / monitor.aspx "), který obsahuje další kontroly.

![Monitorování (výchozí chování) koncových bodů Traffic Manageru][9]

Monitorování nastavení v profilu služby Traffic Manager platí pro všechny koncové body v rámci jediného profilu. Vnořené profily použijete profil jiné podřízené jednotlivé lokality k definování různých nastavení monitorování.

![Monitorování s za koncový bod nastavení koncových bodů Traffic Manageru][10]

## <a name="next-steps"></a>Další postup

Další informace o [profily Traffic Manageru](traffic-manager-overview.md)

Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md)

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
