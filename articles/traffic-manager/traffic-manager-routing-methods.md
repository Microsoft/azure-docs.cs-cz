---
title: Metody směrování provozu Azure Traffic Manageru – | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit metody směrování provozu různých používaná Traffic Managerem
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 797f97b9c1548484d72f518ae1d2c56633b7b5b3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126764"
---
# <a name="traffic-manager-routing-methods"></a>Metody směrování Traffic Manageru

Azure Traffic Manager podporuje šest metod směrování provozu a zjistěte, jak směrovat síťový provoz do různých koncových bodů služby. Pro žádný profil Traffic Manageru platí metodu směrování provozu přidružený k každý dotaz DNS, které obdrží. Metoda směrování provozu určuje, který koncový bod je vrácená v odpovědi DNS.

Nejsou k dispozici v Traffic Manageru čtyři metody směrování provozu:

* **[Priorita](#priority):** vyberte **Priority** když chcete použít koncový bod primární služby pro veškerý provoz a poskytují zálohování v případě primární nebo zálohování koncových bodů nejsou k dispozici.
* **[Vážená](#weighted):** vyberte **vážená** Pokud chcete provoz distribuovat mezi sadu koncových bodů, buď rovnoměrně, nebo podle váhy, které můžete definovat.
* **[Výkon](#performance):** vyberte **výkonu** Pokud máte koncové body v různých geografických umístěních a chcete, aby koncoví uživatelé použít "nejbližší" koncový bod z hlediska nejnižší síťovou latencí.
* **[Geografické](#geographic):** vyberte **geografické** tak, aby uživatelé jsou přesměrováni na konkrétní koncové body (Azure, externí nebo vnořené) na základě které geografického umístění mohou být jejich dotazu DNS. To dává zákazníkům Traffic Manageru, aby se povolily scénáře, ve kterém je důležité vědět, geografické oblasti uživatele a směrování je na základě. Mezi příklady patří vyhovující pověření suverenita dat, lokalizace obsahu & uživatelské prostředí a měření přenosů z různých oblastí.
* **[Více hodnot](#multivalue):** vyberte **hodnot** pro profily Traffic Manageru, které může mít jenom adresy IPv4/IPv6 jako koncové body. Po přijetí dotaz pro tento profil se vrátí všechny koncové body v pořádku.
* **[Podsíť](#subnet):** vyberte **podsítě** metody směrování provozu do mapování sad rozsahy IP adres koncových uživatelů na určitý koncový bod v rámci profilu Traffic Manageru. Při přijetí požadavku koncový bod vrátil, bude je mapován tento požadavek zdrojové IP adresy. 


Všechny profily Traffic Manageru patří monitorování koncových bodů a koncový bod automatické převzetí služeb při selhání. Další informace najdete v tématu [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md). Jeden profil služby Traffic Manager můžete použít pouze jednu metodu směrování provozu. Kdykoli můžete vybrat metodu směrování provozu různých pro svůj profil. Změny se použijí během jedné minuty a vzniknou nedojde k žádnému výpadku. Metody směrování provozu můžete kombinovat pomocí vnořené profily Traffic Manageru. Vnoření umožňuje propracované a flexibilního směrování provozu konfigurace, které splnění požadavků větších a složité aplikace. Další informace najdete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Metody prioritního směrování provozu

Organizace často chce zajistit spolehlivost pro své služby a nasadit jeden nebo více služeb zálohování v případě, že jejich primární služba přestane fungovat. Metody směrování provozu "Priority" umožňuje zákazníkům Azure snadno implementace tohoto modelu převzetí služeb při selhání.

! [Azure Traffic Manager "Priority" metody směrování provozu] [1]

Profil služby Traffic Manager obsahuje seznam seřazený podle priority koncových bodů služby. Ve výchozím nastavení Traffic Manageru odesílá veškerý provoz na primární koncový bod (nejvyšší priorita). Pokud primární koncový bod není k dispozici, Traffic Manager směruje provoz do druhé koncového bodu. Pokud obě primární a sekundární koncových bodů nejsou k dispozici, provoz směrován na třetí a tak dále. Dostupnost koncového bodu závisí na nakonfigurovaných stavu (povoleno nebo zakázáno) a monitorování probíhající koncových bodů.

### <a name="configuring-endpoints"></a>Konfigurace koncových bodů

S Azure Resource Manageru, můžete nakonfigurovat Priorita koncového bodu explicitně pomocí vlastnosti "priority" pro každý koncový bod. Tato vlastnost je hodnota od 1 do 1000. Nižší hodnoty, tím vyšší priorita. Koncové body nelze sdílet hodnoty priority. Nastavení vlastnost je volitelná. Když tento parametr vynechán, použije se výchozí prioritu na základě pořadí koncový bod.

##<a name = "weighted"></a>Metody váženého směrování provozu
Metody směrování provozu 'Váženého' umožňuje rovnoměrně distribuovat provoz nebo použít předem definované váhu.

! [Azure Traffic Manager "" směrování provozu metody váženého] [2]

Do metody váženého směrování provozu přiřaďte váhu každý koncový bod v konfigurace profilu Traffic Manageru. Váha je celé číslo od 1 do 1000. Tento parametr je nepovinný. Pokud tento parametr vynechán, Traffic Manager používá výchozí tloušťka '1'. Váha vyšší, tím vyšší je priorita.

Pro každé přijaté dotazy DNS Traffic Manager náhodně vybere koncový bod k dispozici. Pravděpodobnost Výběr koncového bodu podle váhy přiřazené všechny dostupné koncové body. Použitím stejnou váhou pro všechny koncové body výsledky v distribuci i provoz. Pomocí vyšší nebo nižší váhu na konkrétním koncovým bodům způsobí, že tyto koncové body mají více nebo méně často vrátit v odpovědi DNS.

Metody váženého umožňuje některé užitečné scénáře:

* Postupné zavádění upgradu: přidělit procento provoz směrovat do nového koncového bodu a postupně zvyšovat provoz na 100 %.
* Migrace aplikací do Azure: vytvoření profilu s Azure a externí koncové body. Nastavte váhu koncové body preferovat nové koncové body.
* Shlukování cloudu pro další kapacitu: rychle rozšířit místní nasazení do cloudu tak, že ji vložíte za profil služby Traffic Manager. Pokud potřebujete víc kapacity v cloudu, můžete přidat nebo povolit další koncové body a určit, jaká část provoz směrován na každý koncový bod.

Kromě webu Azure portal můžete nakonfigurovat váhy pomocí Azure Powershellu, rozhraní příkazového řádku a rozhraní REST API.

Je důležité pochopit, že odpovědí DNS v mezipaměti klienty a strany rekurzivních serverů DNS, které budou klienti používat k překladu názvů DNS. Tato ukládání do mezipaměti může mít vliv na vážený provoz distribucí. Když je velký počet klientů a rekurzivních serverů DNS, distribuce provozu funguje podle očekávání. Ale při malém počtu klientů nebo serverů DNS rekurzivní ukládání do mezipaměti může výrazně zkosení distribuce provozu.

Mezi běžné příklady využití patří:

* Vývojová a testovací prostředí
* Komunikace aplikace – aplikace
* Aplikace zaměřené na úzkých uživatele – základ, které sdílejí běžnou infrastrukturu rekurzivní služby DNS (například zaměstnanci společnosti připojení prostřednictvím proxy serveru)

Tyto účinky ukládání do mezipaměti DNS jsou společné pro všechny, přenosu na základě DNS směrování systémů, nejen Azure Traffic Manageru. V některých případech může explicitně vymazává se mezipaměť DNS poskytují alternativní řešení. V jiných případech může být vhodnější alternativní metodu směrování provozu.

## <a name = "performance"></a>Metody směrování provozu výkonu

Nasazení koncových bodů ve dvou nebo více míst po celém světě můžete rychlejší odezvu u mnoha aplikací můžete provoz nasměrovat na umístění, které je nejblíže k vám. Tuto možnost nabízí a metodu směrování provozu "Výkon".

! [Azure Traffic Manager "Výkonu" metody směrování provozu] [3]

'Co nejblíže' koncový bod není nutně co nejblíž koncovým měřený podle geografické vzdálenosti. Místo toho metodu směrování provozu "Výkonu" Určuje koncový bod nejblíže na základě měření latence sítě. Traffic Manager udržuje tabulku Internet latence sledovat dobu odezvy mezi rozsahy IP adres a každé datové centrum Azure.

Traffic Manager vyhledá zdrojové IP adresy příchozí žádosti DNS v tabulce latence sítě Internet. Traffic Manager potom vybere dostupný koncový bod v datovém centru Azure, který má nejnižší latenci pro tento rozsah IP adres a vrátí tohoto koncového bodu v odpovědi DNS.

Jak je vysvětleno v [jak funguje Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager neobdrží dotazy DNS přímo z klientů. Místo toho dotazy DNS pocházejí z rekurzivní službu DNS, že klienti jsou nakonfigurovány pro použití. Proto, IP adresa použitá k určení 'co nejblíže' koncový bod není IP adresa klienta, ale je IP adresa rekurzivní službu DNS. Tato IP adresa v praxi, je dobré proxy pro klienta.


Traffic Manager pravidelně aktualizuje tabulku latence Internet pro změny v globální síti Internet a nové oblasti Azure. Ale výkonu aplikací se liší v závislosti na v reálném čase kolísání zatížení přes Internet. Směrování provozu výkonu nesleduje zatížení v koncovém bodě určitou službu. Ale pokud koncový bod stane nedostupnou, Traffic Manager nezahrnuje ho v odpovědi na dotazy DNS.


Odkazuje na mějte na paměti:

* Pokud váš profil obsahuje několik koncových bodů ve stejné oblasti Azure, Traffic Manager distribuuje provoz mezi dostupné koncové body v dané oblasti rovnoměrně. Pokud dáváte přednost distribuce různý provoz v rámci oblasti, můžete použít [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).
* Pokud jsou degradovány povolených koncových bodů v nejbližší oblasti Azure, Traffic Manager přesune provozu do koncových bodů v dalším nejbližší oblasti Azure. Pokud chcete definovat pořadí upřednostňované převzetí služeb při selhání, použijte [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).
* Pokud používáte metodu směrování provozu výkonu externí koncové body nebo vnořených koncových bodů, musíte zadat umístění těchto koncových bodů. Vyberte oblast Azure, které je nejblíže k nasazení. Tato umístění jsou hodnoty podporován tabulka latence Internet.
* Algoritmus, který vybere koncový bod je deterministický. Opakovaných dotazů DNS od stejného klienta jsou směrované na stejný koncový bod. Obvykle klienti používají jinou rekurzivních serverů DNS při cestě. Klient může směrovat na jiný koncový bod. Směrování může také být ovlivněna aktualizace v tabulce latence sítě Internet. Metody směrování provozu výkonu proto nezaručuje, že klient je vždy směrovány na stejný koncový bod.
* Když se změní v tabulce latence Internet, můžete si všimnout, že někteří klienti přesměrováni na jiný koncový bod. Tato změna směrování je přesnější podle aktuálního data latence. Tyto aktualizace jsou nezbytné pro zachování přesnost směrování provozu výkonu, jak Internetu neustále vyvíjí.

## <a name = "geographic"></a>Metody geografického směrování provozu

Profily Traffic Manageru můžete nakonfigurovat tak, aby uživatelé jsou přesměrováni na konkrétní koncové body (Azure, externí nebo vnořené) na základě které geografické umístění v jejich dotaz DNS pochází z použití metody geografického směrování. To dává zákazníkům Traffic Manageru, aby se povolily scénáře, ve kterém je důležité vědět, geografické oblasti uživatele a směrování je na základě. Mezi příklady patří vyhovující pověření suverenita dat, lokalizace obsahu & uživatelské prostředí a měření přenosů z různých oblastí.
Pokud profil, který je konfigurován pro geografické směrování, každý koncový bod přidružený profil musí obsahovat sadu geografických oblastí, které jsou přiřazeny k němu. Geografické oblasti může být na následujících úrovních podrobností 
- Světové – žádné oblasti
- Oblastní seskupení – například Afrika, Střední východ, Austrálie/Tichomoří atd. 
- Země/oblast – například, Irsko, Peru, Hongkong – zvláštní administrativní oblast atd. 
- Stát/kraj – například Kalifornie USA, Austrálie – Queensland, Kanada Alberta atd. (Poznámka: Tato úroveň členitost je podporována pouze pro stavy / provincie v Austrálii, Kanada, Spojené království a USA).

Když se koncový bod přiřadí sadě oblastí nebo oblast, všechny požadavky z těchto oblastí směrování pouze do tohoto koncového bodu. Traffic Manager používá k určení oblasti, ze kterého uživatel se dotazuje z – to je obvykle IP adresu místního překladače DNS provádění dotazu jménem uživatele zdrojové IP adresy dotaz DNS.  

![Azure Traffic Manager "Geografické" metody směrování provozu](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager načte zdrojové IP adresy dotazu DNS a určuje, na které geografické oblasti je pocházející z. Poté hledá zobrazíte, pokud je koncový bod, který má této geografické oblasti na něj namapovat. Toto vyhledávání začne na nejnižší úrovni členitosti (kde je podporován, jinak na úrovni země/oblast stát/kraj) a platí až nejvyšší úroveň, která je úplně **World**. Najde první shoda, že tento přechod zálohovaných pomocí je určen jako koncový bod vrátit v odpovědi na dotaz. Pokud je vrácen odpovídající s koncovým bodem typu vnořené koncový bod v rámci této podřízené profilu, podle jeho metody směrování. Pro toto chování platí následující body:

- Geografické oblasti lze mapovat pouze na jeden koncový bod v profilu služby Traffic Manager při geografické směrování se typ směrování. Tím se zajistí, že směrování uživatelů je deterministická a Zákazníci můžete povolit scénáře, které vyžadují jednoznačný geografické hranice.
- Pokud oblast uživatele se dodává ve dvou různých koncových bodů geografické mapování, Traffic Manager vybere koncový bod s nejnižší členitosti a neberou v úvahu směrování žádostí z dané oblasti na jiný koncový bod. Představte si třeba typ profilu geografické směrování se dva koncové body - koncovém bodě 1 a Endpoint2. Koncovém bodě 1 je nakonfigurovaný pro příjem provozu z Irska a Endpoint2 je nakonfigurovaný pro příjem provozu z Evropy. Pokud požadavek pochází z Irska, je vždy směrovat na koncovém bodě 1.
- Protože oblast je možné mapovat pouze na jeden koncový bod, Traffic Manager ji vrátí bez ohledu na to, zda je koncový bod v pořádku nebo ne.

    >[!IMPORTANT]
    >Důrazně doporučujeme, aby zákazníci, kteří používají metody geografického směrování, přidružit ho k koncové body typu vnořené, které má podřízené profily obsahující alespoň dva koncové body v rámci každé.
- Pokud se najde shoda koncový bod a že je koncový bod v **Zastaveno** stavu, vrátí odpověď NODATA Traffic Manageru. Žádné další vyhledávání se provádí v tomto případě výše v hierarchii geografické oblasti. Toto chování platí také pro typy vnořených koncových bodů při podřízené profil je v **Zastaveno** nebo **zakázané** stavu.
- Pokud koncový bod se zobrazí **zakázané** stav, nebude zahrnutý v oblasti odpovídající proces. Toto chování platí také pro typy vnořených koncových bodů při koncový bod se **zakázané** stavu.
- V případě dotazu pochází z geografické oblasti, která nemá žádné mapování v tomto profilu, Traffic Manager vrátí odpověď NODATA. Proto důrazně doporučujeme, aby zákazníci využívat geografické směrování s jedním koncovým bodem v ideálním případě typu vnořené s alespoň dva koncové body v rámci profilu podřízené, s oblastí **World** přiřazené. To také zajistí, že jsou zpracovány všechny IP adresy, které se nemapují na oblast.

Jak je vysvětleno v [jak funguje Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager neobdrží dotazy DNS přímo z klientů. Místo toho dotazy DNS pocházejí z rekurzivní službu DNS, že klienti jsou nakonfigurovány pro použití. Proto IP adresa použitá k určení oblasti není IP adresa klienta, ale je IP adresa rekurzivní službu DNS. Tato IP adresa v praxi, je dobré proxy pro klienta.

## <a name = "multivalue"></a>Vícehodnotový metody směrování provozu
**Hodnot** metody směrování provozu umožňuje získat více koncových bodů v dobrém stavu v odpovědi na jeden dotaz DNS. To umožňuje volajícímu provedete opakování na straně klienta pomocí dalších koncových bodů v případě koncový bod vrácené nereagovaly. Tento model může zvýšit dostupnost služby a snižuje latenci přidružené nový dotaz DNS získat funkční koncový bod. Metody směrování s více hodnotami funguje pouze v případě, že všechny koncové body typu "Externí" a jsou zadané jako IPv4 nebo IPv6 adresy. Po přijetí dotaz pro tento profil se vrátí všechny koncové body v pořádku a podléhají konfigurovatelné maximální počet vrácení.

## <a name = "subnet"></a>Metody směrování provozu podsítě
**Podsítě** metody směrování provozu umožňuje mapování sadu rozsahů IP adres koncového uživatele ke konkrétním koncovým bodům v profilu. Poté, pokud obdrží dotaz DNS pro tento profil Traffic Manageru ho bude kontrolovat zdroj IP adresa této žádosti (ve většině případů to bude odchozí IP adresa DNS překladač volajícího), určení koncového bodu je namapovaná na a vrátí t koncový bod Hat odpovědi na dotaz. IP adresu, která namapovat na koncový bod se dá nastavit jako rozsahy CIDR (např. 1.2.3.0/24) nebo jako rozsah adres (třeba 1.2.3.4-5.6.7.8). Rozsahy IP adres spojeném s koncovým bodem musí být jedinečný v rámci tohoto profilu a nemůže mít k překrytí sadu IP adres jiný koncový bod v jednom profilu.
Pokud neexistují žádné koncové body, na které je možné mapovat tuto IP adresu, Traffic Manager odešle odpověď NODATA. Proto důrazně doporučujeme zajistit všechny možné rozsahy IP adres jsou určené ve vašich koncových bodů.
Směrování podsítě je možné k zajištění jiné prostředí pro připojení z konkrétní IP prostoru uživatele. Například používající směrování na podsíť, zákazník provést všechny žádosti od jejich podnikové směrovat na jiný koncový bod kde, může být testování interní pouze verzi své aplikace. Další možností je, pokud chcete jiné poskytovat uživatelé, kteří se připojují konkrétní poskytovatele internetových služeb (například blokovat uživatele z daného poskytovatele internetových služeb).

## <a name="next-steps"></a>Další postup

Informace o vývoji aplikací s vysokou dostupností pomocí [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md)




