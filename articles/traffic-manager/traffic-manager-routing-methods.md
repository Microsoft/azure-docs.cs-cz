---
title: Azure Traffic Manager – metody směrování provozu
description: Tento článek vám pomůže pochopit různé metody směrování provozu používané traffic managerem
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250904"
---
# <a name="traffic-manager-routing-methods"></a>Metody směrování Traffic Manageru

Azure Traffic Manager podporuje šest metod směrování provozu k určení, jak směrovat síťový provoz do různých koncových bodů služby. U každého profilu aplikace Traffic Manager použije metodu směrování provozu, která je k němu přidružena, pro každý dotaz DNS, který obdrží. Metoda směrování provozu určuje, který koncový bod je vrácen v odpovědi DNS.

Ve Správci provozu jsou k dispozici následující metody směrování provozu:

* **[Priorita](#priority-traffic-routing-method):** Vyberte **prioritu,** pokud chcete použít koncový bod primární služby pro veškerý provoz a poskytnout zálohy v případě, že primární nebo záložní koncové body nejsou k dispozici.
* **[Vážený](#weighted):** Vyberte **vážený,** pokud chcete distribuovat provoz mezi sadu koncových bodů, rovnoměrně nebo podle hmotnosti, které definujete.
* **[Výkon](#performance):** Vyberte **výkon,** pokud máte koncové body v různých geografických umístěních a chcete, aby koncoví uživatelé používali "nejbližší" koncový bod z hlediska nejnižší latence sítě.
* **[Geografické](#geographic):** Vyberte **geografické** tak, aby uživatelé byli přesměrováni na konkrétní koncové body (Azure, Externí nebo Vnořené) na základě zeměpisné polohy jejich DNS dotaz pochází. To umožňuje zákazníkům Traffic Manageru povolit scénáře, kde je důležité znát geografickou oblast uživatele a směrovat je na základě toho. Mezi příklady patří dodržování mandátů suverenity dat, lokalizace obsahu & uživatelské zkušenosti a měření návštěvnosti z různých oblastí.
* **[Vícehodnot](#multivalue):** Vyberte **profily MultiValue** pro Traffic Manager, které mohou mít jako koncové body pouze adresy IPv4/IPv6. Při přijetí dotazu pro tento profil jsou vráceny všechny koncové body v pořádku.
* **[Podsíť](#subnet):** Vyberte metodu směrování provozu **podsítě,** chcete-li mapovat sady rozsahů IP adres koncového uživatele na konkrétní koncový bod v rámci profilu traffic manageru. Při přijetí požadavku bude vrácený koncový bod namapován pro zdrojovou IP adresu tohoto požadavku. 


Všechny profily Traffic Manager zahrnují sledování stavu koncového bodu a automatické převzetí služeb při selhání koncového bodu. Další informace naleznete v tématu [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Jeden profil traffic manageru může používat pouze jednu metodu směrování provozu. Pro svůj profil můžete kdykoli vybrat jinou metodu směrování provozu. Změny se použijí během jedné minuty a nedojde k žádnému prostoju. Metody směrování provozu lze kombinovat pomocí vnořených profilů Traffic Manageru. Vnoření umožňuje sofistikované a flexibilní konfigurace směrování provozu, které splňují potřeby větších a složitých aplikací. Další informace naleznete v [vnořených profilech traffic manageru](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Metoda prioritního směrování provozu

Organizace chce často poskytovat spolehlivost svých služeb nasazením jedné nebo více služeb zálohování v případě, že jejich primární služba klesne. Metoda směrování provozu "Priority" umožňuje zákazníkům Azure snadno implementovat tento vzor převzetí služeb při selhání.

![Metoda směrování provozu služby Priority azure traffic manageru](media/traffic-manager-routing-methods/priority.png)

Profil Traffic Manageru obsahuje seznam koncových bodů služby seřazených podle priority. Ve výchozím nastavení Traffic Manager odesílá veškerý provoz na primární koncový bod (s nejvyšší prioritou). Pokud primární koncový bod není dostupný, Traffic Manager směruje provoz do druhého koncového bodu v seznamu. Pokud není dostupný primární ani druhý koncový bod, provoz se bude směrovat do třetího koncového bodu atd. Dostupnost koncového bodu závisí na nakonfigurovaném stavu (povoleno nebo zakázáno) a na průběžném monitorování koncového bodu.

### <a name="configuring-endpoints"></a>Konfigurace koncových bodů

Pomocí Správce prostředků Azure nakonfigurujete prioritu koncového bodu explicitně pomocí vlastnosti "priority" pro každý koncový bod. Tato vlastnost je v hodnotě od 1 do 1000. Nižší hodnoty představují vyšší prioritu. Koncové body nemohou sdílet hodnoty priority. Nastavení vlastnosti je volitelné. Při vynechání se použije výchozí priorita založená na pořadí koncového bodu.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metoda váženého směrování provozu
Metoda "Vážené" směrování provozu umožňuje rovnoměrně distribuovat provoz nebo použít předem definované vážení.

![Azure Traffic Manager 'Vážený' traffic-routing metoda](media/traffic-manager-routing-methods/weighted.png)

V metodě sledování vážených přenosů přiřadíte každému koncovému bodu v konfiguraci profilu traffic manageru váhu. Váha je celé číslo od 1 do 1000. Tento parametr je volitelný. Pokud je to vynecháno, traffic managers používá výchozí váhu "1". Čím vyšší hmotnost, tím vyšší je priorita.

Traffic Manager pro každý přijatý dotaz DNS náhodně vybere dostupný koncový bod. Pravděpodobnost výběru koncového bodu je založená na vahách, které jsou přiřazené ke všem dostupným koncovým bodům. Použití stejné hmotnosti ve všech koncových bodech má za následek rovnoměrné rozložení provozu. Použití vyšší nebo nižší váhy na konkrétní koncové body způsobí, že tyto koncové body, které mají být vráceny více či méně často v odpovědích DNS.

Vážená metoda umožňuje některé užitečné scénáře:

* Postupný upgrade aplikace: Přidělit procento provozu na trasu do nového koncového bodu a postupně zvyšovat provoz v průběhu času na 100 %.
* Migrace aplikací do Azure: Vytvořte profil s Azure i externími koncovými body. Upravte hmotnost koncových bodů tak, aby upřednostňovala nové koncové body.
* Cloud-bursting pro další kapacitu: Rychle rozšířit místní nasazení do cloudu tím, že ho za profil Traffic Manager. Když potřebujete další kapacitu v cloudu, můžete přidat nebo povolit další koncové body a určit, jaká část provozu přejde do každého koncového bodu.

Kromě použití portálu Azure můžete konfigurovat váhy pomocí Azure PowerShell, CLI a REST API.

Je důležité si uvědomit, že odpovědi DNS jsou ukládány do mezipaměti klienty a rekurzivními servery DNS, které klienti používají k překladu názvů DNS. Toto ukládání do mezipaměti může mít vliv na vážené rozložení provozu. Pokud je počet klientů a rekurzivních serverů DNS velký, distribuce provozu funguje podle očekávání. Pokud je však počet klientů nebo rekurzivních serverů DNS malý, ukládání do mezipaměti může výrazně zkreslit distribuci přenosů.

Mezi běžné případy použití patří:

* Vývojová a testovací prostředí
* Komunikace mezi aplikacemi
* Aplikace zaměřené na úzkou uživatelskou základnu, které sdílejí společnou rekurzivní infrastrukturu DNS (například zaměstnanci společnosti, kteří se připojují prostřednictvím proxy serveru)

Tyto efekty ukládání do mezipaměti DNS jsou společné pro všechny systémy směrování provozu založené na DNS, nejen na Azure Traffic Manageru. V některých případech může explicitně vymazání mezipaměti DNS poskytnout řešení. V ostatních případech může být vhodnější alternativní způsob směrování provozu.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Metoda směrování provozu výkonu

Nasazení koncových bodů ve dvou nebo více umístěních po celém světě může zlepšit odezvu mnoha aplikací směrováním provozu do umístění, které je vám nejblíže. Tuto možnost poskytuje metoda směrování provozu "Performance".

![Metoda směrování provozu nástroje Azure Traffic Manager "Výkon"](media/traffic-manager-routing-methods/performance.png)

"Nejbližší" koncový bod není nutně nejblíže, měřeno podle zeměpisné vzdálenosti. Místo toho metoda směrování provozu "Výkon" určuje nejbližší koncový bod měřením latence sítě. Traffic Manager udržuje internetlatence tabulka sledovat dobu odezvy mezi rozsahy IP adres a každé datové centrum Azure.

Traffic Manager vyhledá zdrojovou IP adresu příchozího požadavku DNS v tabulce latence Internetu. Traffic Manager pak vybere dostupný koncový bod v datovém centru Azure, který má nejnižší latenci pro tento rozsah IP adres a vrátí tento koncový bod v odpovědi DNS.

Jak je vysvětleno v části [Jak funguje traffic manager](traffic-manager-how-it-works.md), Traffic Manager nepřijímá dotazy DNS přímo od klientů. Dotazy DNS pocházejí spíše z rekurzivní služby DNS, kterou jsou klienti nakonfigurováni k použití. Adresa IP použitá k určení koncového bodu "nejbližší" tedy není IP adresou klienta, ale ip adresou rekurzivní služby DNS. V praxi je tato adresa IP dobrým proxy serverem pro klienta.


Traffic Manager pravidelně aktualizuje tabulku latence Internetu, aby zohlednil změny v globálním internetu a nových oblastech Azure. Výkon aplikací se však liší v závislosti na změnách zatížení v síti Internet v reálném čase. Směrování provozu výkonu nesleduje zatížení v daném koncovém bodu služby. Pokud však koncový bod přestane být k dispozici, Traffic Manager jej nezahrne do odpovědí na dotaz DNS.


Body k poznámce:

* Pokud váš profil obsahuje více koncových bodů ve stejné oblasti Azure, traffic manager distribuuje provoz rovnoměrně mezi dostupné koncové body v této oblasti. Pokud dáváte přednost jinému rozdělení provozu v rámci oblasti, můžete použít [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).
* Pokud jsou všechny povolené koncové body v nejbližší oblasti Azure degradovány, Traffic Manager přesune provoz do koncových bodů v nejbližší oblasti Azure. Chcete-li definovat upřednostňovanou sekvenci převzetí služeb při selhání, použijte [vnořené profily traffic manageru](traffic-manager-nested-profiles.md).
* Při použití metody směrování přenosů výkonu s externími koncovými body nebo vnořenými koncovými body je třeba zadat umístění těchto koncových bodů. Zvolte oblast Azure, která je nejblíže vašemu nasazení. Tato umístění jsou hodnoty podporované internetovou latencí tabulka.
* Algoritmus, který zvolí koncový bod je deterministický. Opakované dotazy DNS ze stejného klienta jsou směrovány do stejného koncového bodu. Klienti obvykle používají různé rekurzivní servery DNS při cestování. Klient může být směrován do jiného koncového bodu. Směrování může být také ovlivněno aktualizacemi tabulky latence Internetu. Proto metoda směrování provozu výkonu nezaručuje, že klient je vždy směrován do stejného koncového bodu.
* Když se změní tabulka latence Internetu, můžete si všimnout, že někteří klienti jsou přesměrováni na jiný koncový bod. Tato změna směrování je přesnější na základě aktuálních dat latence. Tyto aktualizace jsou nezbytné pro zachování přesnosti směrování provozu výkonu při neustále se vyvíjí V Internetu.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Metoda geografického směrování provozu

Profily Traffic Manageru lze nakonfigurovat tak, aby používaly metodu geografického směrování tak, aby uživatelé byli přesměrováni na konkrétní koncové body (Azure, External nebo Nested), na základě kterého zeměpisného umístění, odkud jejich dotaz DNS pochází. To umožňuje zákazníkům Traffic Manageru povolit scénáře, kde je důležité znát geografickou oblast uživatele a směrovat je na základě toho. Mezi příklady patří dodržování mandátů suverenity dat, lokalizace obsahu & uživatelské zkušenosti a měření návštěvnosti z různých oblastí.
Pokud je profil nakonfigurován pro geografické směrování, musí mít každý koncový bod přidružený k tomuto profilu přiřazenu sadu geografických oblastí. Zeměpisná oblast může být na následujících úrovních rozlišovací schopnosti 
- Svět – jakýkoli region
- Regionální uskupení – například Afrika, Střední východ, Austrálie/Tichomoří atd. 
- Země/region – například Irsko, Peru, Hongkongská zvláštní správní oblast atd. 
- Stát/provincie – například USA-Kalifornie, Austrálie-Queensland, Kanada-Alberta atd.(Poznámka: tato úroveň rozlišovací schopnost je podporována pouze pro státy / provincie v Austrálii, Kanadě a USA).

Pokud oblast nebo sada oblastí je přiřazena ke koncovému bodu, všechny požadavky z těchto oblastí získá směrovány pouze do tohoto koncového bodu. Traffic Manager používá zdrojovou IP adresu dotazu DNS k určení oblasti, ze které se uživatel dotazuje – obvykle se jedná o IP adresu místního překladače DNS, který provádí dotaz jménem uživatele.  

![Metoda směrování provozu Azure Traffic Manageru](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager přečte zdrojovou IP adresu dotazu DNS a rozhodne, ze které zeměpisné oblasti pochází. Potom se zobrazí, pokud je koncový bod, který má tuto geografickou oblast mapovány na něj. Toto vyhledávání začíná na nejnižší úrovni rozlišovací schopnosti (stát/provincie, kde je podporován, jinak na úrovni Země/oblast) a vede až na nejvyšší úroveň, která je **Svět**. První shoda nalezena pomocí tohoto průchodu je určen jako koncový bod vrátit v odpovědi na dotaz. Při porovnávání s koncovým bodem vnořeného typu je vrácen koncový bod v rámci tohoto podřízeného profilu na základě jeho metody směrování. Následující body se vztahují na toto chování:

- Geografickou oblast lze mapovat pouze na jeden koncový bod v profilu Traffic Manageru, pokud je typem směrování geografické směrování. Tím je zajištěno, že směrování uživatelů je deterministický a zákazníci mohou povolit scénáře, které vyžadují jednoznačné zeměpisné hranice.
- Pokud oblast uživatele spadá pod geografické mapování dvou různých koncových bodů, Traffic Manager vybere koncový bod s nejnižší rozlišovací schopnost a nebere v úvahu směrování požadavků z této oblasti do jiného koncového bodu. Zvažte například profil typu geografického směrování se dvěma koncovými body – Koncový bod1 a Koncový bod2. Koncový bod1 je nakonfigurován pro příjem provozu z Irska a Koncový bod2 je nakonfigurován pro příjem provozu z Evropy. Pokud požadavek pochází z Irska, je vždy směrovándo Koncového bodu1.
- Vzhledem k tomu, že oblast může být mapována pouze na jeden koncový bod, Traffic Manager vrátí bez ohledu na to, zda je koncový bod v pořádku nebo ne.

    >[!IMPORTANT]
    >Důrazně doporučujeme, aby zákazníci, kteří používají metodu geografického směrování, ji přidružovali ke koncovým bodům vnořeného typu, které obsahují podřízené profily obsahující alespoň dva koncové body v rámci každého z nich.
- Pokud je nalezena shoda koncového bodu a tento koncový bod je ve stavu **Zastaveno,** Traffic Manager vrátí odpověď NODATA. V tomto případě žádné další vyhledávání jsou provedeny vyšší v hierarchii geografické oblasti. Toto chování je také použitelné pro vnořené typy koncových bodů, když je podřízený profil ve stavu **Zastaveno** nebo **Zakázáno.**
- Pokud koncový bod zobrazí **stav Zakázáno,** nebude zahrnut do procesu párování oblasti. Toto chování je také použitelné pro vnořené typy koncových bodů, když je koncový bod ve stavu **Zakázáno.**
- Pokud dotaz pochází ze zeměpisné oblasti, která nemá žádné mapování v tomto profilu, Traffic Manager vrátí odpověď NODATA. Proto důrazně doporučujeme, aby zákazníci používali geografické směrování s jedním koncovým bodem, ideálně typu Vnořené s alespoň dvěma koncovými body v rámci podřízeného profilu, s přiřazenou oblastí **World.** Tím je také zajištěno, že všechny IP adresy, které nejsou mapovat na oblast jsou zpracovány.

Jak je vysvětleno v části [Jak funguje traffic manager](traffic-manager-how-it-works.md), Traffic Manager nepřijímá dotazy DNS přímo od klientů. Dotazy DNS pocházejí spíše z rekurzivní služby DNS, kterou jsou klienti nakonfigurováni k použití. Adresa IP použitá k určení oblasti proto není ip adresou klienta, ale ip adresou rekurzivní služby DNS. V praxi je tato adresa IP dobrým proxy serverem pro klienta.

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou některé případy použití, kdy je užitečné geografické směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Jak se rozhodnu, jestli mám použít metodu směrování výkonu nebo metodu geografického směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Jaké oblasti, které traffic manager podporuje pro geografické směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Jak správce provozu určuje, odkud se uživatel dotazuje?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Je zaručeno, že Traffic Manager může správně určit přesnou zeměpisnou polohu uživatele v každém případě?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Musí být koncový bod fyzicky umístěn ve stejné oblasti, jakou je nakonfigurován pro geografické směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Je možné přiřadit geografické oblasti koncovým bodům v profilu, který není nakonfigurován pro geografické směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Proč se při pokusu o změnu metody směrování existujícího profilu na geografický zobrazuje chyba?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Proč se důrazně doporučuje, aby zákazníci vytvářeli vnořené profily namísto koncových bodů pod profilem s povoleným geografickým směrováním?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Existují nějaká omezení pro verzi rozhraní API, která podporuje tento typ směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Vícehodnotová metoda směrování provozu
Metoda směrování provozu **více hodnot** umožňuje získat více koncových bodů v pořádku v jedné odpovědi na dotaz DNS. To umožňuje volajícímu provést opakování na straně klienta s jinými koncovými body v případě, že vrácený koncový bod neodpovídá. Tento model může zvýšit dostupnost služby a snížit latenci související s novým dotazem DNS za účelem získání fungujícího koncového bodu. Metoda směrování MultiValue funguje pouze v případě, že všechny koncové body typu Externí a jsou určeny jako adresy IPv4 nebo IPv6. Při přijetí dotazu pro tento profil jsou vráceny všechny koncové body v pořádku a podléhají konfigurovatelnému maximálnímu počtu vrácených dat.

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou některé případy použití, kdy je užitečné směrování MultiValue?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Kolik koncových bodů jsou vráceny při použití směrování MultiValue?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Získám stejnou sadu koncových bodů při použití směrování MultiValue?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Metoda směrování provozu podsítě
Metoda směrování provozu **podsítě** umožňuje mapovat sadu rozsahů IP adres koncového uživatele na konkrétní koncové body v profilu. Poté, pokud Traffic Manager obdrží dotaz DNS pro tento profil, zkontroluje zdrojovou IP adresu tohoto požadavku (ve většině případů to bude odchozí IP adresa překladače DNS používaného volajícím), určit, na který koncový bod je mapován, a vrátí se tento koncový bod v odpovědi na dotaz. 

IP adresu, která má být mapována na koncový bod, lze zadat jako rozsahy CIDR (např. 1.2.3.0/24) nebo jako rozsah adres (např. 1.2.3.4-5.6.7.8). Rozsahy IP přidružené ke koncovému bodu musí být v rámci tohoto profilu jedinečné a nemohou se překrývat se sadou IP adres jiného koncového bodu ve stejném profilu.
Pokud definujete koncový bod bez rozsahu adres, který funguje jako záložní a převzít provoz ze všech zbývajících podsítí. Pokud není zahrnut žádný záložní koncový bod, Traffic Manager odešle odpověď NODATA pro všechny nedefinované rozsahy. Proto důrazně doporučujeme definovat záložní koncový bod, nebo zajistit, že všechny možné rozsahy IP jsou určeny v rámci koncových bodů.

Směrování podsítě lze použít k zajištění jiného prostředí pro uživatele, kteří se připojují z určitého místa IP. Při použití směrování podle podsítě například zákazník může odesílat všechny žádosti z firemní kanceláře do různých koncových bodů, ve kterých může testovat pouze interní verze své aplikace. Dalším scénářem je situace, kdy chcete uživatelům, kteří se připojují z určitého poskytovatele internetových služeb, poskytovat jiné prostředí (například chcete blokovat uživatele, kteří se připojují z konkrétního poskytovatele internetových služeb).

### <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou některé případy použití, kdy je směrování podsítě užitečné?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Jak Traffic Manager zná IP adresu koncového uživatele?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Jak lze zadat adresy IP při použití směrování podsítě?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Jak lze zadat záložní koncový bod při použití směrování podsítě?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Co se stane, pokud je koncový bod v profilu typu směrování podsítě zakázán?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Další kroky

Naučte se vyvíjet aplikace s vysokou dostupností pomocí [sledování koncových bodů Traffic Manageru](traffic-manager-monitoring.md)




