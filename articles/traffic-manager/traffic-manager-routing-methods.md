---
title: Metody směrování v Azure Traffic Manager – provoz
description: V tomto článku se seznámíte s různými metodami směrování provozu, které používá Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: 0eb49f3c2acc31cba7b245995cf3bcb579113e4c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183809"
---
# <a name="traffic-manager-routing-methods"></a>Metody směrování Traffic Manageru

Azure Traffic Manager podporuje šest metod směrování provozu, které určují, jak směrovat síťový provoz do různých koncových bodů služby. Pro jakýkoliv profil Traffic Manager použije pro každý dotaz DNS metodu směrování provozu, která je k němu přidružená. Metoda směrování provozu určuje, který koncový bod se vrátí v odpovědi DNS.

V Traffic Manager jsou k dispozici následující metody směrování provozu:

* **[Priorita](#priority-traffic-routing-method):** vyberte **prioritu** , pokud chcete použít primární koncový bod služby pro veškerý provoz a poskytnout zálohy pro případ, že primární nebo záložní koncové body nejsou k dispozici.
* **[Vážená](#weighted):** Pokud chcete distribuovat provoz napříč sadou koncových bodů, a to buď rovnoměrně, nebo podle vah, které definujete, vyberte **Vážený** .
* **[Výkon](#performance):** vyberte **výkon** , když máte koncové body v různých geografických umístěních a chcete, aby koncoví uživatelé používali "nejbližší" koncový bod z hlediska nejnižší latence sítě.
* **[Geografické](#geographic):** vyberte **geografické** , aby byli uživatelé přesměrováni na konkrétní koncové body (Azure, External nebo Nested) na základě zeměpisné polohy, ze které jejich dotaz DNS pochází. To umožňuje Traffic Manager zákazníkům povolit scénáře, ve kterých je důležité, aby znali geografickou oblast uživatele a mohli je směrovat na základě důležitosti. Mezi příklady patří dodržování pověření k suverenitě dat, lokalizace obsahu & uživatelského prostředí a měření provozu z různých oblastí.
* **[](#multivalue)** Více hodnot: **Vyberte možnost** vyhodnotit pro Traffic Manager profily, které mohou mít jako koncové body pouze adresy IPv4/IPv6. Při přijetí dotazu pro tento profil jsou vráceny všechny funkční koncové body.
* **[Podsíť](#subnet):** vyberte metodu směrování provozu **podsítě** , která mapuje sady rozsahů IP adres koncových uživatelů na konkrétní koncový bod v rámci profilu Traffic Manager. Po přijetí žádosti se vrátí koncový bod, který je namapován pro zdrojovou IP adresu této žádosti. 


Všechny profily Traffic Manager zahrnují monitorování stavu koncových bodů a automatického převzetí služeb při selhání koncového bodu. Další informace najdete v tématu [Traffic Manager monitorování koncového bodu](traffic-manager-monitoring.md). Jeden profil Traffic Manager může používat jenom jednu metodu směrování provozu. Pro váš profil můžete kdykoli vybrat jinou metodu směrování provozu. Změny se uplatní během jedné minuty a neúčtují se žádné výpadky. Metody směrování provozu lze kombinovat pomocí vnořených Traffic Manager profilů. Vnořování umožňuje sofistikované a flexibilní konfigurace směrování provozu, které splňují potřeby větších, složitých aplikací. Další informace najdete v tématu [vnořené Traffic Manager profily](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Prioritní přenos – Metoda směrování

Organizace často chce zajistit spolehlivost pro své služby nasazením jedné nebo více služeb zálohování pro případ, že jejich primární služba přestane být funkční. Metoda "Priorita" přenosu dat umožňuje zákazníkům Azure snadno implementovat tento vzor převzetí služeb při selhání.

![Služba Azure Traffic Manager "Priorita" přenosu – Metoda směrování](media/traffic-manager-routing-methods/priority.png)

Profil Traffic Manageru obsahuje seznam koncových bodů služby seřazených podle priority. Ve výchozím nastavení Traffic Manager odesílá veškerý provoz na primární koncový bod (s nejvyšší prioritou). Pokud primární koncový bod není dostupný, Traffic Manager směruje provoz do druhého koncového bodu v seznamu. Pokud není dostupný primární ani druhý koncový bod, provoz se bude směrovat do třetího koncového bodu atd. Dostupnost koncového bodu závisí na nakonfigurovaném stavu (povoleno nebo zakázáno) a na průběžném monitorování koncového bodu.

### <a name="configuring-endpoints"></a>Konfigurace koncových bodů

U Azure Resource Manager konfigurujete prioritu koncového bodu explicitně pomocí vlastnosti priority pro každý koncový bod. Tato vlastnost je hodnota mezi 1 a 1000. Nižší hodnoty reprezentují vyšší prioritu. Koncové body nemůžou sdílet prioritní hodnoty. Nastavení vlastnosti je volitelné. Je-li tento parametr vynechán, je použita výchozí priorita na základě objednávky koncového bodu.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Vážená Metoda směrování provozu
Metoda "váženého" přenosu dat umožňuje distribuci provozu rovnoměrně nebo pro použití předem definovaného vážení.

![Služba Azure Traffic Manager vážená přenosová Metoda směrování](media/traffic-manager-routing-methods/weighted.png)

V metodě váženého směrování provozu přiřadíte každému koncovému bodu v konfiguraci profilu Traffic Manager váhu. Váha je celé číslo od 1 do 1000. Tento parametr je volitelný. Pokud tento parametr vynecháte, budou správci provozu používat výchozí váhu 1. Vyšší váha, čím vyšší je priorita.

Traffic Manager pro každý přijatý dotaz DNS náhodně vybere dostupný koncový bod. Pravděpodobnost výběru koncového bodu je založená na vahách, které jsou přiřazené ke všem dostupným koncovým bodům. Použití stejné váhy u všech koncových bodů má za následek i distribuci provozu. Použití vyšších nebo nižších vah u konkrétních koncových bodů způsobí, že tyto koncové body budou v odpovědích DNS vráceny více nebo méně často.

Vážená metoda umožňuje několik užitečných scénářů:

* Postupný upgrade aplikace: přidělte procento provozu pro směrování do nového koncového bodu a postupně zvyšujte provoz na 100%.
* Migrace aplikace do Azure: Vytvořte profil s použitím Azure i externích koncových bodů. Upravte váhu koncových bodů tak, aby dávala nové koncové body.
* Rozšiřování cloudů pro další kapacitu: rychlé rozšíření místního nasazení do cloudu tak, že ho umístíte za profil Traffic Manager. Pokud potřebujete dodatečnou kapacitu v cloudu, můžete přidat nebo povolit více koncových bodů a určit, jakou část provozu přechází na každý koncový bod.

Kromě používání Azure Portal můžete konfigurovat váhy pomocí Azure PowerShell, CLI a rozhraní REST API.

Je důležité pochopit, že odpovědi DNS ukládají do mezipaměti klienti a rekurzivní servery DNS, které klienti používají k překladu názvů DNS. Tato mezipaměť může mít dopad na vážené distribuce provozu. Pokud je počet klientů a rekurzivní servery DNS rozsáhlý, bude distribuce provozu fungovat podle očekávání. Pokud je ale počet klientů nebo rekurzivních serverů DNS malý, ukládání do mezipaměti může významně zkreslit distribuci provozu.

Mezi běžné případy použití patří:

* Vývojová a testovací prostředí
* Komunikace mezi aplikacemi
* Aplikace zaměřené na úzký základ uživatele, který sdílí společnou rekurzivní infrastrukturu DNS (například zaměstnanci společnosti připojující se přes proxy server)

Tyto účinky mezipaměti DNS jsou společné pro všechny systémy směrování provozu založené na DNS, ne jenom Azure Traffic Manager. V některých případech může být v případě explicitního vymazání mezipaměti DNS k dispozici alternativní řešení. V jiných případech může být vhodnější alternativní metoda směrování provozu.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Výkon výkonu – metoda směrování

Nasazení koncových bodů ve dvou nebo více umístěních po celém světě může zlepšit rychlost reakce mnoha aplikací tím, že směruje provoz do umístění, které je pro vás nejvhodnější. Tato funkce poskytuje metodu směrování provozu s výkonem.

![Provoz Azure Traffic Manager Performance – Metoda směrování](media/traffic-manager-routing-methods/performance.png)

Koncový bod "nejbližší" není nutně nejblíže měřený geografickou vzdáleností. Místo toho způsob směrování provozu určuje nejbližší koncový bod tím, že měří latenci sítě. Traffic Manager udržuje tabulku latence v Internetu, která sleduje dobu odezvy mezi rozsahy IP adres a jednotlivými datacentrem Azure.

Traffic Manager vyhledá zdrojovou IP adresu příchozího požadavku DNS v tabulce latence Internetu. Traffic Manager pak vybere dostupný koncový bod v datovém centru Azure s nejnižší latencí pro tento rozsah IP adres a vrátí tento koncový bod v odpovědi DNS.

Jak je vysvětleno, [jak Traffic Manager funguje](traffic-manager-how-it-works.md), Traffic Manager nepřijímá dotazy DNS přímo od klientů. Místo toho pocházejí dotazy DNS z rekurzivní služby DNS, které jsou nakonfigurovány pro použití. Proto IP adresa, která se používá k určení nejbližšího koncového bodu, není IP adresa klienta, ale jedná se o IP adresu rekurzivní služby DNS. V praxi je tato IP adresa pro klienta dobrým proxy serverem.


Traffic Manager pravidelně aktualizuje tabulku latence Internetu, aby se zohlednily změny v globálním Internetu a nových oblastech Azure. Výkon aplikace se ale liší v závislosti na změnách v reálném čase v rámci zátěže v Internetu. Přenosy výkonu – směrování nesleduje zatížení daného koncového bodu služby. Pokud ale dojde k nedostupnosti koncového bodu, Traffic Manager ho nezahrne do odpovědí na dotazy DNS.


Ukazuje na poznámku:

* Pokud váš profil obsahuje několik koncových bodů ve stejné oblasti Azure, Traffic Manager distribuuje provoz rovnoměrně napříč dostupnými koncovými body v dané oblasti. Pokud upřednostňujete jiné rozdělení provozu v rámci oblasti, můžete použít [vnořené profily Traffic Manager](traffic-manager-nested-profiles.md).
* Pokud jsou všechny povolené koncové body v nejbližší oblasti Azure degradovány, Traffic Manager přesunou provoz do koncových bodů v nejbližší nejbližší oblasti Azure. Pokud chcete definovat upřednostňovanou sekvenci převzetí služeb při selhání, použijte [vnořené profily Traffic Manager](traffic-manager-nested-profiles.md).
* Při použití metody směrování provozu výkonu s externími koncovými body nebo vnořenými koncovými body je nutné zadat umístění těchto koncových bodů. Vyberte oblast Azure, která je nejblíže vašemu nasazení. Tato umístění jsou hodnoty podporované tabulkou latence Internetu.
* Algoritmus, který zvolí koncový bod, je deterministický. Opakované dotazy DNS ze stejného klienta jsou směrovány do stejného koncového bodu. Klienti obvykle při cestování používají různé rekurzivní servery DNS. Klient může být směrován do jiného koncového bodu. Směrování může také ovlivnit aktualizace tabulky latence Internetu. Proto metoda směrování výkonu přenosu dat nezaručuje, že klient bude vždycky směrován do stejného koncového bodu.
* Když dojde ke změně tabulky latence Internetu, můžete si všimnout, že někteří klienti jsou přesměrováni na jiný koncový bod. Tato změna směrování je přesnější na základě aktuálních dat o latenci. Tyto aktualizace jsou nezbytné pro udržení přesnosti provozu s výkonem při průběžném vývojování v Internetu.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Geografické přenosy – Metoda směrování

Profily Traffic Manager je možné nakonfigurovat tak, aby používaly metodu geografického směrování, takže se uživatelé přesměrují do konkrétních koncových bodů (Azure, External nebo Nested) na základě geografického umístění, ze kterého jejich dotazy DNS pocházejí. To umožňuje Traffic Manager zákazníkům povolit scénáře, ve kterých je důležité, aby znali geografickou oblast uživatele a mohli je směrovat na základě důležitosti. Mezi příklady patří dodržování pověření k suverenitě dat, lokalizace obsahu & uživatelského prostředí a měření provozu z různých oblastí.
Pokud je pro geografické směrování nakonfigurovaný profil, musí mít každý koncový bod přidružený k tomuto profilu přiřazenou sadu geografických oblastí. Geografické oblasti můžou být následující úrovně členitosti. 
- World – Libovolná oblast
- Regionální seskupení – například Afrika, Střední východ, Austrálie/Tichomoří atd. 
- Země/oblast – například Irsko, Peru, Hongkong – zvláštní správní oblast atd. 
- Okres – například USA – Kalifornie, Austrálie – Queenslandu Canada-Alberta atd. (Poznámka: Tato úroveň členitosti je podporována pouze pro stavy/provincie v Austrálii, Kanadě a USA).

Když je oblast nebo sada oblastí přiřazena ke koncovému bodu, všechny požadavky z těchto oblastí budou směrovány pouze do tohoto koncového bodu. Traffic Manager pomocí zdrojové IP adresy dotazu DNS určí oblast, ze které se uživatel dotazuje – obvykle se jedná o IP adresu místního překladače DNS, který provádí dotaz jménem uživatele.  

![Služba Azure Traffic Manager "geografická" přenosová Metoda – směrování](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager přečte zdrojovou IP adresu dotazu DNS a rozhodnete, z jakých geografických oblastí pochází. Pak se zobrazí, zda je k dispozici koncový bod s namapovanou geografickou oblastí. Toto vyhledávání začíná na nejnižší úrovni členitosti (stát/provincie, kde je podporována, jinde na úrovni země/oblasti) a projde až do nejvyšší úrovně, která je **World**. První shoda nalezená pomocí tohoto průchodu je označena jako koncový bod, který se má vrátit v odpovědi na dotaz. Při shodě s vnořeným koncovým bodem je vrácen koncový bod v tomto podřízeném profilu na základě metody směrování. Následující body se vztahují na toto chování:

- Geografickou oblast lze mapovat pouze na jeden koncový bod v profilu Traffic Manager, pokud je typ směrování geografickým směrováním. Tím je zajištěno, že směrování uživatelů je deterministické a zákazníci mohou povolit scénáře, které vyžadují jednoznačné geografické hranice.
- Pokud se oblast uživatele nachází v rámci dvou různých koncových bodů "geografické mapování", Traffic Manager vybere koncový bod s nejnižší členitosti a nebere v úvahu požadavky směrování z této oblasti do druhého koncového bodu. Představte si třeba profil typu geografické směrování se dvěma koncovými body – Endpoint1 a pro endpoint2 u. Endpoint1 je nakonfigurovaný pro příjem provozu z Irska a pro endpoint2 u je nakonfigurovaný pro příjem provozu z Evropy. Pokud žádost pochází z Irska, je vždy směrována do Endpoint1.
- Vzhledem k tomu, že oblast může být mapována pouze na jeden koncový bod, Traffic Manager ji vrátí bez ohledu na to, zda je koncový bod v pořádku nebo ne.

    >[!IMPORTANT]
    >Důrazně doporučujeme, aby zákazníci, kteří používají geografickou metodu směrování, spojili s vnořenými koncovými body, které mají podřízené profily obsahující alespoň dva koncové body v každé z nich.
- Pokud se najde shoda koncového bodu a tento koncový bod je ve stavu **Zastaveno** , Traffic Manager vrátí odpověď na data. V takovém případě se v hierarchii geografických oblastí nezvýší žádná další hledání. Toto chování platí také pro vnořené typy koncových bodů, pokud je podřízený profil v **zastaveném** nebo **zakázaném** stavu.
- Pokud koncový bod zobrazí **zakázaný** stav, nebude zahrnutý v rámci procesu odpovídajícího regionu. Toto chování platí také pro vnořené typy koncových bodů, pokud je koncový bod v **zakázaném** stavu.
- Pokud dotaz pochází z geografické oblasti, která nemá v tomto profilu žádné mapování, Traffic Manager vrátí odpověď na DATA. Proto se důrazně doporučuje, aby zákazníci používali geografické směrování s jedním koncovým bodem, a to v ideálním případě typu vnořeného s alespoň dvěma koncovými body v rámci podřízeného profilu **s oblastí,** která je k ní přiřazená. Tím se také zajistí, že se zpracují všechny IP adresy, které nejsou namapované na oblast.

Jak je vysvětleno, [jak Traffic Manager funguje](traffic-manager-how-it-works.md), Traffic Manager nepřijímá dotazy DNS přímo od klientů. Místo toho pocházejí dotazy DNS z rekurzivní služby DNS, které jsou nakonfigurovány pro použití. Proto IP adresa, která se používá k určení oblasti, není IP adresa klienta, ale jedná se o IP adresu rekurzivní služby DNS. V praxi je tato IP adresa pro klienta dobrým proxy serverem.

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou některé případy použití, kde se geografické směrování hodí?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Návody rozhodnout, jestli mám použít metodu směrování výkonu nebo metodu geografického směrování?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Jaké jsou oblasti podporované Traffic Manager pro geografické směrování?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Jak Traffic Manager zjišťuje, ze kterého uživatele se dotazuje?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Je zaručeno, že Traffic Manager dokáže správně určit přesné geografické umístění uživatele v každém případě?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Musí být koncový bod fyzicky umístěný ve stejné oblasti, ve které je nakonfigurovaný pro geografické směrování?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Je možné přiřazovat geografické oblasti koncovým bodům v profilu, který není nakonfigurován tak, aby provedl geografické směrování?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Proč se při pokusu o změnu metody směrování stávajícího profilu na geografickou chybu zobrazuje chyba?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Proč doporučujeme, aby zákazníci vytvářeli vnořené profily místo koncových bodů v profilu s povoleným geografickým směrováním?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Existují nějaká omezení verze rozhraní API, která podporuje tento typ směrování?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Vyhodnotit metodu směrování provozu
Metoda vynásobení **přenosu provozu** umožňuje v jedné odpovědi na dotaz DNS získat několik zdravých koncových bodů. To umožňuje volajícímu provádět opakování na straně klienta s jinými koncovými body v případě, že vrácený koncový bod nereaguje. Tento model může zvýšit dostupnost služby a snížit latenci související s novým dotazem DNS za účelem získání fungujícího koncového bodu. Metoda směrování s více hodnotami funguje pouze v případě, že jsou všechny koncové body typu External a jsou zadány jako adresy IPv4 nebo IPv6. Při přijetí dotazu pro tento profil jsou vráceny všechny funkční koncové body a podléhají konfigurovatelnému maximálnímu počtu vrácených položek.

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou případy použití, ve kterých je vhodné použít směrování s více hodnotami?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Kolik koncových bodů se vrátí při použití směrování s více hodnotami?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Získá se stejná sada koncových bodů, když se použije směrování s více hodnotami?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Přenos podsítí – Metoda směrování
Metoda směrování provozu v **podsíti** umožňuje namapovat skupinu rozsahů IP adres koncového uživatele na konkrétní koncové body v profilu. Po takovém případě Traffic Manager obdrží dotaz DNS pro tento profil, zkontroluje zdrojovou IP adresu této žádosti (ve většině případů to bude odchozí IP adresa překladače DNS používaného volajícím), zjistit, ke kterému koncovému bodu je namapovaný, a vrátí tento koncový bod v odpovědi na dotaz. 

IP adresa, která se má namapovat na koncový bod, se dá zadat jako rozsahy CIDR (například 1.2.3.0/24) nebo jako rozsah adres (například 1.2.3.4-5.6.7.8). Rozsahy IP adres přidružené ke koncovému bodu musí být v rámci daného profilu jedinečné a nelze je překrývat se sadou IP adres jiného koncového bodu ve stejném profilu.
Pokud definujete koncový bod bez rozsahu adres, tato funkce funguje jako záložní a převezme provoz ze všech zbývajících podsítí. Pokud není součástí žádného záložního koncového bodu, Traffic Manager odešle odpověď na DATA pro všechny nedefinované rozsahy. Důrazně doporučujeme, abyste buď definovali záložní koncový bod, nebo jinak zajisti, že všechny možné rozsahy IP adres jsou zadané v rámci koncových bodů.

Směrování podsítí se dá využít k poskytování různých možností pro uživatele, kteří se připojují z konkrétního ADRESního prostoru. Při použití směrování podle podsítě například zákazník může odesílat všechny žádosti z firemní kanceláře do různých koncových bodů, ve kterých může testovat pouze interní verze své aplikace. Dalším scénářem je situace, kdy chcete uživatelům, kteří se připojují z určitého poskytovatele internetových služeb, poskytovat jiné prostředí (například chcete blokovat uživatele, kteří se připojují z konkrétního poskytovatele internetových služeb).

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou případy použití, kde je směrování podsítě užitečné?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Jak Traffic Manager znát IP adresu koncového uživatele?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Jak lze při používání směrování podsítí zadat IP adresy?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Jak můžu při používání směrování podsítí zadat záložní koncový bod?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Co se stane, když je v profilu typu směrování podsítě zakázán koncový bod?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Další kroky

Naučte se vyvíjet aplikace s vysokou dostupností pomocí [Traffic Manager monitorování koncového bodu](traffic-manager-monitoring.md) .