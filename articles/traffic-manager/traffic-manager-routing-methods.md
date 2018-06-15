---
title: Metody směrování provozu Azure Traffic Manageru - | Microsoft Docs
description: To vám pomůže pochopit metody směrování jiný přenos používaný správcem provoz články
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: c9bd9b4913e38ed5c1f7f4ec8ee7e3210fa3be8f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30245358"
---
# <a name="traffic-manager-routing-methods"></a>Metody směrování Traffic Manageru

Azure Traffic Manager podporuje čtyři metody směrování provozu určit, jak ke směrování síťového provozu různé služby na koncové body. Správce provozu se týká metodu směrování provozu každý dotaz DNS, které obdrží. Metodu směrování provozu určuje, kterému koncovému bodu vrácený v odpovědi DNS.

Jsou k dispozici v Traffic Manageru čtyři metody směrování provozu:

* **[Priorita](#priority):** vyberte **s prioritou** když chcete používat koncového bodu primární služby pro všechny přenosy a poskytnout zálohování v případě primární nebo zálohování koncových bodů nejsou k dispozici.
* **[Vážená](#weighted):** vyberte **vážená** chcete provoz distribuovat mezi sadu koncových bodů, buď rovnoměrně nebo podle váhy, které definují, když.
* **[Výkon](#performance):** vyberte **výkonu** Pokud máte koncové body v různých zeměpisných místech a chcete, aby koncoví uživatelé používat "nejbližší" koncového bodu z hlediska nejnižší latenci sítě.
* **[Zeměpisná](#geographic):** vyberte **geografické** tak, aby uživatelé jsou směrované na konkrétní koncové body (Azure, externí nebo vnořené) na základě které zeměpisné umístění svého dotazu DNS pochází z. To umožňuje zákazníkům Traffic Manager povolit scénáře, kde je důležité zároveň budete vědět, geografické oblasti uživatele a je založeno na tomto směrování. Mezi příklady patří soulad s pověřeními suverenity dat, lokalizace obsah & uživatelské prostředí a měření provoz z různých oblastech.

Všechny profily Traffic Manager zahrnují monitorování stavu koncový bod a koncový bod automatické převzetí služeb při selhání. Další informace najdete v tématu [Traffic Manager koncového bodu monitorování](traffic-manager-monitoring.md). Jeden profil Traffic Manageru můžete použít pouze jednu metodu směrování provozu. Kdykoli můžete vybrat metodu směrování různých provozu pro svůj profil. Změny se použijí během jedné minuty a započítány jsou nedojde k žádnému výpadku. Metody směrování provozu lze spojovat pomocí vnořené profily Traffic Manageru. Vnoření umožňuje sofistikovaná a flexibilní směrování provozu konfigurace, které splňovaly potřeby větší a složité aplikace. Další informace najdete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Metoda směrování provozu s prioritou

Často organizace chce zajistit spolehlivost pro jeho služby nasazením jednu nebo více zálohování služeb v případě, že jejich primární služba ocitne mimo provoz. Metodu směrování provozu, Priority' umožňuje Azure zákazníkům snadno implementovat tento vzor převzetí služeb při selhání.

![Azure Traffic Manager, Priority, směrování provozu – metoda][1]

Profil služby Traffic Manager obsahuje seznam koncových bodů služby seřazený podle priority. Ve výchozím nastavení odešle Traffic Manager veškerý provoz na primární koncový bod (nejvyšší priorita). Pokud primární koncový bod není k dispozici, Traffic Manager směrovat provoz na druhý. Pokud obě primární a sekundární koncových bodů nejsou k dispozici, provoz přejde na třetí a tak dále. Dostupnost koncového bodu je založena na nakonfigurované stav (povolit nebo zakázat) a monitorování probíhající koncového bodu.

### <a name="configuring-endpoints"></a>Konfigurace koncových bodů

S Azure Resource Manager, můžete nakonfigurovat Priorita koncového bodu explicitně pomocí vlastnosti "priority" pro každý koncový bod. Tato vlastnost je hodnotu v rozmezí 1 až 1000. Nižší hodnoty představují vyšší prioritu. Koncové body nemohou sdílet hodnoty priority. Nastavení vlastnosti je volitelný. Když tento parametr vynechán, použije se výchozí prioritu podle pořadí koncový bod.

##<a name = "weighted"></a>Vážené směrování provozu – metoda
'Vyvážené' metodu směrování provozu umožňuje rovnoměrně distribuovat provoz nebo použít předem definované vyvážení.

![Azure Traffic Manager 'Vyvážené' směrování provozu – metoda][2]

Vážený metoda směrování provozu přiřaďte váhu každý koncový bod v konfiguraci profilu Traffic Manageru. Váhu je celé číslo od 1 do 1000. Tento parametr je volitelný. Pokud tento parametr vynechán, provoz správce používá výchozí váhu '1'. Váha vyšší, tím vyšší je priorita.

Pro každé přijaté dotazy DNS Traffic Manager náhodně zvolí koncový bod k dispozici. Pravděpodobnost výběr koncový bod podle váhy přiřazené všechny koncové body k dispozici. Používá stejnou váhou mezi všechny výsledky koncové body rozdělení i provoz. Pomocí vyšší nebo nižší váhu na konkrétním koncovým bodům způsobí, že těchto koncových bodů, který se má vrátit více nebo méně často v odpovědi DNS.

Vážený metoda umožňuje některé užitečné scénáře:

* Aplikace postupného upgradu: přidělte procento provoz směrovat na nový koncový bod a postupně zvyšovat provoz v čase 100 %.
* Migrace aplikací do Azure: vytvoření profilu s Azure a externími koncovými body. Upravte váhu koncové body k raději nové koncové body.
* Cloud bursting pro dodatečnou kapacitu: rychle rozbalte položku místní nasazení do cloudu umístěním za profil Traffic Manageru. Pokud budete potřebovat víc kapacity v cloudu, můžete přidat nebo povolit další koncové body a určit, jaké části přenosů přejde na každém koncovém bodu.

Portál Azure Resource Manager podporuje konfiguraci směrování vyvážené provozu.  Můžete nakonfigurovat váhu pomocí verzí Správce prostředků Azure PowerShell, rozhraní příkazového řádku a rozhraní REST API.

Je důležité si uvědomit, že jsou odpovědi DNS ukládat do mezipaměti klienty a servery DNS rekurzivní, které budou klienti používat k překladu názvů DNS. Toto použití mezipaměti může mít vliv na provoz vyvážené distribuce. Po velký počet klientů a serverů DNS rekurzivní distribuce přenosů funguje podle očekávání. Ale při malém počtu klientů nebo serverů DNS rekurzivní ukládání do mezipaměti může výrazně zkreslit distribuce přenosů.

Běžné případy použití patří:

* Vývoj a testování prostředí
* Komunikace aplikace – aplikace
* Aplikace zaměřené na úzké uživatele – základní hodnotu, která sdílejí společné rekurzivní infrastruktury služby DNS (například zaměstnanci společnosti připojení prostřednictvím proxy serveru)

Tyto účinky ukládání do mezipaměti DNS jsou společné pro všechny přenosy DNS na základě směrování systémy nejen Azure Traffic Manager. V některých případech může explicitně vymazání mezipaměti DNS zadejte alternativní řešení. V jiných případech může být vhodnější alternativní metodu směrování provozu.

## <a name = "performance"></a>Metoda směrování provozu výkonu

Nasazení koncové body ve dvou nebo více umístění po celém světě můžete zlepšit odezvu mnoho aplikací pomocí směrování provozu na umístění, které je nejblíže k vám. Tuto možnost nabízí metodu směrování provozu 'Výkonu'.

![Azure Traffic Manager, výkon, směrování provozu – metoda][3]

'Nejbližší' koncový bod není nutně nejbližší měřený podle zeměpisného vzdálenost. Místo toho metodu směrování provozu, výkon, zjišťuje nejbližší koncový bod, tím měření latence sítě. Správce provozu udržuje tabulky latence Internetu ke sledování doby odezvy mezi rozsahů IP adres a každé datové centrum Azure.

Správce provozu vyhledává IP adresu zdrojového příchozích požadavků DNS v tabulce latence sítě Internet. Správce provozu zvolí koncový bod k dispozici v datovém centru Azure, který má nejnižší latenci pro tento rozsah IP adres a potom vrátí tohoto koncového bodu v odpovědi DNS.

Jak je popsáno v [jak Traffic Manager funguje](traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager neobdrží dotazy DNS přímo z klientů. Místo toho dotazy DNS pocházet ze služby DNS rekurzivní aby klienti jsou konfigurovány k použití. Proto umožňuje určit 'nejbližší' koncový bod není IP adresa klienta, ale je IP adresa služby DNS rekurzivní IP adresu. Tato IP adresa v praxi, je dobré proxy pro klienta.


Traffic Manager pravidelně aktualizuje v tabulce latence Internetu pro případ změny v globálních Internetu a nové oblastech Azure. Ale výkonu aplikací se liší podle v reálném čase rozdíly v zatížení přes Internet. Směrování provozu výkonu nesleduje zatížení koncového bodu dané služby. Ale pokud koncový bod stane nedostupným, Traffic Manager nezahrnuje ho v odpovědi na dotazy DNS.


Všimněte si body:

* Pokud váš profil obsahuje několik koncových bodů ve stejné oblasti Azure, Traffic Manager rozděluje zatížení mezi koncové body k dispozici v této oblasti rovnoměrně. Pokud dáváte přednost distribuce různých přenosů v rámci oblasti, můžete použít [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).
* Pokud jsou degradovány povolených koncových bodů v nejbližší oblast Azure, Traffic Manager přesune provozu do koncových bodů v další nejbližší oblast Azure. Pokud chcete definovat pořadí upřednostňované převzetí služeb při selhání, použijte [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).
* Pokud používáte metodu směrování provozu výkonu s externí koncové body nebo vnořené koncových bodů, budete muset zadat umístění těchto koncových bodů. Vyberte oblast Azure, které je nejblíže k nasazení. Hodnoty nepodporuje tabulky latence Internetu jsou těchto umístěních.
* Algoritmus, který vybere koncový bod je deterministická. Opakované dotazy DNS ze stejného klienta jsou směrované na stejný koncový bod. Obvykle se klienti používat servery DNS jiné rekurzivní cestách. Klient může směrovat na jiný koncový bod. Směrování může být ovlivněn aktualizace v tabulce latence sítě Internet. Proto metodu směrování provozu výkonu nezaručuje, že je klient vždy směrované na stejný koncový bod.
* Když se změní v tabulce latence Internetu, můžete si všimnout, že někteří klienti jsou směrované na jiný koncový bod. Tato změna směrování je přesnější podle aktuálního data latence. Tyto aktualizace jsou nezbytné pro zachování přesnost směrování provozu výkonu jako průběžně zpracovaní Internetu.

## <a name = "geographic"></a>Zeměpisná metodu směrování provozu

Profily Traffic Manageru lze nakonfigurovat tak, aby uživatelé jsou směrované ke konkrétním koncovým bodům (Azure, externí nebo vnořené) na základě, na které zeměpisné umístění svého dotazu DNS pochází z používat metodu geografické směrování. To umožňuje zákazníkům Traffic Manager povolit scénáře, kde je důležité zároveň budete vědět, geografické oblasti uživatele a je založeno na tomto směrování. Mezi příklady patří soulad s pověřeními suverenity dat, lokalizace obsah & uživatelské prostředí a měření provoz z různých oblastech.
Pokud profil je nakonfigurována pro zeměpisnou směrování, každý koncový bod přidružený profil musí obsahovat sadu zeměpisné oblasti přiřazen. Může být geografické oblasti s následující úrovní členitosti 
- World – libovolné oblasti
- Místní seskupení – například Afrika, Střední východ, Austrálie/Tichomoří atd. 
- Země nebo oblast – například Irsku Peru, Hongkong – zvláštní správní oblast ČLR atd. 
- Kraj – například kalifornské USA, Austrálie – Queensland, Kanada Alberta atd. (Poznámka: Tato úroveň členitost je podporována pouze pro stavy nebo provincie Austrálie, Kanada, UK a USA).

Když koncový bod je přiřazen oblasti nebo sadu oblasti, všechny žádosti z těchto oblastí směrovaných pouze do tohoto koncového bodu. Traffic Manager používá IP adresu zdrojového dotazu DNS k určení oblasti, ze kterého uživatel se dotazuje z – to je obvykle IP adresu DNS překladače místní provádění dotazu jménem uživatele.  

![Azure Traffic Manager, geografické' směrování provozu – metoda](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager načte IP adresu zdrojového dotazu DNS a rozhodne, které geografické oblasti je pocházející z. Pak vypadá to, jestli je koncový bod, který má této geografické oblasti k němu mapována. Toto vyhledávání spustí na nejnižší úrovni členitosti (Kraj kde by je podporován, jinak na úrovni země nebo oblast) a klient se přepne až nejvyšší úrovni, což je **World**. Na první shodu nalezen, že použití této traversal je určen jako koncový bod vrátit v odpovědi na dotaz. Pokud je vrácen odpovídající pomocí typu koncový bod vnořené, koncový bod v rámci této podřízené profilu, založené na jeho metody směrování. Pro toto chování platí následující body:

- Geografické oblasti lze mapovat pouze na jeden koncový bod v profilu Traffic Manageru po geografické směrování typ směrování. To zajišťuje, že směrování uživatelů je deterministická a zákazníků můžete povolit scénáře, které vyžadují jednoznačným geografické hranice.
- Pokud uživatele oblast dodává do dvou různých koncových bodů zeměpisné mapování, Traffic Manager vybere koncový bod s nejnižší členitosti a nebere v úvahu směrování žádostí ze danou oblast jiný koncový bod. Představte si třeba profil typ geografické směrování s dva koncové body - koncovém bodě 1 a Endpoint2. Koncovém bodě 1 je konfigurován na přijímání komunikace z Irsku a Endpoint2 je konfigurován na přijímání komunikace z Evropa. Pokud požadavek pochází z Irská, je vždy směrovány do koncovém bodě 1.
- Vzhledem k tomu, že v oblasti lze mapovat pouze na jeden koncový bod, vrátí ji Traffic Manager bez ohledu na to, jestli koncový bod je v pořádku nebo ne.

    >[!IMPORTANT]
    >Důrazně doporučujeme, aby zákazníkům, kteří používají geografické metody směrování, přidružte ji k koncové body typu vnořené, které má podřízené profily obsahující alespoň dva koncové body v každém.
- Pokud je nalezena shoda koncový bod a že koncový bod je v **Zastaveno** stavu Traffic Manager vrátí odpověď NODATA. Žádné další hledání v takovém případě se provádí vyšší nahoru v hierarchii geografické oblasti. Toto chování platí i pro typy vnořených koncových bodů po v profilu podřízené **Zastaveno** nebo **zakázané** stavu.
- Pokud se zobrazí koncový bod **zakázané** stav, nebude zahrnutý v oblasti odpovídající procesu. Toto chování platí i pro typy vnořených koncových bodů při koncový bod je v **zakázané** stavu.
- Pokud dotaz pochází z zeměpisnou oblast, která nemá žádné mapování v tomto profilu, Traffic Manager vrátí odpověď NODATA. Proto důrazně doporučujeme, aby zákazníci používat zeměpisné směrování s jeden koncový bod, v ideálním případě tohoto typu vnořené s alespoň dva koncové body v rámci profilu podřízené, s oblast **World** přiřazen. Také zajistíte, že jsou zpracovávány všechny IP adresy, které se nemapují do oblasti.

Jak je popsáno v [jak Traffic Manager funguje](traffic-manager-how-traffic-manager-works.md), Traffic Manager neobdrží dotazy DNS přímo z klientů. Místo toho dotazy DNS pocházet ze služby DNS rekurzivní aby klienti jsou konfigurovány k použití. Proto IP adresu používá k určení oblasti není IP adresa klienta, ale je IP adresa rekurzivní služby DNS. Tato IP adresa v praxi, je dobré proxy pro klienta.


## <a name="next-steps"></a>Další postup

Další informace jak vyvíjet aplikací s vysokou dostupností pomocí [monitorování koncového bodu Traffic Manager](traffic-manager-monitoring.md)

Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



