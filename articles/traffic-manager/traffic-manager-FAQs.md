---
title: Azure Traffic Manager – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy o Traffic Manager
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
ms.openlocfilehash: 29c7994485eeb2b3fdde52d1794704ecb51d65e5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301061"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager nejčastější dotazy (FAQ)

## <a name="traffic-manager-basics"></a>Základy Traffic Manageru

### <a name="what-ip-address-does-traffic-manager-use"></a>Jaké IP adresu Traffic Manager používat?

Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager funguje na úrovni DNS. Odešle odpovědí DNS k nasměrování klientů na koncový bod příslušné služby. Klienti se potom připojují ke koncovému bodu služby přímo, nejsou prostřednictvím Správce provozu.

Proto Traffic Manager neposkytuje na koncový bod nebo IP adresu pro klienty pro připojení k. Pokud chcete statickou IP adresu pro vaši službu, která musí být nakonfigurovaná na službu, není v Traffic Manageru.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Typy přenosů, které je možné směrovat pomocí Traffic Manageru?
Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), koncový bod Traffic Manager může být internetové služby hostované uvnitř nebo mimo Azure. Proto Traffic Manager může směrovat přenosy pocházející z veřejného Internetu do sady koncových bodů, aby se také internet setkávají. Pokud máte koncové body, které jsou v privátní síti (například na interní verzi [nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md#internalloadbalancer)) nebo mít uživatele, které DNS požadavků Traffic Manageru nelze použít pro tyto přenosy tyto interní sítě.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Podporuje Traffic Manager "rychlé" relace?

Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager funguje na úrovni DNS. Odpovědí DNS používá k nasměrování klientů na koncový bod příslušné služby. Klienti připojovat k koncový bod služby přímo, nejsou prostřednictvím Správce provozu. Traffic Manager proto nejsou vidět provoz protokolu HTTP mezi klientem a serverem.

Kromě toho IP adresu zdrojového dotazu DNS přijatých Traffic Managerem patří do rekurzivní služba DNS, nikoli u klienta. Traffic Manager proto nemá žádný způsob, jak sledovat jednotlivých klientů a nelze implementovat, trvalé' relací. Toto omezení je společná pro všechny systémy správy provozu na základě DNS a není specifická pro Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Proč se při použití Traffic Manager zobrazuje chybu HTTP?

Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager funguje na úrovni DNS. Odpovědí DNS používá k nasměrování klientů na koncový bod příslušné služby. Klienti se potom připojují ke koncovému bodu služby přímo, nejsou prostřednictvím Správce provozu. Správce provozu nepodporuje najdete přenos HTTP není mezi klientem a serverem. Proto musí být chyby protokolu HTTP, které vidíte pocházejících z vaší aplikace. Pro klienta se můžete připojit k aplikaci jsou všechny kroky rozlišení DNS dokončeny. Interakce s Traffic Manager na toku přenosu aplikace, který zahrnuje.

Další šetření by proto soustředit na aplikaci.

Hlavička hostitele HTTP odeslané z prohlížeče klienta je zdroji většiny běžných problémů. Ujistěte se, že aplikace je nakonfigurovaná tak, aby přijímal hlavičku správné hostitele pro název domény, který používáte. Koncové body pomocí služby Azure App Service, najdete v části [konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Co je dopad na výkon pomocí Traffic Manageru?

Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager funguje na úrovni DNS. Vzhledem k tomu, že klienti připojují k vaší koncové body služby přímo, neexistuje žádný dopad na výkon vzniklé při použití Traffic Manager po navázání připojení.

Vzhledem k tomu, že Traffic Manager se integruje s aplikacemi na úrovni DNS, nevyžaduje další vyhledávání DNS má být vložen do řetězu rozlišení DNS. Dopad Traffic Manager na dobu překladu názvů DNS je minimální. Používá globální sítě názvové servery Traffic Manageru a [libovolného vysílání](https://en.wikipedia.org/wiki/Anycast) sítě zajistit DNS dotazy jsou vždy směrovány do nejbližší dostupné název serveru. Kromě toho ukládání do mezipaměti odpovědí DNS znamená další DNS latence při pomocí Traffic Manager, které se vztahuje pouze na zlomek relací.

Metoda výkonu směruje provoz na nejbližší dostupný koncový bod. Net výsledkem je, že by měla být minimální dopad na celkový výkon přidružený k této metodě. Každé zvýšení latence DNS by měla být pokryta nižší latenci sítě ke koncovému bodu.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jaké protokoly aplikace lze použít s nástrojem Traffic Manager?

Jak je popsáno v [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager funguje na úrovni DNS. Po dokončení vyhledávání DNS se klienti připojovat k koncový bod aplikace přímo, nejsou prostřednictvím Správce provozu. Proto připojení můžete použít libovolný protokol pro aplikace. Pokud vyberete TCP jako monitorování protokol Traffic Manager na koncový bod sledování stavu lze provést bez použití žádné protokoly aplikací. Pokud zvolíte možnost mít stav ověření pomocí aplikační protokol, musí být schopné reagovat na žádosti protokolu HTTP nebo HTTPS získat koncový bod.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Můžete použít Traffic Manager s názvem "holé" domény?

Ne. Standardech DNS nepovoluje záznamů CNAME, aby nevznikaly konflikty s další záznamy DNS se stejným názvem. Vrcholu (nebo kořenové) zóny DNS vždy obsahuje dva existující záznamy DNS; záznamy autoritativních NS a SOA. To znamená, že nelze bez narušení standardech DNS vytvořit záznam CNAME ve vrcholu zóny.

Správce provozu vyžaduje záznam DNS CNAME pro mapování názvu DNS jednoduché. Například namapujete www.contoso.com na název contoso.trafficmanager.net Traffic Manager profil DNS. Kromě toho vrátí profil služby Traffic Manager druhý DNS CNAME označíte, kterému koncovému bodu, klient se musí připojit k.

Chcete-li tento problém obejít, doporučujeme používat přesměrování protokolu HTTP pro přímé přenosy z názvu holé domény na jinou adresu URL, který lze potom použít Traffic Manager. Například holé doménu "contoso.com" může přesměrovat uživatele do CNAME 'www.contoso.com, který odkazuje na název DNS Traffic Manageru.

Plnou podporu pro holé domén v Traffic Manager je sledovány v našem funkce nevyřízených položek. Můžete zaregistrovat podporu pro tento požadavek funkce [volíte ho na náš web pro zasílání názorů komunity](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager zvažte adresu podsítě klienta při zpracování dotazů DNS 
Ano, kromě IP adresu zdrojového dotazu DNS obdrží (což obvykle je IP adresa Překladač DNS), při provádění hledání pro metody směrování Geographic a výkonu, traffic Manageru také zvažuje adresu podsítě klienta, pokud je zahrnutý v dotazu pomocí překladače vytvoření požadavku za koncového uživatele.  
Konkrétně [RFC 7871 – klientské podsíti v dotazech DNS](https://tools.ietf.org/html/rfc7871) , který poskytuje [mechanismus rozšíření pro službu DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) které můžete předat na adresu podsítě klienta z překladače, které ji podporují.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co je DNS TTL a jak ovlivní to svým uživatelům?

Pokud dotaz DNS pojmenováváme v Traffic Manageru, nastaví hodnotu v odpovědi nazývá time to live (TTL). Tato hodnota, jejíž jednotka je v sekundách, označuje do překladače služby DNS po proudu na jak dlouho pro ukládání do mezipaměti této odpovědi. Zatímco překladače služby DNS se nezaručuje, že pro ukládání do mezipaměti tento výsledek, mezipaměti umožňuje, aby odpovídal na všechny následné dotazy vypnout mezipaměti místo na servery DNS Traffic Manager. To ovlivní odpovědi následujícím způsobem:
- vyšší hodnota TTL snižuje počet dotazů, které nebude zobrazovat na serverech DNS Traffic Manager, které může snížit náklady pro zákazníka, vzhledem k tomu, že počet dotazů na zpracování se fakturovatelné využití.
- vyšší hodnota TTL potenciálně můžete zkrátit dobu potřebnou k provést vyhledávání DNS.
- vyšší hodnota TTL také znamená, že vaše data nemusí odpovídat nejnovější informace o stavu, který Traffic Manager má získaných pomocí jeho testování agenti.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak horní nebo dolní lze nastavit hodnotu TTL pro odpovědi Traffic Manageru?

Můžete nastavit, v na úrovni profilu, TTL DNS v rozsahu od 0 sekund a až 2 147 483 647 sekund (maximální rozsah, který je kompatibilní s [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). Hodnota TTL 0 znamená, že podřízené překladače služby DNS Neukládat do mezipaměti odpovědi na dotazy a všechny dotazy se očekává k dosažení servery pro překlad DNS Traffic Manageru.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak můžete porozumět svazku dotazů přicházející do mého profilu? 
Zadat jednu z metriky položek Traffic Managerem je počet dotazů profil odpověděl. Tyto informace můžete získat na úrovni agregace profil, nebo můžete rozdělit ho dále najdete objem dotazy, které byly vráceny specifické koncové body. Kromě toho můžete nastavit upozornění pro upozornění, pokud odpověď svazku dotazu protne podmínky, že jste nastavili. Další podrobnosti [Traffic Manager metriky a výstrahy](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metodu směrování provozu Traffic Manageru Geographic

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jaké jsou některé případy použití, kde geografické směrování je užitečné? 
Zeměpisná typ směrování lze použít v žádném scénáři, kde Azure zákazníků potřebuje k rozlišení uživatelů podle zeměpisné oblasti. Například používáte metodu směrování provozu geografické, můžete uživatelům z určitých oblastí v jiné uživatelské rozhraní než ty, které z jiných oblastí. Dalším příkladem je dodržování vyžaduje suverenity místní data, které vyžadují, aby uživatelé z určité oblasti zpracovat pouze pomocí koncových bodů v této oblasti.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak rozhodnout, pokud mám by měl použít metody směrování podle výkonu nebo geografické metody směrování? 
Klíčovým rozdílem mezi tyto dvě metody oblíbených směrování je, že v výkonu směrování hlavním cílem je odesílat provoz do koncového bodu, který může poskytnout nejnižší latenci volajícího, že v Geographic směrování hlavním cílem je k vynucení geografická – metoda plotu pro své volající, takže je možné úmyslně směrovat na konkrétní. Překrytí se stane vzhledem k tomu, že existuje korelace mezi zeměpisné míru a nižší latenci, i když to není vždy hodnotu true. Může být koncový bod jiném datovém typu geography, který může poskytovat lepší latence pro volající a v takovém případě směrování výkonu bude uživateli odeslat do tohoto koncového bodu, ale geografické směrování vždycky odešlou je ke koncovému bodu namapování pro jejich geografické oblasti. Chcete-li ji vymazat, podívejte se na následující příklad - s Geographic další směrování můžete provádět neobvyklé mapování jako odesílat veškerý přenos z Asie do koncových bodů v USA a veškerý provoz USA s koncovými body v Asii. V takovém případě geografické směrování záměrně provede přesně co jste nakonfigurovali na práci a optimalizace výkonu není potřeba rozlišovat. 
>[!NOTE]
>Mohou existovat scénáře, kde může být nutné i výkon a geografické směrování možnosti pro tyto scénáře vnořených profilů může být služba skvělou volbou. Například můžete nastavit profil nadřazené s geografické směrování, kde odesílat veškerý přenos z USA a Kanady vnořené profil, který má koncové body v USA a používat výkonu směrování odesílat tyto provoz do nejlepší koncový bod v dané sadě. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jaké jsou oblasti, které jsou podporovány nástrojem Traffic Manager pro zeměpisnou směrování? 
Hierarchie země nebo oblast, která se používá Traffic Managerem najdete [zde](traffic-manager-geographic-regions.md). Při této stránky je udržováno odpovídalo nejnovějším změnám, můžete také programově načíst stejné informace pomocí [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak traffic Manageru určit, kde je uživatel dotazování z? 
Správce provozu vypadá na zdrojové IP dotazu (je to s největší pravděpodobností místní překladač DNS provádění dotazování jménem uživatele) a používá k určení umístění na interní IP adresu do oblasti mapy. Tato mapa se aktualizuje průběžně pro případ změny v Internetu. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Ho záruku, že Traffic Manageru můžete určit správně přesné geografické umístění uživatele v každém případě?
Ne, Traffic Manager nemůže zaručit, že zeměpisnou oblast, kterou jsme odvození z IP adresu zdrojového dotazu DNS vždycky odpovídají umístění uživatele z následujících důvodů: 

- Nejprve jak je popsáno v části Nejčastější dotazy pro předchozí, zdrojovou IP adresu, kterou vidíte je, že překladače DNS provádění vyhledávání jménem uživatele. Zeměpisnou polohu Překladač DNS je dobré proxy pro zeměpisnou polohu uživatele, může také být různé v závislosti na nároky na překladač služby DNS a konkrétní službu Překladač DNS, kterou zákazník se rozhodli použít. Jako příklad může zákazník umístěný v Malajsii zadejte svoje zařízení používá nastavení překladač služby DNS pro zpracování dotazu řešení pro tohoto uživatele či zařízení může získat zachyceny jejichž server DNS v Singapur. V takovém případě Traffic Manager uvidí jenom k překladači IP adresu, která odpovídá Singapur umístění. Viz také starší – nejčastější dotazy týkající se podpory adresu podsítě klienta na této stránce.

- Druhý Traffic Manager používá interní mapy pro IP adresu, kterou překlad geografické oblasti. Tato mapa je ověřen a aktualizovat průběžně zvýšit jeho přesnost a účet pro vyvíjející povaha Internetu, je stále možné naše informace není přesný reprezentace zeměpisnou polohu všechny IP adresy.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Potřebuje byly fyzicky umístěné ve stejné oblasti jako ten, který je nakonfigurován s geografické směrování koncový bod? 
Ne, ukládá umístění koncového bodu bez omezení, na které můžete k němu mapována oblasti. Koncový bod v oblasti Azure USA – střed, například může mít všichni uživatelé z Indie směrované na ni.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Můžete přiřadit zeměpisné oblasti s koncovými body v profilu, který není nakonfigurovaný geografické směrování? 

Ano, pokud není geografické metodu směrování profilu, můžete použít [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) přiřadit zeměpisné oblasti s koncovými body v tomto profilu. V případě-geografické směrování profily typů toto nastavení je ignorováno. Pokud později změníte takový profil geografické typ směrování, Traffic Manager můžete použít tyto mapování.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Proč dochází k chybě při pokusu změnit metodu směrování existujícího profilu na Geographic?

Všechny koncové body v rámci profilu s geografické směrování musí být alespoň jedné oblasti, které jsou k němu mapována. Převést existující profil geografické typ směrování, je nutné nejprve přidružit zeměpisné oblasti všechny jeho pomocí koncových bodů [REST API služby Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) před změnou geografické typ směrování. Pokud používáte portál, nejprve odstranit koncových bodů, změňte metodu směrování profilu geografické a poté přidejte koncové body spolu s jejich mapování geografické oblasti. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Proč se důrazně doporučujeme, aby zákazníci vytvořit vnořených profilů místo koncové body v rámci profilu s povoleným směrováním geografické? 

V oblasti lze přiřadit pouze jeden koncový bod v rámci profilu, pokud jeho pomocí zeměpisné typ směrování. Pokud tohoto koncového bodu není vnořené typy k profilu podřízené připojit, pokud provoz tohoto koncového bodu přejdete není v pořádku, správce nadále odeslat provoz od alternativní není odesílání přenosy dat se všechny lepší. Traffic Manager nemá převzetí služeb při selhání jiným koncovým bodem, i když oblasti přiřazené je "nadřazená" oblasti přiřazené ke koncovému bodu, který s vámi (například pokud koncový bod, který má oblast Španělsko přejde není v pořádku, že provedeme není převzetí služeb při selhání jiným koncovým bodem, který není v pořádku má oblast, kterou Evropa přiřazen). Tomu je potřeba zajistit, že Traffic Manager respektuje geografické hranice, že zákazník má instalační program v svůj profil. Chcete-li získat výhody převzetí služeb při selhání na jiný koncový bod, kdy přestane koncový bod není v pořádku, doporučujeme zeměpisné oblasti být přiřazovány vnořených profilů s víc koncových bodů v něm místo jednotlivých koncových bodů. Tímto způsobem Pokud koncový bod v profilu vnořené podřízené selže, můžete provoz převzetí služeb při selhání jiným koncovým bodem uvnitř stejný profil vnořené podřízené.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existují nějaká omezení na verzi rozhraní API, který podporuje tento typ směrování?

Ano, geografické směrování zadejte pouze verze rozhraní API 2017-03-01 a novější podporuje. Všechny starší verze rozhraní API nelze použít k vytvoření profilů geografické typ směrování nebo zeměpisné oblasti přiřadit koncové body. Pokud starší verze rozhraní API slouží k načtení profilů z předplatného Azure, není vrácen žádný profil geografické typ směrování. Kromě toho, pokud používáte starší verze rozhraní API vrátil žádný profil, má koncové body s přiřazením zeměpisnou oblast, nemá jeho přiřazení geografické oblasti vidět.

## <a name="real-user-measurements"></a>Měření reálných uživatelů

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jaké jsou výhody používání reálného měření uživatele?
Při použití metody směrování podle výkonu Traffic Manager vybere nejlepší oblast Azure pro koncovým uživatelům se připojit k zkontrolováním zdrojové IP adresy a EDNS klientské podsíti (Pokud je předaná) a kontrola proti intelligence latence sítě služby udržuje. Skutečné uživatele měření to zvyšuje pro základní koncovým uživatelům tak, že jejich prostředí přispívat do této tabulky latence také ověřit, která v této tabulce adekvátní zahrnuje sítě koncového uživatele z kde koncoví uživatelé připojit k Azure. To vede k vyšší přesností v směrování koncovým uživatelům.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Můžete použít reálného měření uživatele s oblasti mimo Azure?
Skutečné míry uživatele měření a zprávy o jenom latence k dosažení oblastech Azure. Pokud používáte směrování podle výkonu s koncovými body, které jsou hostované v oblastech mimo Azure, můžete přesto využívat výhod tuto funkci pomocí s vyšší latence informace o reprezentativní oblast Azure, že jste vybrali chcete přidružit k tomuto koncovému bodu.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Které metody směrování výhody z reálného měření uživatele?
Další informace získané prostřednictvím reálného měření uživatele platí jenom pro profily, které používají metody směrování výkonu. Všimněte si, že odkaz reálného měření uživatele je k dispozici ve všech profilech při zobrazení prostřednictvím portálu Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Je třeba povolit reálného měření uživatele každý profil samostatně?
Ne, je potřeba jenom jednou povolit jedno předplatné a všechny informace o latenci měří a hlášené jsou dostupné pro všechny profily.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak vypnout reálného měření uživatele pro Moje předplatné?
Můžete zastavit nabíhání poplatků za reálného měření uživatele Když zastavíte shromažďování a odesílání back latence měření z klientské aplikace. Například při měření JavaScript vložené do webové stránky, můžete zastavit pomocí této funkce odebráním JavaScript nebo vypnutím jeho volání při vykreslení stránky.

Můžete také vypnout reálného měření uživatele odstraněním váš klíč. Po odstranění klíče se zahodí všechny měření odeslaných do Traffic Manageru s tímto klíčem.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Můžete použít reálného měření uživatele pomocí klientských aplikací než webové stránky?
Ano, reálného měření uživatele slouží k ingestování údaje shromážděné prostřednictvím jiného typu klientů koncového uživatele. Tyto nejčastější dotazy bude aktualizován, jak získat podporuje nové typy klientské aplikace.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Kolik měřením pokaždé, když můj reálného měření uživatele povolené webové stránky je vykreslen?
Při reálného měření uživatele se používá s měřením JavaScript zadaný, výsledkem vykreslování každé stránky šesti měření. Tyto jsou pak zpětně hlášeny služby Traffic Manager. Všimněte si, že budou se vám účtovat pro tuto funkci, na základě počtu měření oznámeny služby Traffic Manager. Například pokud uživatel přejde mimo vaši webovou stránku, dokud se se měří, ale předtím, než byla nahlášena, tyto měření nejsou považovány za pro účely fakturace.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Je k dispozici ke zpoždění před spuštěním skriptu reálného měření uživatele v Moje webová stránka?
Ne, je žádné naprogramovaných zpoždění před vyvoláním skript.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Je možné používat nakonfigurovat jenom oblastí Azure chcete měřit reálného měření uživatele?
Ne, každý čas vyvolání, skript reálného měření uživatele měří sadu šesti oblastí, které jsou určené pomocí služby Azure. Toto nastavení změny mezi různými voláními a když stát velké množství takových volání pokrytí měření fungují v rámci různých oblastech Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Můžete omezit počet měření provedených na konkrétní číslo?
Měření, které JavaScript je vkládán vaši webovou stránku a jsou v úplnou kontrolu nad k zahájení a ukončení jeho použití. Tak dlouho, dokud služby Traffic Manager obdrží žádost o seznam oblastí Azure měření, jsou vráceny sadu oblasti.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Můžete zobrazit měření pořízených Moje aplikace klienta jako součást reálného měření uživatele?
Vzhledem k tomu, že logice měření spuštění z klientské aplikace, se v úplné řízení co se stane, včetně, zobrazuje se zjištěnými hodnotami latence. Traffic Manager nevytváří sestavu agregované zobrazení měření přijatých pod klíčem přidružený k vašemu předplatnému.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Můžete upravit skript měření uvedený Traffic Managerem?
Když jste kontrolu nad co vložené na webové stránce, jsme důrazně bránit vám v provedení změn měření skript, aby mohly měří a sestavy latence správně.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Je možné pro ostatní uživatele na klíč, který se používá s reálného měření uživatele?
Vložený skript měření webové stránky, je možné pro ostatní uživatele skript a klíč měření skutečné uživatele (RUM). Ale je důležité vědět, že tento klíč se liší od svoje id předplatného a je generována Traffic Managerem má být použit pouze pro tento účel. Znalost RUMU klíč nebude ohrozit bezpečnost vašeho účtu Azure.

### <a name="can-others-abuse-my-rum-key"></a>Ostatní zneužití Moje RUMU klíč?
Když je možné, aby jej ostatní mohli používat klíč k odesílání chybných informací do Azure Upozorňujeme, že několik nesprávný měření nezmění směrování vzhledem k tomu, že ho je vzít v úvahu společně s všech měření získaných. Pokud potřebujete změnit klíče, můžete znovu vygenerovat klíč okamžiku stane starý klíč zahozeny.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Je nutné uvést měření JavaScript do mých webových stránek?
Skutečné měření uživatele přináší další hodnotu počtu zvýšení měření. S uvedená který, je rozhodnout, jestli je potřeba ji umístit do všech webových stránek nebo s výběrem několika. Doporučujeme začít tím, že ji umístíte v stránku nejnavštěvovanější, kde je očekávána uživatel zůstane na této stránce pět sekund nebo více.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Informace o Moje koncoví uživatelé možné identifikovat Traffic Managerem pokud používám reálného měření uživatele?
Když se používá zadaný měření JavaScript, bude mít Traffic Manager Přehled IP adresu klienta koncového uživatele a zdrojovou IP adresu místního Překladač DNS, které používají. Traffic Manager používá IP adresu klienta až po nutnosti jej zkrácena nebude moci zjistit konkrétní koncového uživatele, který poslal měření. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Nemá na webovou stránku měřicí reálného měření uživatele, musí používat pro směrování Traffic Manageru?
Ne, není třeba pomocí služby Traffic Manager. Na straně směrování Traffic Manager funguje samostatně z části reálného měření uživatele a je sice kvalitních nápad na jejich oba ve stejné webové vlastnosti, nemusí být.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Potřebuji k hostování všechny služby v oblastech Azure za účelem použití s reálného měření uživatele?
Ne, nemusíte hostovat žádné součásti serveru straně v Azure pro reálného měření uživatele pro práci. Jeden pixelů stahuje měření JavaScript a služby spuštěné v různých oblastech Azure je hostované a spravované službou Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Zvýší mého používání Azure šířky pásma při použití reálného měření uživatele?
Jak je uvedeno v předchozí odpověď, serverové komponenty reálného měření uživatele vlastněná a spravovaná službou Azure. To znamená, že použití šířky pásma Azure nebude zvýšit, protože používáte reálného měření uživatele. Všimněte si, že nezahrnuje žádné využití šířky pásma mimo co Azure poplatky. Jsme Minimalizovat šířku pásma používanou stahování pouze jeden pixelů bitovou kopii do měření latence do oblasti Azure. 

## <a name="traffic-view"></a>Zobrazení přenosů

### <a name="what-does-traffic-view-do"></a>Jakým způsobem zobrazení provozu?
Zobrazení provozu je funkce správce provoz, který vám pomůže pochopit více o uživatele a jak se jejich prostředí. Používá dotazů přijatých Traffic Manager a tabulky intelligence latenci sítě, které služba udržuje, kde přinášejí následující:
- Oblasti, kde jsou vaši uživatelé připojovala k vaší koncových bodů v Azure.
- Svazek uživatelé, kteří se připojují z těchto oblastí.
- Oblastí Azure, ke kterým jsou získávání směrovány do.
- Jejich latence prostředí k těchto oblastem Azure.

Tyto informace jsou k dispozici pro můžete využívat prostřednictvím překrytí zeměpisné mapě a tabulkové zobrazení na portálu nejen k dispozici jako nezpracovaná data ke stažení.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak můžete využívat pomocí zobrazení provozu?

Provoz zobrazení poskytuje celkový přehled o provoz, který zobrazí vaše profily Traffic Manageru. Konkrétně může sloužit pochopit, kde vaší uživatelské základny připojuje z a stejnou měrou je nejdůležitější co je jejich prostředí Průměrná latence. Tyto informace pak můžete nalézt oblasti, ve kterých je potřeba zaměřit, například rozšířením vašeho Azure nároky na oblasti, která může obsluhovat tito uživatelé se nižší latence. Jiné přehled, které můžete odvozujete od pomocí zobrazení provoz je zobrazíte vzory přenosů dat do jiných oblastí, které pak vám můžou pomoct rozhodnout o zvýšením nebo snížením zásob v těchto oblastech.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Jak se liší od metriku Traffic Manager k dispozici prostřednictvím Azure monitorování provozu zobrazení?

Monitorování Azure slouží k pochopení na agregační úrovni provoz přijímaný váš profil a jeho koncové body. Také umožňuje sledovat stav koncových bodů podle vystavení výsledky kontroly stavu. Pokud budete potřebovat nad rámec těchto a pochopit připojení k Azure na místní úrovni činnost koncového uživatele, provoz zobrazení lze použít k dosažení který.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Používá provoz zobrazit informace o podsíti EDNS klienta?

Dotazy DNS obsluhovat pomocí Azure Traffic Manager vezměte v úvahu informace ECS zvýšit přesnost směrování. Ale při vytváření datové sady, který ukazuje, kde se uživatelé připojují z, provoz zobrazení používá pouze IP adresu Překladač DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Kolik dnů dat zobrazení provozu použít?

Zobrazení provoz vytvoří její výstup zpracování dat z sedm dní před den před při jeho zobrazení vy. Jedná se o pohyblivý okno a pokaždé, když navštívíte budou použity nejnovější data.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak zobrazit provoz pracovat externí koncové body?

Pokud používáte externí koncové body hostované mimo Azure oblasti v profilu Traffic Manageru můžete jej namapované na oblast Azure, což je proxy server pro jeho vlastnosti latence (to je ve skutečnosti je třeba Pokud používáte metody směrování podle výkonu). Pokud má toto mapování oblast Azure, že Azure oblast latence metriky se použije při vytváření zobrazení provoz výstup. Pokud není zadaný žádný oblast Azure, bude informace latence prázdný v datech pro tyto externí koncové body.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Je potřeba povolit provoz zobrazení pro každý profil v Moje předplatné?

Během období preview provoz zobrazení bylo povoleno na úrovni předplatného. Jako součást vylepšení, které jsme provedli před obecné dostupnosti můžete teď povolit provoz zobrazení na úrovni profilu, díky tomu můžete získat podrobnější povolení této funkce. Ve výchozím nastavení bude zakázáno provoz zobrazení pro profil.

>[!NOTE]
>Pokud jste povolili zobrazení provoz na úrovni předplatného během doby preview, musíte nyní znovu ji povolit pro každý profil v rámci tohoto předplatného.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak je můžete zobrazit provoz vypnout? 
Provoz zobrazení můžete vypnout pro žádné profily pomocí portálu nebo REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Jak funguje fakturace zobrazení provozu?

Počet datových bodů použít k vytvoření výstupu vychází provoz Zobrazit cenu. V současné době podporováno pouze datový typ je dotazy, které obdrží váš profil. Kromě toho můžete se účtují pouze pro zpracování, které bylo provedeno, když máte provoz zobrazení povoleno. To znamená, že pokud povolíte provoz zobrazení pro některé časové období, za měsíc a vypnout během jinou dobu, jenom datové body zpracovat měl funkci povoleno počet směrem vašem vyúčtování.

## <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Můžete použít Správce provozu s koncovými body z více předplatných?

Pomocí koncových bodů z více předplatných není možné pomocí Azure Web Apps. Azure Web Apps vyžaduje, aby všechny vlastní název domény použít s webovými aplikacemi se používá pouze v rámci jednoho předplatného. Není možné používat webové aplikace z více předplatných se stejným názvem domény.

Pro jiné typy koncových bodů je možné pomocí služby Traffic Manager s koncovými body z více než jedno předplatné. Ve Správci prostředků koncových bodů z libovolné předplatné, mohou být přidány do Traffic Manageru, tak dlouho, dokud uživatel konfigurace profil služby Traffic Manager má přístup pro čtení ke koncovému bodu. Tato oprávnění lze udělit pomocí [Azure Resource Manager řízení přístupu na základě role (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Můžete použít Správce provozu se sloty 'Staging, Cloudová služba?

Ano. Cloudové služby, přípravné sloty lze nakonfigurovat v Traffic Manageru jako externí koncové body. Kontroly stavu budou účtovat stále rychlostí koncové body Azure. Vzhledem k tomu, že typ externí koncový bod se používá, změny základní služby nejsou zachyceny automaticky. S externí koncové body Traffic Manageru nelze rozpoznat, kdy se Cloudová služba je zastavena nebo odstranit. Traffic Manager proto pokračuje fakturace kontroly stavu, dokud koncový bod je deaktivované nebo odstraněné.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Podporuje správce provoz koncovými body IPv6?

Traffic Manager aktuálně neposkytuje IPv6 addressible názvové servery. Ale Traffic Manager stále možné IPv6 klienti připojení ke koncovým bodům protokol IPv6. Klient neprovede požadavky na DNS přímo do Traffic Manageru. Klient místo toho použije služba DNS rekurzivní. Klientem pouze protokol IPv6 zasílá požadavky na službu DNS rekurzivní prostřednictvím protokolu IPv6. Službu rekurzivní pak by měl být schopen kontaktovat Traffic Manager názvové servery pomocí protokolu IPv4.

Správce provozu odpoví DNS název koncového bodu. Pro podporu koncový bod IPv6, musí existovat záznam DNS AAAA DNS název koncového bodu přejdete na adresu IPv6. Kontroly stavu Traffic Manageru se podporují jenom adresy IPv4. Služba potřebuje ke zveřejnění na IPv4 koncový bod se stejným názvem DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Můžete použít Správce provozu s více než jednu webovou aplikaci ve stejné oblasti?

Správce provozu se zpravidla používá, chcete-li směrovat provoz na aplikace nasazené v různých oblastech. Však může taky sloužit kde aplikace má víc než jedno nasazení ve stejné oblasti. Koncové body Azure Traffic Manager nepovoluje více než jeden koncový bod webové aplikace ze stejné oblasti Azure, který se má přidat na stejný profil Traffic Manageru.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Přesunutí koncové body Azure profil Traffic Manageru k jiné skupině prostředků.

Koncové body Azure, které jsou spojeny s profilem správce provozu se sledují pomocí jejich ID prostředku. Když je prostředek služby Azure, který se používá jako koncový bod (například veřejná IP adresa, Classic Cloudová služba, WebApp nebo jiný profil služby Traffic Manager použít vnořené způsobem) přesunout do jiné skupině prostředků, jeho změny ID prostředku. V tomto scénáři v současné době je třeba aktualizovat profil služby Traffic Manager nejprve odstranit a potom přidat zpět koncové body k profilu. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncového bodu Traffic Manageru

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Je odolné vůči selhání oblast Azure Traffic Manageru?

Správce provozu je klíčovou součástí doručení vysoce dostupných aplikací v Azure.
Traffic Manager k poskytování vysoké dostupnosti, musí mít velmi vysokou dostupnost a být odolné vůči selhání místní.

Standardně jsou odolné vůči selhání dokončení v libovolné oblasti Azure Traffic Manager součásti. Tato odolnost platí pro všechny součásti Traffic Manager: název DNS, serverů, rozhraní API, vrstvy úložiště a koncový bod služby monitorování.

K nepravděpodobnému výpadku celé oblasti Azure Traffic Manager budou i nadále fungovat normálně. Aplikace nasazené v několika oblastmi Azure můžete spoléhat na Traffic Manager slouží k řízení provozu na dostupnou instanci své aplikace.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak Volba umístění skupiny prostředků ovlivní Traffic Manageru?

Správce provozu je jediná globální služba. Není místní. Volba umístění skupiny prostředků díky žádný rozdíl profily Traffic Manager nasazené v příslušné skupině prostředků.

Azure Resource Manager vyžaduje všechny skupiny prostředků a zadejte umístění, která určuje výchozí umístění pro prostředky nasazené v příslušné skupině prostředků. Když vytvoříte profil Traffic Manageru, se vytvoří ve skupině prostředků. Všechny profily Traffic Manager používat **globální** jako jejich umístění přepsání výchozího skupiny prostředků.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak je možné zjistit aktuální stav každý koncový bod?

Aktuální stav monitorování každý koncový bod, kromě celkové profil, se zobrazí na portálu Azure. Tyto informace jsou také k dispozici prostřednictvím sledování provozu [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [rutiny prostředí PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), a [a platformy Azure CLI](../cli-install-nodejs.md).

Monitorování Azure můžete také sledovat stav koncových bodů a zobrazit je vizuální reprezentace. Další informace o používání Azure monitorování najdete v tématu [dokumentace Azure Monitoring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Můžete monitorovat koncových bodů HTTPS?

Ano. Traffic Manager podporuje zjišťování přes protokol HTTPS. Konfigurace **HTTPS** jako protokol v konfiguraci monitorování.

Správce provozu neposkytuje žádné ověření certifikátu, včetně:

* Serverové certifikáty nejsou ověřené.
* SNI serverové certifikáty nejsou podporovány.
* Klientské certifikáty nejsou podporovány.

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>I zastavena Azure cloud service / webové aplikace koncového bodu v Můj profil služby Traffic Manager, ale i po jeho restartoval veškeré přenosy doručována. Jak můžete tento problém odstranit?

Když Azure Cloudová služba / web koncový bod aplikace je zastaven Traffic Manageru zastaví kontrola jeho stav a restartuje kontroly stavu až poté, co zjistí, že má restartovat koncový bod. Aby tato prodleva, zakažte a poté je znovu povolit tohoto koncového bodu v profil služby Traffic Manager po restartování koncový bod.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Můžete použít i v případě, že Moje aplikace nemá podpora protokolu HTTP nebo HTTPS Traffic Manageru?

Ano. Můžete zadat TCP jako protokol pro monitorování a Traffic Manageru můžete iniciovat připojení TCP a čekat na odpověď z koncového bodu. Koncový bod reaguje na požadavek na připojení s odpověď k navázání připojení během časového limitu tohoto koncového bodu je označen jako v pořádku.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jaké konkrétní odpovědi jsou potřeba z koncového bodu, při použití protokolu TCP monitorování?

Při monitorování TCP se používá, spustí Traffic Manager třícestné TCP odesláním SYN požadavku na koncový bod na zadaný port. Potom počká na určitou dobu (jako je zadaný v nastavení časového limitu) pro odpověď z koncového bodu. Koncový bod odpoví na žádost SYN s SYN ACK odpověď v rámci časový limit zadaný v nastavení monitorování, že koncový bod se považuje v pořádku. Pokud je odpověď SYN potvrzení, Traffic Manager obnoví připojení pomocí reagovat zpět se RVNÍ.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak rychle přesunout Traffic Manager Moji uživatelé mimo koncový bod není v pořádku?

Traffic Manager poskytuje několik nastavení, které umožňují řídit chování převzetí služeb při selhání vašeho profilu Traffic Manageru následujícím způsobem:
- můžete určit, že Traffic Manager sondy koncových bodů častěji nastavením zkušební fáze Interval na 10 sekund. Tím se zajistí, že žádný koncový bod není v pořádku, budete se může zjistit co nejdříve. 
- můžete určit, jak dlouho má počkat, než stavu žádosti časy rezervovat (hodnota minimální časový limit je 5 s).
- můžete určit, kolik selhání může dojít předtím, než koncový bod je označen jako chybný. Tato hodnota může být v rozsahu od 0, ve kterém případ koncový bod je označen jako chybný při selhání první kontrola stavu. Však pomocí minimální hodnotu 0. povolená počet selhání může vést ke koncovým bodům se dostala mimo otočení kvůli všechny přechodné problémy, které mohou nastat při zjišťování.
- můžete zadat time to live (TTL) pro odpověď DNS na být menší než 0. V tom znamená, že překladače služby DNS nelze odpověď do mezipaměti a každém novém dotazu získá odpověď, který obsahuje nejaktuálnější informace o stavu, který má Traffic Manager.

Pomocí těchto nastavení můžete Traffic Manager zadejte převzetí služeb při selhání v části 10 sekund poté, co koncový bod přejde není v pořádku a dotazu DNS je vytvořen pro odpovídající profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak můžete zadat různé nastavení monitorování pro různými koncovými body v profilu?

Nastavení monitorování jsou v Traffic Manageru úrovni profilu. Pokud budete muset použít jiné nastavení monitorování pro pouze jeden koncový bod, je možné ji provést tak, že tohoto koncového bodu jako [vnořené profil](traffic-manager-nested-profiles.md) jejichž nastavení monitorování se liší od nadřazené profilu.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jaké proveďte koncový bod stavu pro hostitele záhlaví zjišťuje použít?

Traffic Manager používá hlavičky hostitele v kontroly stavu HTTP a HTTPS. Hlavička hostitele používaný správcem provoz je název cílového koncového bodu nakonfigurované v profilu. Hodnota použitá v hlavičce hostitele nelze zadat samostatně z vlastnost target.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jaké jsou IP adresy, ze kterých kontroluje stav pocházejí?

Klikněte na tlačítko [zde](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) zobrazíte soubor JSON, který zobrazí seznam IP adres, ze které Traffic Manager může pocházet kontroly stavu. Zkontrolujte IP adresy uvedené v souboru JSON zajistit, že jsou povolena příchozí připojení z těchto IP adres v koncových bodů zkontrolovat její stav.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Kolik kontroly stavu na můj koncový bod je možné očekávat z Traffic Manageru?

Počet stavů Traffic Manager zkontroluje, že dosažení váš koncový bod, závisí na následujících:
- hodnota, která jste nastavili pro monitorování intervalu (interval menší znamená další požadavky cílová stránka na váš koncový bod v jakékoli dané časové období).
- počet umístění, ze kterých kontroly stavu pocházejí (IP adresy, kde může být těmito kontrolami je uveden v části Nejčastější dotazy pro předchozí).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak mohu získat oznamování Pokud jeden z mých koncových bodů výpadku? 
Jedním z metriky zadaný správcem provoz je stav koncových bodů v profilu. Můžete to vidět jako agregace všechny koncové body v profilu (například 75 % koncové body jsou v pořádku), nebo v na úrovni koncového bodu. Metriky Traffic Manageru se zveřejňují přes Azure monitorování a můžete použít jeho [výstrahy možnosti](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) dostávat oznámení, když dojde ke změně v stav váš koncový bod. Další podrobnosti najdete v tématu [Traffic Manager metriky a výstrahy](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Správce provozu vnořených profilů

### <a name="how-do-i-configure-nested-profiles"></a>Konfigurování vnořených profilů

Vnořené profily Traffic Manager lze nakonfigurovat pomocí Azure Resource Manager a classic Azure rozhraní API REST, rutin prostředí Azure PowerShell a rozhraní příkazového řádku Azure příkazy napříč platformami. Podporovány jsou i prostřednictvím nového portálu Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Počet vrstev vnoření nemá provoz Manager podporuje?

Profily až 10 úrovní do hloubky vnoření. 'Cyklu' nejsou povoleny.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Můžete kombinovat jiné typy koncových bodů s profily vnořené podřízené, v jednom profilu Traffic Manageru?

Ano. Neexistují žádná omezení na tom, jak kombinovat koncové body různých typů v rámci profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak platí fakturační model pro vnořené profily?

Neexistuje žádné negativní dopad pomocí vnořených profilů – ceny.

Správce provozu fakturace má dvě součásti: kontroly stavu koncový bod a miliony dotazy DNS

* Kontroly stavu koncový bod: je bezplatná profilu podřízené při nakonfigurovaný jako koncový bod v profilu nadřazené. Monitorování koncových bodů v podřízených profilu se fakturuje obvyklým způsobem.
* Dotazy DNS: každý dotaz se počítá pouze jednou. Dotaz vůči nadřazené profil, který vrátí koncový bod z profilu podřízené se počítá s profilem nadřazené.

Úplné podrobnosti najdete v tématu [Traffic Manager stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Je k dispozici pro vnořených profilů dopad na výkon?

Ne. Neexistuje žádný dopad na výkon vzniklé při použití vnořených profilů.

Traffic Manager názvové servery procházení hierarchie profil interně při zpracování každý dotaz DNS. Dotaz DNS do nadřazené profilu může přijímat odpověď DNS se koncový bod z podřízené profilu. Jeden záznam CNAME se používá, zda používáte jediného profilu nebo vnořených profilů. Není nutné vytvořit záznam CNAME pro každý profil v hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager výpočetní stav vnořené koncového bodu v nadřazené profilu?

Profil nadřazené neprovede kontroly stavu na podřízených přímo. Místo toho stav koncových bodů podřízené profil slouží k výpočtu celkového stavu podřízené profilu. Tato informace jsou přeneseny hierarchie vnořených profilů k určení stavu vnořené koncového bodu. Profil nadřazené používá k určení, jestli provoz může přesměrovat k podřízené toto agregovaný stav.

Následující tabulka popisuje chování služby Traffic Manager kontroluje stav pro vnořené koncový bod.

| Stav monitorování podřízených profilu | Stav nadřazeného monitorování koncového bodu | Poznámky |
| --- | --- | --- |
| Zakázané. Podřízené profilu bylo zakázáno. |Zastaveno |Stav koncového bodu nadřazené je zastavena, není zakázáno. Stav Zakázáno, je vyhrazený pro indikující, že jste zakázali koncový bod v nadřazené profilu. |
| Snížený výkon. Koncový bod profilu služby alespoň jednu podřízenou je ve stavu snížený. |Online: počet Online koncových bodů v podřízených profilu je alespoň hodnota MinChildEndpoints.<BR>CheckingEndpoint: počet Online plus CheckingEndpoint koncových bodů v podřízených profilu je alespoň hodnota MinChildEndpoints.<BR>Snížený výkon: jinak. |Provoz se směruje na koncový bod stavu CheckingEndpoint. Pokud MinChildEndpoints se nastaví příliš vysoko, koncový bod vždy snížený výkon. |
| Online. Koncový bod profilu služby alespoň jednu podřízenou je stavu Online. Žádný koncový bod je ve stavu snížený. |Viz výše. | |
| CheckingEndpoints. Koncový bod profilu služby alespoň jednu podřízenou je 'CheckingEndpoint'. Nejsou žádné koncové body, Online' nebo 'snížený výkon. |Stejné jako výše. | |
| Neaktivní. Všechny podřízené profil koncové body jsou zakázán nebo zastaven, nebo tento profil nemá žádné koncové body. |Zastaveno | |

## <a name="next-steps"></a>Další kroky:
- Další informace o Traffic Manager [koncového bodu monitorování a automatické převzetí služeb při selhání](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o Traffic Manager [metodách směrování provozu](../traffic-manager/traffic-manager-routing-methods.md).
