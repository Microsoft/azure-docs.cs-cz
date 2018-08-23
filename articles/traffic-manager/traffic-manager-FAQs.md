---
title: Azure Traffic Manager – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy o Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: 6c196d16258e4bf000f998899086c7a6d0197fba
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058029"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager – nejčastější dotazy (FAQ)

## <a name="traffic-manager-basics"></a>Základní informace o Traffic Manageru

### <a name="what-ip-address-does-traffic-manager-use"></a>Jaké IP adresy používá Traffic Manager?

Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager pracuje na úrovni DNS. Odesílání odpovědí DNS k nasměrování klientů na příslušné službě koncového bodu. Klienti se pak připojují ke koncovému bodu služby přímo, ne pomocí Traffic Manageru.

Proto se Traffic Manager neposkytuje koncovému bodu nebo IP adresu pro připojení klientů k. Pokud chcete statickou IP adresu pro vaši službu, která musí být nakonfigurované na službu, není v Traffic Manageru.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Co může být typy provozu směruje pomocí Traffic Manageru?
Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), koncový bod služby Traffic Manager může být internetové služby hostované v Azure nebo mimo něj. Proto Traffic Manager může směrovat provoz pocházející z veřejného Internetu do sady koncových bodů, že jsou také Internetu. Pokud máte koncové body, které jsou v privátní síti (například na interní verze [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)) nebo uživatel provedete DNS požadavků Traffic Manageru nelze použít pro tyto přenosy tyto interní sítě.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager podporuje "rychlé" relace?

Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager pracuje na úrovni DNS. Využívá odpovědi služby DNS k nasměrování klientů na příslušné službě koncového bodu. Klienti připojení ke koncovému bodu služby přímo, ne pomocí Traffic Manageru. Proto Traffic Manageru nezobrazují přenos pomocí protokolu HTTP mezi klientem a serverem.

Kromě toho zdrojové IP adresy přijaté Traffic Managerem dotazy DNS patří do rekurzivní službu DNS, ne klienta. Traffic Manageru, proto nemá žádný způsob, jak sledovat jednotlivé klienty a nemůže implementovat "rychlé" relace. Toto omezení je společný pro všechny systémy správy provozu na základě DNS a není specifická pro Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Proč se mi zobrazuje chyba HTTP při použití Traffic Manageru?

Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager pracuje na úrovni DNS. Využívá odpovědi služby DNS k nasměrování klientů na příslušné službě koncového bodu. Klienti se pak připojují ke koncovému bodu služby přímo, ne pomocí Traffic Manageru. Traffic Manager nemá viz přenosy HTTP není mezi klientem a serverem. Proto všechny Chyba protokolu HTTP, který se zobrazí musí pocházet z vaší aplikace. Aby klient mohl připojit k aplikaci se dokončí všechny kroky řešení DNS. To zahrnuje všechny interakce, který má Traffic Manageru na tok provozu aplikace.

Další šetření byste proto se zaměřit na aplikace.

Hlavička hostitele HTTP odeslané do prohlížeče klienta je nejběžnější příčiny problémů. Ujistěte se, že aplikace je nakonfigurovaná tak, aby přijímal hlavičku hostitele správná pro název domény, který používáte. Koncové body pomocí služby Azure App Service, najdete v části [konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Co je dopad na výkon pomocí Traffic Manageru?

Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager pracuje na úrovni DNS. Protože klienti připojení koncových bodů služby přímo, neexistuje žádný dopad na výkon při použití Traffic Manageru, jakmile se naváže připojení.

Vzhledem k tomu, že Traffic Manager se integruje s aplikacemi na úrovni DNS, vyžaduje další vyhledávání DNS má být vložen do řetězu překlad DNS. Je minimální dopad na dobu překladu názvů DNS Traffic Manageru. Traffic Manager využívá globální síť názvových serverů a využívá [anycast](https://en.wikipedia.org/wiki/Anycast) sítě zajistit DNS dotazy jsou vždy směrovány do nejbližší dostupné názvový server. Kromě toho ukládání do mezipaměti odpovědí DNS znamená, že další latence DNS při pomocí Traffic Manageru aplikuje pouze na zlomek relací.

Metoda výkonu směruje provoz na nejbližší dostupný koncový bod. Net výsledkem je, že by měl být minimální dopad na celkový výkon přidružený k této metodě. Každé zvýšení počtu latence DNS by měla být pokryta nižší latence sítě ke koncovému bodu.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jaké aplikační protokoly je možné použít s využitím Traffic Manageru?

Jak je vysvětleno v [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager pracuje na úrovni DNS. Po dokončení vyhledávání DNS se klienti připojovat k koncový bod aplikace přímo, ne pomocí Traffic Manageru. Připojení proto můžete použít jakýkoli protokol aplikace. Pokud vyberete TCP jako monitorování protokolu, Traffic Manager koncový bod monitorování stavu se dá udělat bez použití jakékoli aplikačních protokolů. Pokud budete chtít mít stav ověření pomocí protokolu aplikace, koncový bod musí být schopné reagovat na požadavky HTTP nebo HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Můžete použít Traffic Manageru s názvem "základní" doména?

Ne. Standardy DNS nepovolují záznamy CNAME, aby nevznikaly konflikty s další záznamy DNS se stejným názvem. Vrcholu (nebo kořenový) zóny DNS vždy obsahuje dva existující záznamy DNS; záznamy autoritativních NS a SOA. To znamená, že nelze aniž by byla porušena standardy DNS vytvořit záznam CNAME, který ve vrcholu zóny.

Traffic Manager vyžaduje záznam DNS CNAME pro mapování názvu DNS jednoduché. Například můžete namapovat `www.contoso.com` jako název DNS profilu Traffic Manageru `contoso.trafficmanager.net`. Kromě toho profilu služby Traffic Manager vrátí druhý záznam CNAME DNS označíte, který koncový bod by měl klient připojit k.

Chcete-li tento problém obejít, doporučujeme používat přesměrování HTTP směrovat přenos dat ze základní doména jméno na jinou adresu URL, které pak může použít k Traffic Manageru. Základní doména "contoso.com" může například přesměrovat uživatele na CNAME "www.contoso.com", který odkazuje na název DNS Traffic Manageru.

Plnou podporu pro základní domény ve službě Traffic Manager je sledována v backlogu a funkce. Můžete zaregistrovat vaše podpora pro tuto žádost o funkci ve [pro ni hlasovat o náš web pro zasílání názorů komunity](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manageru zvažte adresa podsítě klienta při zpracování dotazů DNS 
Ano, kromě IP adresu zdrojového dotazu DNS obdrží (což obvykle je IP adresa překladače DNS), když provádíte vyhledávání pro Geographic a výkonu metod směrování, traffic Manageru také bere v úvahu adresa podsítě klienta Pokud je zahrnutý v dotazu pomocí překladače, který zadal žádost jménem koncového uživatele.  
Konkrétně [RFC 7871 – klientské podsíti v dotazech DNS](https://tools.ietf.org/html/rfc7871) poskytující [mechanismus rozšíření pro službu DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) které můžete předat na adresu podsítě klienta z překladače, kteří jej podporují.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co je hodnotu TTL pro DNS a jak to ovlivní Moji uživatelé?

Pokud dotaz DNS jsou v Traffic Manageru, nastaví hodnotu v odpovědi nazývá time to live (TTL). Tato hodnota, jejíž jednotka je v sekundách, označuje do překladače DNS směru server-klient na jak dlouho pro ukládání do mezipaměti této odpovědi. Zatímco překladače DNS není zaručena pro ukládání do mezipaměti tohoto výsledku, ukládání do mezipaměti umožňuje jim reagovat na následující dotazy vypnout mezipaměti místo přechodu na servery DNS Traffic Manageru. To má vliv na odpovědi následujícím způsobem:
- vyšší hodnoty TTL snižuje počet dotazů, které se objeví na serverech DNS Traffic Manageru, které můžete snížit náklady na zákazníka, protože počet dotazů, které obsluhují je fakturovatelná spotřeba.
- vyšší hodnoty TTL můžete potenciálně snížili množství času, kterou trvá proveďte vyhledání DNS.
- vyšší hodnoty TTL také znamená, že vaše data neodráží nejnovějších informací o stavu, které Traffic Manager je získali prostřednictvím jeho zjišťování agentů.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak vysoká nebo Nízká můžete nastavit hodnotu TTL pro odpovědi Traffic Manageru?

Můžete nastavit, v na úroveň profilu, hodnotu TTL pro DNS v rozsahu od 0 sekund a tak vysoké jako 2 147 483 647 sekund (kompatibilní s maximální rozsah [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). Hodnota TTL 0 znamená, že podřízené překladače DNS do mezipaměti odpovědi na dotazy a se očekává, že všechny dotazy DNS Traffic Manageru mohly spojit se servery pro překlad.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak můžete porozumět objem dotazů přicházející do svého profilu? 
Jednu z metrik uvedených Traffic Managerem je počet dotazů v rámci profilu odpověděl. Tyto informace můžete získat na úrovni agregace profilu nebo můžete rozdělit ho uvidět objem dotazů, ve kterém byly vráceny konkrétní koncové body. Kromě toho můžete nastavit upozornění, která upozorní, pokud objem odpovědi dotazu překročí podmínky, že jste nastavili. Další podrobnosti [Traffic Manageru metrik a výstrah](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metody směrování provozu Traffic Manageru Geographic

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jaké jsou některé případy použití, ve kterém geografické směrování je užitečné? 
Geografické směrování typu lze použít ve všech scénářích, kde zákazník Azure potřebuje k rozlišení jejich uživatelů podle geografických oblastí. Například pomocí geografickou metodu směrování provozu, můžete uživatelům udělit z konkrétních oblastí a činnost koncových uživatelů jiné než ty, které z jiných oblastí. Dalším příkladem je dodržování pověření místní datové suverenity, vyžadující, že uživatelé z určité oblasti být obsluhovaný pouze koncové body v dané oblasti.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak můžu rozhodnout, pokud mám použít metody směrování podle výkonu nebo metody geografického směrování? 
Klíčovým rozdílem mezi těmito dvěma metodami oblíbených směrování je, že v metody, které primární cílem je odesílat provoz do koncového bodu, který může poskytnout nejnižší latenci volajícímu, že v Geographic směrování hlavním cílem je vynucení zeměpisné oblasti směrování výkonu plotu pro vaši volající tak, aby je záměrně směrovat do určitého koncového bodu. Překrytí se stane vzhledem k tomu, že existuje korelace mezi zeměpisné těsnost a nižší latenci, i když to není vždy hodnotu true. V různých zeměpisné oblasti, která dokáže poskytovat lepší prostředí latence pro volající může být koncový bod a v takovém případě směrování podle výkonu odešle uživatel do tohoto koncového bodu, ale geografické směrování bude vždy odesílat je do koncového bodu pro namapování jejich zeměpisné oblasti. Dále nastavte ji vymazat, zvažte následující příklad – s Geographic směrování, můžete nastavit neobvyklé mapování jako odesílat veškerý přenos v Asii ke koncovým bodům v USA a všechny USA provozu do koncových bodů v Asii. V takovém případě geografické směrování bude provádět záměrně, přesně jak jste nakonfigurovali na práci a optimalizace výkonu není potřeba. 
>[!NOTE]
>Můžou existovat situacích, kdy můžete potřebovat i výkon a geografické směrování možnosti pro tyto scénáře vnořených profilů může být skvělou volbu. Například můžete nastavit nadřazený profil s geografické směrování, kde odesílat veškerý přenos v Severní Americe do vnořeného profilu, který má koncové body v USA a používat k posílání těchto provozu nejlepší koncový bod v rámci této sady směrování výkonu. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Co jsou oblasti, které jsou podporovány nástrojem Traffic Manager pro geografické směrování? 
Hierarchie zemí/oblastí, který se používá Traffic Managerem můžete najít [tady](traffic-manager-geographic-regions.md). Zatímco tato stránka je veden odpovídalo nejnovějším změnám, můžete také programově načítat stejné informace s použitím [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak traffic manager Určuje, kde je uživatel dotazování z? 
Traffic Manager zjistí Zdrojová IP adresa dotazu (je to pravděpodobně místního překladače DNS způsobem dotazování jménem uživatele) a používá interní IP adresu do oblasti mapy k určení umístění. Toto mapování se aktualizuje průběžně pro změny v Internetu. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>To zaručuje, že Traffic Manager správně zjistit přesnou zeměpisnou polohu uživatele v každém případě?
Ne, Traffic Manager nemůže zaručit, že geografické oblasti, které jsme odvodit od zdrojové IP adresy pomocí dotazu DNS bude vždycky odpovídají podle umístění uživatele z následujících důvodů: 

- Nejprve jak je popsáno v předchozí – nejčastější dotazy, zdrojové IP adresy, které můžeme vidět je, že překladače DNS způsobem vyhledávání jménem uživatele. Geografické umístění Překladač DNS je dobré proxy pro geografické umístění uživatele, může být jiný v závislosti na nároky překladač služby DNS a konkrétní službu Překladač DNS, kterou zákazník se rozhodli použít. Například zákazník nachází v Malajsii zadat jejich zařízení nastavení používá překladač služby DNS, server DNS, in Singapore může získat vybere zpracování dotazu řešení pro uživatele nebo zařízení. V takovém případě Traffic Manageru uvidí jenom překladač IP adresu, která odpovídá Singapur umístění. Viz také starší – nejčastější dotazy týkající se podpory adresa podsítě klienta na této stránce.

- Za druhé Traffic Manager používá interní mapy provedete IP adresu pro překlad geografické oblasti. Přestože toto mapování se ověří a aktualizovat průběžně zvýšit jeho přesnost a zohlednily vyvíjející povaze internet, je stále možné, že naše informace nejsou přesnou reprezentací zeměpisné umístění všech IP adresy.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Koncový bod musí být fyzicky umístěné ve stejné oblasti jako ta, kterou má nakonfigurovanou pro geografické směrování? 
Ne, ukládá umístění koncového bodu bez omezení, na kterých lze k němu mapována oblastech. Koncový bod v oblasti Azure USA – střed můžete mít například všichni uživatelé z Indie směrované do něj.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Můžete přiřadit geografických oblastí na koncové body v profilu, který není nakonfigurovaný geografické směrování? 

Ano, pokud není geografické metodu směrování profilu, můžete použít [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) přiřadit geografických oblastí na koncové body v profilu. V případě-geografické směrování profily typů toto nastavení je ignorováno. Pokud později změníte takový profil pro geografické směrování typu, Traffic Manager může použít tyto mapování.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Proč se při změně metodu směrování existující profil pro Geographic dochází k chybě?

Všechny koncové body v rámci profilu s geografické směrování musí mít aspoň jednu oblast k němu mapována. Převést existující profil pro geografické směrování typu, je nutné nejprve přidružit geografických oblastí na všechny její pomocí koncových bodů [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) před změnou typu směrování do geografických. Pokud používáte portál, nejprve odstranit koncových bodů, změňte metodu směrování profilu na geografické a pak přidat koncové body spolu s jejich zeměpisnou oblast mapování. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Proč je důrazně doporučujeme, aby zákazníci vytvářet vnořené profily místo koncové body v rámci profilu se zapnutým směrováním geografické? 

V oblasti lze přiřadit pouze jeden koncový bod v rámci profilu, pokud jeho použití geografické směrování typu. Pokud tohoto koncového bodu není vnořený typ profilem podřízené připojené, pokud provoz tohoto koncového bodu, bude v pořádku, správce i nadále posílat provoz k němu od alternativní není odesílání není žádná lepší veškerý provoz. Traffic Manager nemá převzetí služeb při selhání do jiného koncového bodu, i když oblasti přiřazené je "nadřazený" oblasti přiřazené ke koncovému bodu, který není v pořádku (například, pokud koncový bod, který má oblast Španělsko přestane není v pořádku, že to není převzetí služeb při selhání do jiného koncového bodu se nepovedlo obsahuje oblast, kterou přiřazenou Evropa). To slouží k zajištění, že Traffic Manager respektuje geografické hranice, že zákazník má instalační program ve svém profilu. K budete mít k dispozici přebírání služeb při selhání do jiného koncového bodu, když se koncový bod dostane není v pořádku, se doporučuje pro vnořené profily s několika koncovými body v rámci něj namísto jednotlivé koncové body přidělí geografických oblastí. Tímto způsobem Pokud koncový bod v profilu vnořené podřízené selže, můžete provoz převzetí služeb při selhání do jiného koncového bodu uvnitř vnořené podřízené stejný profil.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existují nějaká omezení na verzi rozhraní API, která podporuje tento typ směrování?

Ano, geografické směrování zadejte pouze verze rozhraní API 2017-03-01 a novější podporuje. Všechny starší verze rozhraní API nelze použít k vytvořené profily geografické směrování typu nebo přiřadit geografických oblastí na koncové body. Pokud starší verze rozhraní API slouží k načtení profily z předplatného Azure, nevrátí se žádný profil geografické směrování typu. Kromě toho, jestli používáte starší verze rozhraní API vrácen žádný profil, který má koncové body s přiřazením geografické oblasti, nemá jeho přiřazení geografické oblasti.

## <a name="real-user-measurements"></a>Měření reálných uživatelů

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jaké jsou výhody používání měření Real User Measurements?
Při použití metody směrování podle výkonu Traffic Manageru vybere nejlepší oblast Azure pro vaše koncového uživatele pro připojení k zkontrolováním Zdrojová IP adresa a podsíť EDNS klienta (Pokud je předaná) a kontrola s inteligentními funkcemi latence sítě služby udržuje. Měření Real User Measurements to vylepšuje pro základní koncové uživatele tak, že jejich zkušenostem přispívat do této tabulky latence ale také ověřit, který tuto tabulku adekvátně pokrývaly sítě koncový uživatel z kde koncoví uživatelé připojení k Azure. To vede k vyšší přesnost v směrování koncových uživatelů.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Můžete použít měření Real User Measurements oblasti mimo Azure?
Měření Real User Measurements měří a oznamuje omezení jenom latence pro dosažení oblasti Azure. Pokud používáte směrování podle výkonu s koncovými body, které jsou hostované v oblasti mimo Azure, můžete stále využívat výhod této funkce pomocí s vyšší latencí informace o reprezentativní oblast Azure, která jste vybrali k tomuto koncovému bodu.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Které metody směrování těží z reálných uživatelů?
Další informace o získaných měření Real User Measurements platí jenom pro profily, které používají metody směrování výkonu. Všimněte si, že měření Real User Measurements odkaz je k dispozici ve všech profilech při zobrazení na webu Azure portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Je potřeba povolit měření Real User Measurements všechny profily samostatně?
Ne, je potřeba jenom jednou ji povolit na jedno předplatné a všechny informace o latenci měří a hlásí jsou k dispozici pro všechny profily.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak vypnout měření Real User Measurements pro Moje předplatné?
Můžete zastavit nabíhání poplatků za související s měření Real User Measurements, když zastavíte shromažďování a odesílání měření latence zpět z klientské aplikace. Například při měření JavaScript vložený do webové stránky, můžete přestat používat tuto funkci tak, že odeberete jazyka JavaScript nebo vypnutím jeho vyvolání při vykreslování stránky.

Měření Real User Measurements můžete také vypnout tak, že odstraníte váš klíč. Když klíč odstraníte, se zahodí všechny měření odesílané do Traffic Manageru s tímto klíčem.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Můžete použít měření Real User Measurements pomocí klientských aplikací, než webové stránky
Ano, měření Real User Measurements je navržena na příjem údaje shromážděné prostřednictvím různých typů klientů koncového uživatele. Tyto nejčastější dotazy se aktualizovat, protože získání podporuje nové typy klientských aplikací.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Kolik se měří pokaždé, když můj měření Real User Measurements povolené webové stránky se vykreslí?
Při měření Real User Measurements se používá s měřením jazyka JavaScript poskytuje, výsledkem každého vykreslování části stránky šest měření. Ty se pak hlášeny zpět ke službě Traffic Manager. Všimněte si, že se vám účtovat tuto funkci na základě počtu měření nahlášené službě Traffic Manager. Například pokud uživatel přejde od webovou stránku, zatímco se se měří, ale předtím, než byla nahlášena, se nepovažují těchto měření pro účely fakturace.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Je k dispozici ke zpoždění před spuštěním skriptu měření Real User Measurements v Moje webová stránka?
Ne, není žádný naprogramovaných zpoždění před vyvoláním skriptu.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Můžu použít měření Real User Measurements nakonfigurovat pouze oblastí Azure chcete měřit?
Ne, každý čas, který je vyvolán, skript měření Real User Measurements měří sada šesti oblastech Azure podle služby. Tato možnost nastavená změny mezi různými voláními a při velké množství taková volání, pokrytí měření zabírá různých oblastech Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Můžete omezit počet měření na konkrétní číslo?
Měření, které jazyk JavaScript je vložený v rámci vaší webové stránky a mít plnou kontrolu nad pro spouštění a zastavování jeho použití. Za předpokladu, službu Traffic Manager obdrží žádost o seznam oblastí Azure k měření, jsou vráceny sadě oblastí.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Můžete zobrazit měření Moje klientská aplikace jako součást měření Real User Measurements?
Protože logiky měření je spuštěn z klientské aplikace, se v úplné řízení co se stane, včetně zobrazení měření latence. Traffic Manager nevytváří sestavu agregované zobrazení měření přijatých pod klíčem k předplatnému připojený.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Můžete upravit skript měření uvedený pomocí Traffic Manageru?
Když jste si kontrolu nad co je vložen do webové stránky, budeme důrazně Zabraňte můžete z jakýchkoli změn do skriptu měření k zajištění, že se měří a oznamuje latence správně.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>To bude možné, aby ho ostatní viděli na klíč, který mohu použít s měření Real User Measurements?
Když vkládáte měření skript pro webové stránky je možné aby ho ostatní viděli skript a klíč měření reálných skutečných uživatelů (REÁLNÝCH). Ale je důležité vědět, že tento klíč se liší od id vašeho předplatného a je generována pomocí Traffic Manageru se použije pouze pro tento účel. Znalost spuštění klíč nebude ohrozit bezpečnost vašeho účtu Azure.

### <a name="can-others-abuse-my-rum-key"></a>Ostatní zneužívání Můj klíč spuštění?
I když je možné pro ostatní uživatele k odesílání chybných informací do Azure pomocí svého klíče mějte prosím na paměti, že několik nesprávné měření nezmění, směrování, protože je vzít do úvahy spolu s všechny měření, že státem. Pokud budete muset změnit svoje klíče, můžete znovu vygenerovat klíče v tomto okamžiku bude zahozen starý klíč.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Je nutné umístit měření jazyka JavaScript v mých webových stránek?
Měření Real User Measurements zajišťuje vyšší hodnotu jako číslo zvýšení měření. Musíme ale, je vaše rozhodování o tom, jestli je potřeba ji umístit do všech webových stránek nebo select málo. Naše doporučení je spustit tak, že vložíte ho většina navštívenou stránku kde se uživatel očekává setrvá na této stránce pět sekund nebo více.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Informace o koncovým uživatelům možné identifikovat Traffic Managerem když používám měření Real User Measurements?
Při použití zadané měření JavaScript Traffic Manageru bude mít přehled o IP adresu klienta koncového uživatele a zdrojové IP adresy místního překladače DNS, které používají. Traffic Manager používá IP adresu klienta jenom za to zkrácen na nebylo možné identifikovat konkrétní koncové uživatele, který odeslal měření. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Provádí webovou stránku měření reálných uživatelů potřeba používat ke směrování Traffic Manageru?
Ne, není nutné používat Traffic Manager. Na straně směrování Traffic Manageru funguje samostatně z část reálného měření uživatele a je sice skvělý nápad, jak je oba ve stejné vlastnosti webové, nemusí být.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Potřebuji k hostování jakoukoli službu v oblasti Azure pro použití s měření Real User Measurements?
Ne, není nutné pro hostování všechny komponenty na straně serveru v Azure pro měření Real User Measurements pro práci. Jeden pixel image nestáhne měřením jazyka JavaScript a služby spuštěné v různých oblastech Azure je hostované a spravované v Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Zvýší využití Azure šířku pásma, když používám měření Real User Measurements?
Jak je uvedeno v předchozí odpověď, serverové součásti měření Real User measurements vlastní a spravuje Azure. To znamená, že vaše využití Azure šířky pásma nezvýší vzhledem k tomu, že používáte měření Real User Measurements. Všimněte si, že to nezahrnují veškeré využití šířky pásma mimo s jakými poplatky Azure. Jsme Minimalizovat šířku pásma používanou stažením pouze jeden pixel bitové kopie k měření latence do oblasti Azure. 

## <a name="traffic-view"></a>Zobrazení přenosů

### <a name="what-does-traffic-view-do"></a>Co dělá Traffic View?
Zobrazení provozu je funkce Traffic Manageru, který vám pomůže pochopit více o své uživatele a jak se jejich prostředí. Používá dotazů přijatých službou Traffic Manager a tabulky intelligence latence sítě, které spravuje služba, kde přinášejí následující:
- Oblasti, kde jsou vaši uživatelé připojit do koncových bodů v Azure.
- Svazek uživatelé, kteří se připojují z těchto oblastí.
- Oblasti Azure, ke kterým jsou získávání předán.
- Jejich zkušenostem latence do těchto oblastí Azure.

Tyto informace jsou k dispozici přes překrytí zeměpisné mapě a tabulkové zobrazení kromě toho, že k dispozici jako nezpracovaných dat si můžete stáhnout na portálu.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak může přinést použití Traffic View?

Zobrazení přenosů poskytne celkový přehled o provozu, který se zobrazí vaše profily Traffic Manageru. Konkrétně to je možné pochopit, kde se svou uživatelskou základnu připojuje z a stejně důležité je co je jejich zkušenostem průměrnou dobu vyřízení. Tyto informace pak můžete nalézt oblasti, ve kterých je potřeba zaměřit, třeba tak, že rozbalíte Azure nároky na oblasti, který může sloužit tito uživatelé s nižší latencí. Další přehledy, které lze odvodit z používání funkce zobrazení přenosů se odhalit vzory provozu do různých oblastí, které pak může pomoci při rozhodování o zvýšením nebo snížením vytvářet v těchto oblastech.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Čím se liší od metriky Traffic Manageru dostupné přes Azure monitor zobrazení přenosů

Azure Monitor umožňuje porozumět na souhrnné úrovni přenosů přijatých profilu a jeho koncových bodů. Také umožňuje sledovat stav koncových bodů zveřejněním výsledky kontroly stavu. Když budete potřebovat nad rámec těchto a pochopení prostředí koncového uživatele pro připojení k Azure na místní úrovni, lze toho dosáhnout Traffic View.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Používá Traffic View informace o podsíti EDNS klienta?

Dotazy DNS služby Azure Traffic Manager vezměte v úvahu informace ECS zvýšit přesnost směrování. Ale při vytváření sady dat, který ukazuje, kde se uživatelé připojují ze zobrazení přenosů používá pouze IP adresu Překladač DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Kolik dnů od data zobrazení přenosů použít?

Zobrazení přenosů zpracování dat ze sedmi dnů předchozí den před při jeho zobrazení vámi vytvoří jeho výstup. To se posouvá a nejnovější data se použijí pokaždé, když navštívíte.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak zobrazení přenosů zpracovává externí koncové body?

Pokud používáte externí koncové body, které jsou hostované mimo oblasti Azure v profilu služby Traffic Manager můžete její namapované na určitá oblast Azure, což je proxy server pro charakteristik latence (to je ve skutečnosti je třeba Pokud používáte metody směrování podle výkonu). Pokud má toto mapování oblasti Azure, Azure národními metriky latence se použije při vytváření výstupu Traffic View. Pokud není zadána žádná oblast Azure, bude informace o latenci prázdný v datech pro tyto externí koncové body.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Je potřeba povolit zobrazení provozu pro každý profil ve svém předplatném?

Během období preview bylo povoleno zobrazení provozu na úrovni předplatného. Jako součást vylepšení, které jsme provedli před všeobecnou dostupnost můžete teď povolit zobrazení provozu na úrovni profilu, díky kterým podrobnější povolení této funkce. Ve výchozím nastavení se zakážou Traffic View profilu.

>[!NOTE]
>Pokud jste povolili zobrazení provozu na úrovni předplatného v době ve verzi preview, musíte teď ji znovu povolit pro každý profil v rámci příslušného předplatného.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak můžu vypnout Traffic View? 
Můžete vypnout zobrazení přenosů pro žádný profil pomocí portálu nebo rozhraní REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Jak funguje fakturace Traffic View?

Zobrazit ceny provozu je podle počtu datových bodů použitých k vytvoření výstupu. V současné době podporované pouze datový typ je dotazů, které obdrží váš profil. Kromě toho nebudete dostávat faktury, výpočetní výkon, který bylo provedeno v případě, že máte Traffic View povolena. To znamená, že-li povolit zobrazení provozu pro některé časové období v daném měsíci a vypněte jej během jindy pouze datové body zpracována, když jste měli funkci Počet povolených na faktuře.

## <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Můžete použít Traffic Manageru s koncovými body z několika předplatných?

Pomocí koncových bodů z několika předplatnými není možné s Azure Web Apps. Azure Web Apps vyžaduje, že všechny vlastní doménu používat s Web Apps se používá pouze v rámci jednoho předplatného. Není možné používat službu Web Apps z několika předplatných se stejným názvem domény.

Pro jiné typy koncových bodů je možné použít Traffic Manageru s koncovými body z více než jedno předplatné. V Resource Manageru koncových bodů z jakéhokoli předplatného je možné přidat do Traffic Manageru, jako osoba konfigurace profilu služby Traffic Manager má přístup pro čtení ke koncovému bodu. Tato oprávnění lze udělit prostřednictvím [Azure Resource Manageru řízení přístupu na základě role (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Můžete použít Traffic Manageru s sloty "Pracovní" cloudové služby?

Ano. Cloudová služba, přípravné sloty lze nakonfigurovat v Traffic Manageru jako externí koncové body. Kontroly stavu jsou stále účtovat sazba koncových bodů Azure. Protože typ je externí koncový bod se používá, změny v základní službě se nenačítají automaticky. S externí koncové body Traffic Manageru nelze rozpoznat, kdy se zastavení nebo odstranění cloudové služby. Traffic Manager proto pokračuje fakturace pro kontroly stavu, dokud koncový bod je deaktivováno nebo odstraněno.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager podporuje koncové body IPv6?

Traffic Manager aktuálně neposkytuje IPv6 addressible názvové servery. Ale Traffic Manageru můžete stále využívat IPv6 klientů připojujících se ke koncovým bodům protokol IPv6. Klient Nedovolte, aby byly žádosti DNS přímo na Traffic Manager. Klient místo toho použije rekurzivní službu DNS. Pouze protokol IPv6 klient zasílá požadavky na rekurzivní službu DNS prostřednictvím protokolu IPv6. Rekurzivní službu pak by měl být schopen kontaktovat názvové servery Traffic Manageru pomocí protokolu IPv4.

Traffic Manager jsou reaguje s názvem DNS koncového bodu. Pro podporu koncový bod IPv6, musí existovat záznam AAAA služby DNS, přejděte na adresu IPv6 DNS název koncového bodu. Kontroly stavu Traffic Manageru se podporují jenom adresy IPv4. Služba je potřeba zveřejnit koncový bod protokolu IPv4 na stejný název DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Můžete použít Traffic Manageru s více než jednu webovou aplikaci ve stejné oblasti?

Traffic Manager se obvykle používá pro směrování provozu do aplikace nasazené v různých oblastech. Nicméně jej lze také pokud má aplikace více než jedno nasazení ve stejné oblasti. Koncové body Azure Traffic Manageru není povoleno více než jeden koncový bod webové aplikace ze stejné oblasti Azure mají být přidány do stejného profilu Traffic Manageru.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Jak přesunu profil Traffic Manageru koncových bodů Azure k jiné skupině prostředků.

Koncové body Azure, které jsou spojeny s profilem Traffic Manager jsou sledovány v jejich ID prostředků. Když prostředek Azure, který je používán jako koncový bod (například veřejná IP adresa, klasické cloudové služby, webové aplikace nebo jiný profil služby Traffic Manager používá vnořené způsobem) se přesune do jiné skupiny prostředků, jeho změny ID prostředku. V tomto scénáři v současné době je nutné aktualizovat profil Traffic Manageru předchozího odstranění a následným přidáním zpět koncových bodů do profilu. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Je odolné vůči selhání oblasti Azure Traffic Manager?

Traffic Manager je klíčovou komponentou doručování aplikací s vysokou dostupností v Azure.
Traffic Manager k zajištění vysoké dostupnosti, musíte mít mimořádně vysoký stupeň dostupnosti a být odolné vůči selhání v oblasti.

Traffic Manager součásti jsou standardně odolné vůči selhání dokončení v libovolné oblasti Azure. Tato odolnosti se vztahuje na všechny komponenty Traffic Manager: název DNS servery, rozhraní API, vrstvy úložiště a monitorování služeb koncových bodů.

V nepravděpodobném případě výpadku celé oblasti Azure Traffic Manager má dál normálně fungovat. Aplikace nasazené v několika oblastech Azure můžete spolehnout na Traffic Manager ke směrování provozu na dostupnou instanci aplikace.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jaký vliv má Volba umístění skupiny prostředků Traffic Manageru?

Traffic Manager je jedna globální služba. Není místní. Volba umístění skupiny prostředků díky žádný rozdíl a profily Traffic Manageru, které jsou nasazené v této skupině prostředků.

Azure Resource Manageru vyžaduje všechny skupiny prostředků a zadejte umístění, která určuje výchozí umístění pro prostředky nasazené v této skupině prostředků. Když vytvoříte profil Traffic Manageru, se vytvoří ve skupině prostředků. Všechny profily Traffic Manageru využívají **globální** jako svoje umístění, přepíšete výchozí skupiny prostředků.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak zjistím, jaký je aktuální stav každého koncového bodu?

Aktuální stav monitorování každého koncového bodu, kromě celkové profilu se zobrazí na webu Azure Portal. Tyto informace jsou také k dispozici prostřednictvím sledování provozu [rozhraní REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager), a [Azure CLI pro různé platformy](../cli-install-nodejs.md).

Azure Monitor můžete také sledovat stav vašich koncových bodů a zobrazte vizualizaci z nich. Další informace o použití Azure monitoru, najdete v článku [dokumentace ke službě Azure Monitoring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Můžete monitorovat koncové body HTTPS?

Ano. Traffic Manager podporuje zjišťování přes protokol HTTPS. Konfigurace **HTTPS** jako protokol v konfiguraci monitorování.

Traffic manager neposkytuje žádné ověření certifikátu, včetně:

* Pořadí úloh se neověřuje certifikáty na straně serveru
* SNI certifikáty na straně serveru nejsou podporovány.
* Klientské certifikáty nejsou podporovány.

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Zastavení cloudové služby Azure / web koncového bodu aplikace v mém profilu Traffic Manageru, ale i poté, co jsem restartování veškerý provoz doručována. Jak to mohu napravit?

Při Azure cloud service / web koncového bodu aplikace je zastavený Traffic Manageru zastaví kontrola jeho stavu a restartuje kontroly stavu až poté, co zjistí, že má restartovat koncový bod. Abyste zabránili tomuto zpoždění dochází, zakázat a pak znovu povolit tohoto koncového bodu v profilu služby Traffic Manager po restartování koncový bod.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Můžete použít i v případě, že Moje aplikace nemá podporu pro protokol HTTP nebo HTTPS Traffic Manageru?

Ano. TCP můžete zadat jako protokol monitorování a Traffic Manageru můžete iniciovat připojení TCP a čekat na odpověď od koncového bodu. Koncový bod odpoví na požadavek na připojení odpovědí k navázání připojení v časovém limitu tohoto koncového bodu je označen jako v pořádku.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jaké konkrétní odpovědi jsou vyžadována z koncového bodu při použití protokolu TCP monitorování?

Při použití protokolu TCP monitorování Traffic Manageru začíná třícestné TCP odesláním SYN požadavek na koncový bod na zadaný port. Pak čeká určitou dobu (jak je uvedeno v nastavení časového limitu) pro odpovědi z koncového bodu. Pokud koncový bod odpoví na požadavek SYN SYN ACK odpovědí v rámci časový limit zadaný v nastavení monitorování, pak tento koncový bod se považuje za v pořádku. Pokud odpověď SYN ACK, Traffic Manager resetuje připojení reakcí vraťte se RVNÍ.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak rychle přesunout Traffic Manageru Moje uživatelů mimo koncový bod není v pořádku?

Traffic Manager poskytuje několik nastavení, které vám mohou pomoci při řízení chování převzetí služeb při selhání z vašeho profilu Traffic Manageru následujícím způsobem:
- můžete určit, že Traffic Manager sondy koncových bodů častěji tak, že nastavíte Interval zjišťování na 10 sekund. Tím se zajistí, že nejdříve lze zjistit libovolný koncový bod přechod není v pořádku. 
- můžete zadat jak dlouho čekat, než stavu požadavků doby rezervovat (hodnota minimální časový limit je 5 s).
- můžete určit, kolik selhání může dojít předtím, než koncový bod je označen jako není v pořádku. Tato hodnota může být v rozsahu od 0, v kterémžto případě koncového bodu je označen není v pořádku, jakmile selže první kontrola stavu. Však pomocí minimální hodnotu 0 pro Tolerovaný počet selhání může vést ke koncovým bodům se vyřazen ze smyčky kvůli všechny přechodné problémy, které mohou nastat v době zjišťování.
- můžete zadat time to live (TTL) pro odpověď DNS být v rozsahu od 0. Provádí se tak znamená, že překladače DNS nejde ukládat do mezipaměti odpovědi a každý nový dotaz načte odpověď, která zahrnuje nejnovější informace stavu, který má Traffic Manageru.

Pomocí těchto nastavení Traffic Manageru umožňuje převzetí služeb při selhání v části 10 sekund poté, co koncový bod přejde není v pořádku a proti odpovídající profil se provede dotaz DNS.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak lze určit různé nastavení monitorování pro různé koncové body v profilu?

Nastavení monitorování se v Traffic Manageru na úroveň profilu. Pokud budete muset použít jiné nastavení monitorování pro pouze jeden koncový bod, lze provést tak, že tento koncový bod jako [vnořené profilu](traffic-manager-nested-profiles.md) nastavení, jejíž monitorování se liší od nadřazené profilu.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jaké stav koncového bodu hlavičky hostitele kontroluje použití?

Traffic Manager používá hlavičky hostitele v kontroly stavu protokolu HTTP a HTTPS. Hlavička hostitele používaná Traffic Managerem je název cílového koncového bodu nakonfigurovali v profilu. Hodnota hlavičky hostitele nemůže zadávají samostatně z cílové vlastnosti.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Co jsou IP adresy, ze kterých doplněk pro kontroly stavu pocházejí?

Klikněte na tlačítko [tady](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) k zobrazení souboru JSON, který zobrazí seznam IP adres, ze které Traffic Manager může pocházet kontroly stavu. Zkontrolujte IP adresy uvedené v souboru JSON k zajištění, že příchozí připojení z těchto IP adres jsou povolena na koncové body zkontrolovat její stav.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Kolik kontroly stavu k koncovému bodu můžu očekávat od Traffic Manageru?

Počet stavů Traffic Manageru bude kontrolována dosažení váš koncový bod závisí na následujících:
- hodnota, která jste nastavili monitorování interval (interval menší znamená další požadavky na úvodní na váš koncový bod v daném časovém období).
- počet umístění, ze kterých pocházejí kontroly stavu (IP adresy z kde můžou očekávat, těmito kontrolami je uveden v předchozím – nejčastější dotazy).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak lze můžu získat vás upozorní, když jeden z mé koncových bodů ocitne mimo provoz? 
Jednu z metrik uvedených Traffic Managerem je stav koncových bodů v profilu. Můžete to zobrazit jako agregací všechny koncové body v profilu (například 75 % z vašich koncových bodů jsou v pořádku), nebo v na úrovni koncového bodu. Metriky Traffic Manageru se zobrazují v nástroji Azure Monitor a můžete použít jeho [upozorňování](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) dostávat oznámení, když dojde ke změně v stav koncového bodu. Další podrobnosti najdete v tématu [Traffic Manageru metrik a výstrah](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Vnořené profily Traffic Manageru

### <a name="how-do-i-configure-nested-profiles"></a>Jak nakonfigurovat vnořené profily

Vnořené profily Traffic Manageru můžete konfigurovat pomocí Azure Resource Manageru a rozhraní classic Azure REST API, rutin prostředí Azure PowerShell a příkazy Azure CLI pro různé platformy. Podporovány jsou i na novém webu Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Počet úrovní vnoření nemá Traffic Manager podporuje?

Je možné vnořovat profilů až 10 úrovní do hloubky. "Smyčky" nejsou povoleny.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Můžete kombinovat jiné typy koncových bodů s profily vnořené podřízené, v jednom profilu Traffic Manageru?

Ano. Nejsou žádná omezení jak kombinovat různé typy v rámci profilu koncové body.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak model fakturace použít pro vnořené profily?

Neexistuje žádné negativní vliv na použití vnořených profilů cenu.

Fakturace Traffic Manageru má dvě součásti: kontrolám stavu koncových bodů a milionů dotazů DNS

* Kontroly stavu koncových bodů: neplatí žádné poplatky pro podřízené profil při nakonfigurovaný jako koncový bod v nadřazené profilu. Monitorování koncových bodů v podřízených profilu se účtuje obvyklým způsobem.
* Dotazy DNS: každý dotaz se počítá pouze jednou. Dotaz vůči nadřazené profilu, která vrací koncový bod z podřízených profilu se započítává jenom profil nadřazené.

Úplné podrobnosti najdete v tématu [Traffic Manageru stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Je nějaký dopad na výkon pro vnořené profily?

Ne. Neexistuje žádný dopad na výkon při použití vnořených profilů.

Názvové servery Traffic Manageru procházet hierarchii profilu interně při zpracování jednotlivých dotazů DNS. Dotaz DNS na nadřazené profil může přijímat odpovědi DNS pomocí koncového bodu z profilu podřízené. Jeden záznam CNAME se používá, jestli používáte jeden profil nebo vnořených profilů. Není nutné vytvořit záznam CNAME pro každý profil v hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager compute stavu vnořený koncový bod v nadřazené profilu?

Profil nadřazené neprovádí kontroly stavu pro podřízený přímo. Stav profilu podřízených koncových bodů se místo toho používají k výpočtu celkového stavu profilu podřízené. Tyto informace se šíří hierarchii vnořeného profilu určit stav vnořený koncový bod. Nadřazené profil používá toto agregovaný stav k určení, zda lze přenášená do podřízeného.

Následující tabulka popisuje chování Traffic Manageru kontroly stavu pro vnořený koncový bod.

| Stav podřízené nástroj Profile Monitor | Stav nadřazeného monitorování koncového bodu | Poznámky |
| --- | --- | --- |
| Zakázané. Podřízené profilu bylo zakázáno. |Zastaveno |Stav koncového bodu nadřazené je zastaven, není zakázáno. Stav Zakázáno je vyhrazený pro označující, že jste zakázali koncový bod v nadřazené profilu. |
| Snížený výkon. Nejméně jeden podřízený prvek koncovému bodu profilu je ve stavu snížený. |Online: počet Online koncových bodů v podřízených profilu je nejméně hodnotu MinChildEndpoints.<BR>CheckingEndpoint: počet Online plus CheckingEndpoint koncových bodů v podřízených profilu je minimálně hodnota MinChildEndpoints.<BR>Snížený výkon: jinak. |Provoz se směruje do koncového bodu stavu CheckingEndpoint. Pokud se nastaví příliš vysoko MinChildEndpoints, koncový bod má vždy snížený výkon. |
| Online. Koncový bod profilu aspoň jedním podřízeným prvkem je stavu Online. Žádný koncový bod je ve stavu snížený. |Viz výše. | |
| CheckingEndpoints. Koncový bod profilu aspoň jedním podřízeným prvkem je "CheckingEndpoint". Žádné koncové body jsou "Online" nebo "snížený výkon. |Stejné jako výše. | |
| Neaktivní. Všechny podřízené profilu koncové body jsou zakázané nebo zastaveno, nebo tento profil nemá žádné koncové body. |Zastaveno | |

## <a name="next-steps"></a>Další kroky:
- Další informace o Traffic Manageru [koncový bod monitorování a automatickému převzetí služeb při selhání](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o Traffic Manageru [metody směrování provozu](../traffic-manager/traffic-manager-routing-methods.md).
