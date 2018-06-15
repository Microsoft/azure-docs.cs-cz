---
title: Vnořené profily Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje funkci 'vnořených profilů z Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 1ac4ec2775ca9f690f5adf4f939908f8cee3f715
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876975"
---
# <a name="nested-traffic-manager-profiles"></a>Vnořené profily Traffic Manageru

Traffic Manager zahrnuje celou řadu metod směrování provozu, které umožňují řídit, jak Traffic Manager vybere, kterému koncovému bodu musí přijímat přenosy z jednotlivých koncových uživatelů. Další informace najdete v tématu [metody směrování provozu Traffic Manager](traffic-manager-routing-methods.md).

Každý profil služby Traffic Manager určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují složitější směrování provozu, než směrování poskytuje jeden profil Traffic Manageru. Lze vnořit profily Traffic Manageru se zkombinovat výhod více než jednu metodu směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manager na podporu větší a složitější nasazení aplikací.

Následující příklady ukazují, jak použít vnořené profily Traffic Manageru v různých scénářích.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Příklad 1: Kombinování 'Výkonu' a 'Vážená' směrování provozu

Předpokládejme, že jste nasadili aplikace v následujících oblastech Azure: západní USA, západní Evropa a ve východní Asii. Použijete metodu směrování provozu Traffic Manager, výkon, distribuovat provoz do oblasti nejblíže uživatele.

![Profil služby Traffic Manager jedním][4]

Nyní předpokládejme, že chcete otestovat aktualizaci služby před distribucí další široce. Chcete použít metodu směrování provozu, vyvážené' k přímé malým procentem provoz do testovací nasazení. Nastavit testovací nasazení společně se stávajícímu provoznímu prostředí v oblasti západní Evropa.

Nelze kombinovat obě vážená a ' výkonu-směrování provozu v jednom profilu. Pro podporu tohoto scénáře, vytvořte profil Traffic Manageru pomocí dva koncové body západní Evropa a metodu směrování provozu 'Vážená'. V dalším kroku přidejte tento profil, podřízené' jako koncový bod profilu "nadřazená". Profil nadřazené stále používá metodu směrování provozu výkonu a obsahuje další globální nasazení jako koncové body.

Následující diagram znázorňuje tento příklad:

![Vnořené profily Traffic Manageru][2]

V této konfiguraci přenášená prostřednictvím profilu nadřazené rozděluje zatížení mezi oblastmi normálně. V oblasti západní Evropa vnořených profilů distribuuje provoz do produkčního prostředí a testovací koncových bodů podle váhy přiřazené.

Pokud profil nadřazené používá metodu směrování provozu 'Výkonu', musí být každý koncový bod přiřazen umístění. Umístění je přiřazen při konfiguraci koncového bodu. Vyberte oblast Azure, které je nejblíže k nasazení. Oblasti jsou hodnoty umístění nepodporuje tabulky latence sítě Internet. Další informace najdete v tématu [Traffic Manager, výkon, metoda směrování provozu](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Příklad 2: Monitorování koncového bodu ve vnořených profilů

Správce provozu aktivně sleduje stav každý koncový bod služby. Pokud koncový bod není v pořádku, přesměruje Traffic Manager uživatelům alternativní koncové body pro zachování dostupnosti vaší služby. Toto chování monitorování a převzetí služeb při selhání koncový bod platí pro všechny metody směrování provozu. Další informace najdete v tématu [Traffic Manager koncového bodu monitorování](traffic-manager-monitoring.md). Monitorování koncového bodu funguje jinak vnořených profilů. S vnořených profilů nemá profil nadřazené provádět kontroly stavu na podřízených přímo. Místo toho stav koncových bodů podřízené profil se používá k výpočtu celkového stavu podřízené profilu. Tato informace o stavu šíří hierarchie vnořených profilů. Profil nadřazené používá toto agregovaný stav k určení, zda směrovat provoz na podřízené profilu. Najdete v článku [– nejčastější dotazy](traffic-manager-FAQs.md#traffic-manager-nested-profiles) úplné podrobnosti o monitorování stavu serveru vnořených profilů.

Vrátíte k předchozí příklad, Předpokládejme, že selže produkční nasazení v oblasti západní Evropa. Ve výchozím profilem "podřízený" směrovat veškerý provoz do testovací nasazení. Pokud se nezdaří ani testovací nasazení, nadřazené profil určuje, že profil podřízené nesmí přijímat přenosy vzhledem k tomu, že jsou všechny koncové body podřízené není v pořádku. Profil nadřazené poté distribuuje provoz do jiných oblastí.

![Vnořené profil převzetí služeb při selhání (výchozí nastavení)][3]

Je možné radostí s toto uspořádání. Nebo může být obavy, že všechny přenosy pro západní Evropa bude testovací nasazení místo omezená podmnožina provoz. Bez ohledu na stav testovací nasazení kterou chcete převzetí služeb při selhání jiných oblastech při selhání produkční nasazení v oblasti západní Evropa. Pokud chcete povolit toto převzetí služeb při selhání, můžete zadat parametr 'MinChildEndpoints' při konfiguraci profilu podřízené jako koncový bod v nadřazené profilu. Parametr určuje minimální počet dostupných koncových bodů v podřízených profilu. Výchozí hodnota je '1'. Pro tento scénář nastavte hodnotu MinChildEndpoints na 2. Pod touto prahovou hodnotou profil nadřazené za celý podřízené profil není k dispozici a přesměruje přenosy na dalších koncových bodů.

Následující obrázek znázorňuje tuto konfiguraci:

![Vnořené převzetí služeb při selhání profil 'MinChildEndpoints' = 2][4]

> [!NOTE]
> Metodu směrování provozu, Priority, distribuuje veškerý provoz na jeden koncový bod. V nastavení MinChildEndpoints než '1' proto není málo účel profil podřízené.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Příklad 3: Oblasti seřazených podle priorit převzetí služeb při selhání v směrování provozu 'výkonu.

Výchozí chování pro metodu směrování provozu, výkon, je určena pro vyhnout přepsání načítání další nejbližší koncový bod a způsobí kaskádových řady chyb. Když se koncový bod nezdaří, je veškerý provoz, který by byl přesměrován do tohoto koncového bodu rovnoměrně rozdělené do dalších koncových bodů přes všechny oblasti.

![Směrování s výchozí převzetí služeb při selhání provozu 'výkonu.][5]

Předpokládejme však přednost západní Evropa provoz převzetí služeb při selhání západní USA a pokud obě koncových bodů nejsou k dispozici pouze směrovat provoz na jiných oblastí. Toto řešení pomocí profilu podřízené metodu směrování provozu, Priority, můžete vytvořit.

![Směrování s přednostní převzetí služeb při selhání provozu 'výkonu.][6]

Vzhledem k tomu, že koncový bod západní Evropa má vyšší prioritu než koncový bod západní USA, veškerý provoz je odeslána koncovému bodu západní Evropa oba koncové body jsou online. Pokud se nezdaří západní Evropa, západní USA se přesměruje jeho provoz. S profilem vnořené provoz se přesměruje východní Asie jenom v případě selhání západní Evropa a západní USA.

Tento vzor, můžete opakovat pro všechny oblasti. Nahradí všechny tři koncové body v profilu nadřazené tři profily podřízené každý poskytování pořadí seřazený podle priority převzetí služeb při selhání.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Příklad 4: Řízení, výkon, směrování provozu mezi několik koncových bodů ve stejné oblasti

Předpokládejme, výkon, směrování provozu metoda se používá v profilu, který má více než jeden koncový bod v určité oblasti. Ve výchozím nastavení je data na danou oblast rovnoměrně rozdělené mezi všechny koncové body k dispozici v této oblasti.

![Směrování distribuce přenosů v oblasti (výchozí chování) provozu 'výkonu.][7]

Místo přidávání několik koncových bodů v oblasti západní Evropa, jsou v profilu samostatnou podřízenou uzavřené těchto koncových bodů. Podřízené profil se přidá do nadřazené jako pouze koncový bod v oblasti západní Evropa. Nastavení v profilu podřízené můžete řídit distribuci přenosů s západní Evropa povolením směrování na základě priority nebo vyvážené provozu v rámci této oblasti.

![Směrování se distribuce přenosů vlastní v oblasti provozu 'výkonu.][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Příklad 5: Nastavení monitorování pro koncový bod

Předpokládejme, že používáte Traffic Manager bez problémů migrovat provoz z starší verze místní webový server na novou verzi cloudové hostované v Azure. Pro starší verze lokality budete chtít použít na domovskou stránku URI k monitorování stavu lokality. Ale pro novou verzi založené na cloudu, kterou implementujete vlastní monitorování stránku (cesta ' / monitor.aspx') obsahující další kontroly.

![Koncový bod Traffic Manager monitorování (výchozí nastavení)][9]

Monitorování nastavení v profilu Správce provozu se vztahují na všechny koncové body v rámci jednoho profilu. S vnořených profilů používáte profil jiné podřízené jednomu serveru k definování různých nastavení monitorování.

![Koncový bod Traffic Manager monitorování pomocí nastavení za koncový bod][10]

## <a name="next-steps"></a>Další kroky

Další informace o [profily Traffic Manageru](traffic-manager-overview.md)

Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

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
