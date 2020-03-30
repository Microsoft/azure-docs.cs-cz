---
title: Azure Traffic Manager – časté otázky
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se traffic manageru
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254362"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Nejčastější dotazy manažera provozu (FAQ)

## <a name="traffic-manager-basics"></a>Základy správce provozu

### <a name="what-ip-address-does-traffic-manager-use"></a>Jakou IP adresu traffic manager používá?

Jak je vysvětleno v [části Jak funguje traffic manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Odešle odpovědi DNS na přímé klienty do příslušného koncového bodu služby. Klienti se pak připojit ke koncovému bodu služby přímo, nikoli prostřednictvím Traffic Manager.

Traffic Manager proto neposkytuje koncový bod nebo adresu IP pro klienty, ke kterému se mají připojit. Pokud chcete statickou IP adresu pro vaši službu, která musí být nakonfigurována ve službě, nikoli v Traffic Manageru.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jaké typy provozu lze směrovat pomocí Traffic Manageru?
Jak je vysvětleno v [aplikaci How Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), koncovýbod Traffic Manageru může být libovolná služba přístupná internetu hostovaná uvnitř nebo vně Azure. Traffic Manager proto může směrovat provoz, který pochází z veřejného internetu, do sady koncových bodů, které jsou také internetové. Pokud máte koncové body, které jsou uvnitř privátní sítě (například interní verze [nástroje Azure Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer)) nebo máte uživatele, kteří požadují DNS z těchto interních sítí, pak nelze použít Traffic Manager ke směrování tohoto provozu.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Podporuje Traffic Manager "lepkavé" relace?

Jak je vysvětleno v [části Jak funguje traffic manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Používá odpovědi DNS k přesměrování klientů na příslušný koncový bod služby. Klienti se připojují ke koncovému bodu služby přímo, nikoli prostřednictvím Traffic Manageru. Traffic Manager proto nevidí přenosy HTTP mezi klientem a serverem.

Kromě toho zdrojová IP adresa dotazu DNS přijatého traffic managerem patří do rekurzivní služby DNS, nikoli ke klientovi. Traffic Manager proto nemá žádný způsob, jak sledovat jednotlivé klienty a nemůže implementovat 'sticky' relací. Toto omezení je společné pro všechny systémy správy provozu založené na službě DNS a není specifické pro Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Proč se při používání Traffic Manageru zobrazuje chyba HTTP?

Jak je vysvětleno v [části Jak funguje traffic manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Používá odpovědi DNS k přesměrování klientů na příslušný koncový bod služby. Klienti se pak připojit ke koncovému bodu služby přímo, nikoli prostřednictvím Traffic Manager. Traffic Manager nevidí přenosy HTTP mezi klientem a serverem. Proto všechny chyby PROTOKOLU HTTP, které vidíte, musí být pocházející z vaší aplikace. Aby se klient mohl připojit k aplikaci, jsou dokončeny všechny kroky překladu DNS. To zahrnuje všechny interakce, které Traffic Manager má na tok provozu aplikace.

Další šetření by se proto mělo zaměřit na žádost.

Záhlaví hostitele HTTP odeslané z prohlížeče klienta je nejčastějším zdrojem problémů. Ujistěte se, že aplikace je nakonfigurována tak, aby přijímala správnou hlavičku hostitele pro název domény, který používáte. Koncové body používající službu Azure App Service najdete [v tématu konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Jaký je dopad používání Traffic Manageru na výkon?

Jak je vysvětleno v [části Jak funguje traffic manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Vzhledem k tomu, že klienti připojit k koncovým bodům služby přímo, neexistuje žádný vliv na výkon při použití Traffic Manager po navázání připojení.

Vzhledem k tomu, že Traffic Manager integruje s aplikacemi na úrovni DNS, vyžaduje další vyhledávání DNS, které má být vloženo do řetězce rozlišení DNS. Dopad Traffic Manageru na dobu rozlišení DNS je minimální. Traffic Manager používá globální síť názvových serverů a používá sítě [libovolného vysílání](https://en.wikipedia.org/wiki/Anycast) k zajištění toho, aby dotazy DNS byly vždy směrovány na nejbližší dostupný názvový server. Ukládání odpovědí DNS do mezipaměti navíc znamená, že další latence DNS vzniklá použitím Traffic Manageru se vztahuje pouze na zlomek relací.

Metoda Performance směruje provoz na nejbližší dostupný koncový bod. Čistý výsledek je, že celkový dopad na výkon spojené s touto metodou by měla být minimální. Jakékoli zvýšení latence DNS by mělo být kompenzováno nižší latencí sítě ke koncovému bodu.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jaké aplikační protokoly lze použít s Traffic Managerem?

Jak je vysvětleno v [části Jak funguje traffic manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Po dokončení vyhledávání DNS se klienti připojují ke koncovému bodu aplikace přímo, nikoli prostřednictvím Traffic Manageru. Proto připojení můžete použít libovolný protokol aplikace. Pokud vyberete TCP jako protokol monitorování, sledování stavu koncového bodu traffic manageru lze provést bez použití jakýchkoli aplikačních protokolů. Pokud se rozhodnete mít ověření stavu pomocí protokolu aplikace, koncový bod musí být schopen reagovat na požadavky HTTP nebo HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Mohu použít Traffic Manager s "nahým" názvem domény?

Ano. Informace o tom, jak vytvořit záznam aliasu pro vrchol názvu domény tak, aby odkazoval na profil Služby Azure Traffic Manager, najdete v [tématu Konfigurace záznamu aliasu pro podporu názvů domén vrcholů pomocí Traffic Manageru](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Bere Traffic Manager při zpracování dotazů DNS v úvahu adresu podsítě klienta? 

Ano, kromě zdrojové IP adresy dotazu DNS, který obdrží (což je obvykle IP adresa překladače DNS), při vyhledávání metod směrování geografické, výkonnosti a podsítě správce provozu také zvažuje adresu podsítě klienta, pokud je součástí dotazu překládání, který podává žádost jménem koncového uživatele.  
Konkrétně [RFC 7871 – Podsíť klienta v dotazech DNS,](https://tools.ietf.org/html/rfc7871) která poskytuje [rozšiřující mechanismus pro DNS (EDNS0),](https://tools.ietf.org/html/rfc2671) který může předat adresu podsítě klienta z překladačů, které ji podporují.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co je DNS TTL a jaký to má vliv na mé uživatele?

Když dotaz DNS přistane na Traffic Manager, nastaví hodnotu v odpovědi nazvané time-to-live (TTL). Tato hodnota, jejíž jednotka je v sekundách, označuje překladačům DNS po proudu o tom, jak dlouho má být tato odpověď uložena do mezipaměti. I když není zaručeno, že překladače DNS tento výsledek uloží do mezipaměti, ukládání do mezipaměti jim umožní reagovat na všechny následné dotazy mimo mezipaměť namísto toho, aby přecčovati na servery DNS Traffic Manageru. To má dopad na následující reakce:

- vyšší TTL snižuje počet dotazů, které přistanou na serverech DNS Traffic Manager, což může snížit náklady pro zákazníka, protože počet doručených dotazů je fakturovatelné využití.
- vyšší TTL může potenciálně zkrátit čas potřebný k vyhledávání DNS.
- vyšší TTL také znamená, že vaše data neodráží nejnovější informace o stavu, které Traffic Manager získal prostřednictvím svých sondovacích agentů.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak vysoké nebo nízké lze nastavit TTL pro traffic manager odpovědi?

Na úrovni profilu můžete nastavit hodnotu TTL DNS tak nízkou, jako 0 sekund a až na 2 147 483 647 sekund (maximální rozsah kompatibilní s [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Hodnota TTL 0 znamená, že předávající servery DNS pro příjem dat neuvádí odpovědi na dotazy a očekává se, že všechny dotazy se dostanou k serverům DNS traffic manageru pro rozlišení.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak mohu porozumět objemu dotazů přicházejících do mého profilu? 

Jednou z metrik poskytovaných Traffic Manager je počet dotazů, na které profil odpověděl. Tyto informace můžete získat na úrovni profilu agregace nebo můžete rozdělit dále zobrazíte objem dotazů, kde byly vráceny konkrétní koncové body. Kromě toho můžete nastavit výstrahy, které vás upozorní, pokud svazek odpovědí na dotaz protíná nastavené podmínky. Další podrobnosti naleznete v [metrikách a výstrahách Traffic Manageru](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metoda směrování geografického provozu Traffic Manageru

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jaké jsou některé případy použití, kdy je užitečné geografické směrování?

Geografický typ směrování lze použít v jakémkoli scénáři, kde zákazník Azure potřebuje odlišit své uživatele na základě geografických oblastí. Pomocí metody směrování geografického provozu můžete například uživatelům z určitých oblastí poskytnout jiné uživatelské prostředí než uživateli z jiných oblastí. Dalším příkladem je dodržování místních oprávnění suverenity dat, které vyžadují, aby uživatelé z určité oblasti sloužili pouze koncovým bodům v této oblasti.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak se rozhodnu, jestli mám použít metodu směrování výkonu nebo metodu geografického směrování?

Hlavní rozdíl mezi těmito dvěma oblíbenými metodami směrování spočívá v tom, že v metodě směrování výkonu je vaším primárním cílem odeslat provoz do koncového bodu, který může volajícímu poskytnout nejnižší latenci, zatímco v geografickém směrování je primárním cílem vynutit geo pro volající, abyste je mohli záměrně směrovat do určitého koncového bodu. K překrytí dochází, protože existuje korelace mezi geografickou blízkostí a nižší latencí, i když to není vždy pravda. Může existovat koncový bod v jiné zeměpisné oblasti, který může poskytnout lepší latenci pro volajícího a v takovém případě směrování výkonu odešle uživatele do tohoto koncového bodu, ale geografické směrování je vždy odešle do koncového bodu, který jste namapovali pro své zeměpisné oblasti. Chcete-li to dále objasnit, zvažte následující příklad – pomocí geografického směrování můžete provádět neobvyklá mapování, například odesílat veškerý provoz z Asie do koncových bodů v USA a veškerý provoz v USA do koncových bodů v Asii. V takovém případě geografické směrování záměrně provede přesně to, k čemu jste ho nakonfigurovali, a optimalizace výkonu není zvažována. 
>[!NOTE]
>Mohou existovat scénáře, kde můžete potřebovat možnosti výkonu i geografického směrování, pro tyto scénáře vnořené profily může být skvělou volbou. Můžete například nastavit nadřazený profil se zeměpisným směrováním, ve kterém odešlete veškerý provoz ze Severní Ameriky do vnořeného profilu, který má koncové body v USA, a pomocí směrování výkonu odeslat tento provoz do nejlepšího koncového bodu v rámci této sady. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jaké oblasti, které traffic manager podporuje pro geografické směrování?

Hierarchii země nebo oblasti používanou traffic managerem naleznete [zde](traffic-manager-geographic-regions.md). I když je tato stránka udržována aktuální s případnými změnami, můžete také programově načíst stejné informace pomocí [rozhraní REST API Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak správce provozu určuje, odkud se uživatel dotazuje?

Traffic Manager se podívá na zdrojovou IP adresu dotazu (to je s největší pravděpodobností místní překladač DNS, který provádí dotazování jménem uživatele) a k určení umístění používá interní IP adresu k mapě oblasti. Tato mapa je průběžně aktualizována, aby zohledňovala změny na internetu. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Je zaručeno, že Traffic Manager může správně určit přesnou zeměpisnou polohu uživatele v každém případě?

Ne, Traffic Manager nemůže zaručit, že zeměpisná oblast, kterou odvodíme ze zdrojové IP adresy dotazu DNS, bude vždy odpovídat umístění uživatele z následujících důvodů:

- Za prvé, jak je popsáno v předchozích častých dotazech, zdrojová IP adresa, kterou vidíme, je adresa DNS, která provádí vyhledávání jménem uživatele. Zatímco geografické umístění překladače DNS je dobrým proxy serverem pro geografickou polohu uživatele, může se také lišit v závislosti na stopě služby překladače DNS a konkrétní službě překladače DNS, kterou se zákazník rozhodl použít. Jako příklad může zákazník se sídlem v Malajsii zadat v nastavení svého zařízení službu překladače DNS, jejíž server DNS v Singapuru může být vybrán pro zpracování rozlišení dotazů pro tohoto uživatele nebo zařízení. V takovém případě může Traffic Manager zobrazit pouze IP adresu překládání, která odpovídá umístění v Singapuru. Přečtěte si také dřívější nejčastější dotazy týkající se podpory adres podsítě klientů na této stránce.

- Za druhé, Traffic Manager používá interní mapu k překladu IP adresy do geografické oblasti. I když je tato mapa průběžně ověřována a aktualizována, aby se zvýšila její přesnost a zohlednila se vyvíjející se povaha internetu, stále existuje možnost, že naše informace nejsou přesným vyjádřením zeměpisné polohy veškerého duševního vlastnictví Adresy.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Musí být koncový bod fyzicky umístěn ve stejné oblasti, jakou je nakonfigurován pro geografické směrování?

Ne, umístění koncového bodu neukládá žádná omezení, na které oblasti lze mapovat na něj. Například koncový bod v oblasti AZURE mezi USA a centrálním azure může mít všechny uživatele z Indie, kteří jsou na něj přesměrováni.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Je možné přiřadit geografické oblasti koncovým bodům v profilu, který není nakonfigurován pro geografické směrování?

Ano, pokud metoda směrování profilu není geografická, můžete použít [rozhraní REST Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) k přiřazení geografických oblastí koncovým bodům v tomto profilu. V případě negeografických profilů typu směrování je tato konfigurace ignorována. Pokud tento profil později změníte na geografický typ směrování, může správce provozu tato mapování použít.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Proč se při pokusu o změnu metody směrování existujícího profilu na geografický zobrazuje chyba?

Všechny koncové body v rámci profilu s geografickým směrováním musí mít alespoň jednu oblast namapovanou. Chcete-li převést existující profil na typ geografického směrování, musíte nejprve přidružit geografické oblasti ke všem jeho koncovým bodům pomocí [rozhraní REST API Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) u převedení, než změníte typ směrování na geografický. Pokud používáte portál, nejprve odstraňte koncové body, změňte metodu směrování profilu na geografickou a pak přidejte koncové body spolu s mapováním geografické oblasti.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Proč se důrazně doporučuje, aby zákazníci vytvářeli vnořené profily namísto koncových bodů pod profilem s povoleným geografickým směrováním?

Oblast může být přiřazena pouze jeden koncový bod v rámci profilu, pokud používá metodu geografického směrování. Pokud tento koncový bod není vnořený typ s podřízený profil k němu připojené, pokud tento koncový bod bude v nepořádku, Traffic Manager nadále odesílat provoz na něj, protože alternativa není odesílání žádné ho dohledu není o nic lepší. Traffic Manager není převzetí služeb při selhání do jiného koncového bodu, i když přiřazená oblast je "nadřazená" oblasti přiřazené ke koncovému bodu, který neproběhl v pořádku (například pokud koncový bod, který má oblast Španělsko nefunguje v pořádku, nemáme převzetí služeb při selhání na jiný koncový bod, který region, který mu byla přidělena Evropa). To se provádí, aby správce provozu respektoval zeměpisné hranice, které má zákazník nastavenve svém profilu. Chcete-li získat výhodu převzetí selhání do jiného koncového bodu, když koncový bod přejde v pořádku, doporučujeme, aby geografické oblasti přiřazeny vnořené profily s více koncových bodů v něm místo jednotlivých koncových bodů. Tímto způsobem pokud koncový bod v vnořený podřízený profil selže, přenos může převzetí služeb při selhání do jiného koncového bodu uvnitř stejného vnořeného podřízeného profilu.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existují nějaká omezení pro verzi rozhraní API, která podporuje tento typ směrování?

Ano, pouze rozhraní API verze 2017-03-01 a novější podporuje typ geografického směrování. Žádné starší verze rozhraní API nelze použít k vytvoření profilů geografického typu směrování nebo přiřazení geografických oblastí koncovým bodům. Pokud se starší verze rozhraní API používá k načtení profilů z předplatného Azure, není vrácen žádný profil geografického typu směrování. Kromě toho při použití staršíverze rozhraní API všechny profil vrátil, který má koncové body s přiřazení geografické oblasti, nemá jeho přiřazení geografické oblasti zobrazeny.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metoda směrování provozu podsítě Traffic Manager

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Jaké jsou některé případy použití, kdy je směrování podsítě užitečné?

Směrování podsítí umožňuje rozlišit prostředí, které poskytujete pro konkrétní sady uživatelů identifikované zdrojovou IP adresou IP jejich požadavků DNS. Příkladem může být zobrazení jiného obsahu, pokud se uživatelé připojují k webu z vašeho podnikového ústředí. Jiným způsobem by bylo omezení uživatelů z určitých uživatelů na přístup ke koncovým bodům, které podporují pouze připojení IPv4, pokud tito uživatelé mají při použití iPv6 podřadný výkon.
Dalším důvodem použití metody směrování podsítě je ve spojení s jinými profily v vnořené sadě profilů. Chcete-li například použít metodu geografického směrování pro geografické oplocení uživatelů, ale pro konkrétního isp, kterého chcete provést jinou metodu směrování, můžete mít profil s metodou směrování podsítě jako nadřazený profil a přepsat tohoto isp pro použití určitého podřízeného profil a mají standardní geografický profil pro všechny ostatní.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Jak Traffic Manager zná IP adresu koncového uživatele?

Zařízení koncových uživatelů obvykle používají překladač DNS k vyhledávání DNS jejich jménem. Odchozí IP adresa těchto překladačů je to, co Traffic Manager vidí jako zdrojovou IP adresu. Kromě toho metoda směrování podsítě také vyhledá, zda je edns0 rozšířené podsítě klienta (ECS) informace, které byly předány s požadavkem. Pokud jsou k dispozici informace ecs, je to adresa použitá k určení směrování. Pokud informace o ECS neexistují, zdrojová IP adresa dotazu se používá pro účely směrování.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak lze zadat adresy IP při použití směrování podsítě?

Ip adresy přidružit ke koncovému bodu lze zadat dvěma způsoby. Nejprve můžete použít čtyřmístný desítkový oktační zápis s počáteční a koncovou adresou k určení rozsahu (například 1.2.3.4-5.6.7.8 nebo 3.4.5.6-3.4.5.6). Za druhé můžete použít zápis CIDR k určení rozsahu (například 1.2.3.0/24). Můžete určit více rozsahů a můžete použít oba typy zápisů v sadě rozsahů. Platí několik omezení.

-    Rozsahy adres nelze překrývat, protože každá adresa IP musí být mapována pouze na jeden koncový bod.
-    Počáteční adresa nemůže být větší než koncová adresa.
-    V případě zápisu CIDR by IP adresa před '/' měla být počáteční adresou tohoto rozsahu (například 1.2.3.0/24 je platná, ale 1.2.3.4.4/24 není platná)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak lze zadat záložní koncový bod při použití směrování podsítě?

Pokud máte v profilu se směrováním podsítě koncový bod bez namapovaných podsítí, bude sem směrován jakýkoli požadavek, který se neshoduje s jinými koncovými body. Důrazně doporučujeme, abyste měli takový záložní koncový bod ve vašem profilu, protože Traffic Manager vrátí odpověď NXDOMAIN, pokud je žádost přijde a není mapována na žádné koncové body nebo pokud je mapována na koncový bod, ale tento koncový bod není v pořádku.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co se stane, pokud je koncový bod v profilu typu směrování podsítě zakázán?

Pokud máte v profilu se směrováním podsítě koncový bod, který je zakázán, traffic manager se bude chovat, jako by tento koncový bod a mapování podsítě, které má, neexistuje. Pokud je přijat dotaz, který by odpovídal mapování IP adres a koncový bod je zakázán, Traffic Manager vrátí záložní koncový bod (jeden bez mapování) nebo pokud takový koncový bod není k dispozici, vrátí odpověď NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager MultiValue způsob směrování provozu

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Jaké jsou některé případy použití, kdy je užitečné směrování MultiValue?

Směrování MultiValue vrátí více koncových bodů v pořádku v jedné odpovědi na dotaz. Hlavní výhodou je, že pokud koncový bod není v pořádku, klient má další možnosti opakovat bez dalšího volání DNS (což může vrátit stejnou hodnotu z mezipaměti nadřazeného datového proudu). To platí pro aplikace citlivé na dostupnost, které chtějí minimalizovat prostoje.
Jiné použití pro metodu směrování MultiValue je, pokud je koncový bod "dual-homed" na adresy IPv4 a IPv6 a chcete dát volajícímu obě možnosti, ze kterých si může vybrat při inicializaci připojení ke koncovému bodu.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Kolik koncových bodů jsou vráceny při použití směrování MultiValue?

Můžete zadat maximální počet koncových bodů, které mají být vráceny a MultiValue vrátí více než mnoho koncových bodů v pořádku při přijetí dotazu. Maximální možná hodnota pro tuto konfiguraci je 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Získám stejnou sadu koncových bodů při použití směrování MultiValue?

Nemůžeme zaručit, že stejná sada koncových bodů budou vráceny v každém dotazu. To je také ovlivněno skutečností, že některé koncové body mohou být nefunkční, v tomto okamžiku nebudou zahrnuty do odpovědi

## <a name="real-user-measurements"></a>Měření reálných uživatelů

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jaké jsou výhody používání měření reálných uživatelů?

Při použití metody směrování výkonu, Traffic Manager vybere nejlepší oblast Azure pro koncového uživatele pro připojení k kontrolou zdrojové IP a PODsítě klienta EDNS (pokud je předán) a kontrolu proti inteligenci latence sítě udržuje služby. Měření reálných uživatelů tuto hodnotu vylepšuje pro vaši základnu koncových uživatelů tím, že jejich zkušenosti přispívají k této tabulce latence a zajišťují, že tato tabulka dostatečně pokrývá sítě koncových uživatelů, odkud se koncoví uživatelé připojují k Azure. To vede ke zvýšení přesnosti směrování koncového uživatele.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Můžu použít měření reálných uživatelů s oblastmi mimo Azure?

Měření reálných uživatelů měří a sestavuje jenom latenci pro dosažení oblastí Azure. Pokud používáte směrování založené na výkonu s koncovými body hostovanými v oblastech mimo Azure, můžete stále těžit z této funkce tím, že máte zvýšené informace o latenci o reprezentativní oblasti Azure, kterou jste vybrali, aby byla přidružena k tomuto koncovému bodu.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Která metoda směrování těží z měření reálných uživatelů?

Další informace získané prostřednictvím měření reálných uživatelů jsou použitelné pouze pro profily, které používají metodu směrování výkonu. Odkaz Skutečná měření uživatelů je k dispozici ze všech profilů, když ho zobrazíte prostřednictvím portálu Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Musím povolit měření reálných uživatelů každý profil zvlášť?

Ne, stačí ji povolit jednou za předplatné a všechny informace o latenci naměřené a hlášené jsou k dispozici pro všechny profily.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak vypnu měření reálných uživatelů pro své předplatné?

Můžete zastavit nabíhání poplatků souvisejících s měřením reálných uživatelů, když přestanete shromažďovat a odesílat zpět měření latence z klientské aplikace. Například při měření JavaScriptvložený do webových stránek, můžete přestat používat tuto funkci odebráním JavaScriptu nebo vypnutím jeho vyvolání při vykreslení stránky.

Skutečná měření uživatelů můžete také vypnout odstraněním klíče. Jakmile klíč odstraníte, všechna měření odeslaná do Traffic Manageru s tímto klíčem budou zahozena.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Mohu použít měření reálných uživatelů s jinými klientskými aplikacemi než webovými stránkami?

Ano, měření skutečných uživatelů je navrženo tak, aby ingestovalo data shromážděná prostřednictvím různých typů klientů koncových uživatelů. Tyto nejčastější dotazy budou aktualizovány, protože jsou podporovány nové typy klientských aplikací.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Kolik měření se provádí pokaždé, když je vykreslena moje webová stránka s povolenými měřeními skutečných uživatelů?

Při použití měření reálných uživatelů s měřením, které je k dispozici v jazyce JavaScript, má každé vykreslení stránek za následek šest provedených měření. Ty jsou pak hlášeny zpět do služby Traffic Manager. Za tuto funkci se vám bude účtovat poplatek na základě počtu měření nahlášených službě Traffic Manager. Pokud například uživatel přejde mimo vaši webovou stránku v době, kdy se měří, ale před tím, než byla nahlášena, tato měření se nepovažují za účely fakturace.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Došlo ke zpoždění, než se na mé webové stránce spustí skript Měření skutečných uživatelů?

Ne, před vyvolání skriptu neexistuje žádné naprogramované zpoždění.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Můžu měření reálných uživatelů používat jenom s oblastmi Azure, které chci měřit?

Ne, pokaždé, když je vyvolána, skript Měření reálného uživatele měří sadu šesti oblastí Azure podle služby. Tato sada se mění mezi různými vyvoláními a když dojde k velkému počtu takových vyvolání, pokrytí měření se rozprostírá v různých oblastech Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Mohu omezit počet měření provedených na konkrétní číslo?

Měření JavaScript je vložen do vaší webové stránky a máte úplnou kontrolu nad tím, kdy začít a přestat používat. Dokud služba Traffic Manager obdrží požadavek na seznam oblastí Azure, které mají být měřeny, je vrácena sada oblastí.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Mohu vidět měření provedená klientskou aplikací v rámci měření reálných uživatelů?

Vzhledem k tomu, že logika měření je spuštěna z klientské aplikace, máte plnou kontrolu nad tím, co se stane, včetně zobrazení měření latence. Traffic Manager nehlásí souhrnné zobrazení měření přijatých v rámci klíče spojeného s vaším předplatným.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Mohu upravit měřicí skript poskytovaný traffic managerem?

I když máte kontrolu nad tím, co je vloženo na vaší webové stránce, důrazně vás nedoporučujeme provádět žádné změny ve skriptu měření, abyste zajistili, že správně změří a hlásí latence.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Bude možné, aby ostatní viděli klíč, který používám s měřením skutečných uživatelů?

Když vložíte měřicí skript na webovou stránku, budou moci ostatní vidět skript a klíč PRO Měření skutečných uživatelů (RUM). Ale je důležité vědět, že tento klíč se liší od vašeho id předplatného a je generován Traffic Manager pro použití pouze pro tento účel. Znalost klíče RUM neohrozí bezpečnost vašeho účtu Azure.

### <a name="can-others-abuse-my-rum-key"></a>Mohou ostatní zneužívat můj klíč RUM?

I když je možné, aby ostatní používali váš klíč k odeslání nesprávných informací do Azure, několik chybných měření nezmění směrování, protože se bere v úvahu spolu se všemi ostatními měřeními, která obdržíme. Pokud potřebujete změnit klíče, můžete znovu vygenerovat klíč, ve kterém okamžiku se starý klíč zahodí.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Musím měřicí JavaScript vložit na všechny své webové stránky?

Měření reálných uživatelů přináší větší hodnotu s tím, jak se zvyšuje počet měření. Having řekl, že je to vaše rozhodnutí, zda je třeba, aby to ve všech svých webových stránkách, nebo několik vybraných. Naše doporučení je začít tím, že ji na nejnavštěvovanější stránce, kde se očekává, že uživatel zůstane na této stránce pět sekund nebo více.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Mohou být informace o mých koncových uživatelích identifikovány traffic managerem, pokud používám měření reálných uživatelů?

Při použití poskytnutého měření JavaScript, Traffic Manager bude mít přehled o IP adresu klienta koncového uživatele a zdrojovou IP adresu místního překladače DNS, které používají. Traffic Manager používá IP adresu klienta pouze poté, co ji zkrácena, aby nebylo možné identifikovat konkrétního koncového uživatele, který poslal měření.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Má webová stránka měření reálných uživatelských měření musí být pomocí Traffic Manager pro směrování?

Ne, není potřeba používat Traffic Manager. Strana směrování Traffic Manager pracuje odděleně od části Měření skutečného uživatele a přestože je skvělý nápad mít je oba ve stejné webové vlastnosti, nemusí být.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Musím hostovat nějakou službu v oblastech Azure, abych ji mohl používat s měřením reálných uživatelů?

Ne, nemusíte hostovat žádnou komponentu na straně serveru v Azure pro měření reálných uživatelů, aby fungovala. Bitová kopie s jedním obrazovým páják stažená měřicím JavaScriptem a službou, která ho provozuje v různých oblastech Azure, je hostovaná a spravovaná Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Zvýší se využití šířky pásma Azure, když použiju měření reálných uživatelů?

Jak již bylo zmíněno v předchozí odpovědi, součásti na straně serveru měření reálných uživatelů jsou vlastněny a spravovány Azure. To znamená, že využití šířky pásma Azure se nezvýší, protože používáte měření reálných uživatelů. To nezahrnuje žádné využití šířky pásma mimo jaké poplatky Azure. Minimalizujeme šířku pásma používanou stažením pouze jednoho obrazového bodu k měření latence do oblasti Azure. 

## <a name="traffic-view"></a>Zobrazení přenosů

### <a name="what-does-traffic-view-do"></a>K čemu zobrazení provozu dělá?

Zobrazení provozu je funkce Traffic Manageru, která vám pomůže lépe porozumět uživatelům a jejich zkušenostem. Používá dotazy přijaté Traffic Manager a tabulky inteligence latence sítě, které služba udržuje, aby vám poskytly následující:

- Oblasti, odkud se vaši uživatelé připojují k vašim koncovým bodům v Azure.
- Objem uživatelů, kteří se připojují z těchto oblastí.
- Oblasti Azure, do kterých jsou směrovány.
- Jejich latence zkušenosti do těchto oblastí Azure.

Tyto informace můžete využívat prostřednictvím geografického překrytí mapy a tabulkových zobrazení na portálu a navíc jsou k dispozici jako nezpracovaná data ke stažení.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mohu využít zobrazení provozu?

Zobrazení provozu poskytuje celkový přehled o provozu, který vaše profily traffic manageru obdrží. Zejména jej lze použít k pochopení, odkud se vaše uživatelská základna připojuje, a stejně důležité je, jaká je jejich průměrná latence. Tyto informace pak můžete použít k vyhledání oblastí, ve kterých je potřeba zaměřit, například rozšířením vaše Azure stopu do oblasti, která může sloužit těmto uživatelům s nižší latencí. Dalším přehledem, který můžete získat pomocí zobrazení provozu, je zobrazení vzorců provozu do různých oblastí, které vám zase mohou pomoci při rozhodování o zvýšení nebo snížení vynalézání v těchto oblastech.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Jak se zobrazení návštěvnosti liší od metrik Traffic Manageru dostupných prostřednictvím azure monitoru?

Azure Monitor se dá použít k pochopení na agregované úrovni provozu přijatého vaším profilem a jeho koncovými body. Umožňuje také sledovat stav koncových bodů vystavením výsledky kontroly stavu. Když potřebujete jít nad rámec těchto a pochopit prostředí koncového uživatele připojení k Azure na místní úrovni, Traffic View lze použít k dosažení tohoto cíle.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Používá zobrazení provozu informace o podsíti klienta EDNS?

Dotazy DNS obsluhované službou Azure Traffic Manager uvažují o informacích ECS, aby se zvýšila přesnost směrování. Ale při vytváření sady dat, která ukazuje, odkud se uživatelé připojují, traffic view používá pouze IP adresu překladače DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Kolik dní dat zobrazení provozu používá?

Zobrazení provozu vytvoří svůj výstup zpracováním dat ze sedmi dnů předcházejících předcházejícímu dni, kdy jste je prohlíželi. Toto je pohyblivé okno a nejnovější data budou použita při každé návštěvě.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak zobrazení provozu zpracovává externí koncové body?

Když používáte externí koncové body hostované mimo oblasti Azure v profilu Traffic Manageru, můžete si vybrat, aby byly mapovány na oblast Azure, která je proxy pro jeho charakteristiky latence (to je ve skutečnosti potřeba, pokud používáte metodu směrování výkonu). Pokud má toto mapování oblasti Azure, že metriky latence oblasti Azure se použijí při vytváření výstupu zobrazení návštěvnosti. Pokud není zadána žádná oblast Azure, informace o latenci budou prázdné v datech pro tyto externí koncové body.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Musím povolit zobrazení provozu pro každý profil v mém předplatném?

Během období náhledu bylo zobrazení provozu povoleno na úrovni předplatného. Jako součást vylepšení, které jsme provedli před obecnou dostupností, můžete nyní povolit zobrazení provozu na úrovni profilu, což vám umožní mít podrobnější povolení této funkce. Ve výchozím nastavení bude zobrazení provozu pro profil zakázáno.

>[!NOTE]
>Pokud jste během doby náhledu povolili zobrazení provozu na úrovni předplatného, musíte ho nyní znovu povolit pro každý profil v rámci tohoto předplatného.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak mohu vypnout traffic view?

Zobrazení provozu můžete vypnout pro libovolný profil pomocí rozhraní Portal nebo REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Jak funguje fakturace zobrazení provozu?

Ceny zobrazení provozu jsou založeny na počtu datových bodů použitých k vytvoření výstupu. V současné době je podporován pouze datový typ dotazy, které váš profil obdrží. Kromě toho se vám budou účtovat pouze zpracování, které bylo provedeno, když máte povoleno zobrazení provozu. To znamená, že pokud povolíte zobrazení provozu po určitou dobu v měsíci a vypnete ho v jiných časech, do vaší faktury se započítávají pouze datové body zpracovávané v době, kdy je tato funkce povolena.

## <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Můžu použít Traffic Manager s koncovými body z více předplatných?

Použití koncových bodů z více předplatných není možné s Azure Web Apps. Azure Web Apps vyžaduje, aby se jakýkoli vlastní název domény používaný s webovými aplikacemi používal jenom v rámci jednoho předplatného. Není možné používat webové aplikace z více předplatných se stejným názvem domény.

Pro ostatní typy koncových bodů je možné použít Traffic Manager s koncovými body z více než jednoho předplatného. Ve Správci prostředků mohou být koncové body z libovolného předplatného přidány do Traffic Manageru, pokud osoba, která konfiguruje profil Traffic Manageru, má přístup pro čtení ke koncovému bodu. Tato oprávnění lze udělit pomocí [řízení přístupu na základě rolí Azure Resource Manager (RBAC)](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Mohu použít Traffic Manager s 'Staging' sloty cloudové služby?

Ano. Pracovní sloty cloudové služby lze ve Správci provozu nakonfigurovat jako externí koncové body. Kontroly stavu se pořád účtují sazbou azure koncových bodů.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Podporuje Traffic Manager koncové body IPv6?

Traffic Manager v současné době neposkytuje názvové servery adresovatelné iPv6. Traffic Manager však stále mohou používat klienti IPv6, kteří se připojují ke koncovým bodům IPv6. Klient neprovádí požadavky DNS přímo do Traffic Manageru. Místo toho klient používá rekurzivní službu DNS. Klient pouze s IPv6 odesílá požadavky rekurzivní službě DNS prostřednictvím iPv6. Rekurzivní služba by pak měla být schopna kontaktovat názvové servery Traffic Manageru pomocí iPv4.

Traffic Manager odpoví názvem DNS nebo IP adresou koncového bodu. Pro podporu koncového bodu IPv6 existují dvě možnosti. Koncový bod můžete přidat jako název DNS, který má přidružený záznam AAAA a Traffic Manager tento koncový bod zkontroluje a vrátí jej jako typ záznamu CNAME v odpovědi na dotaz. Tento koncový bod můžete také přidat přímo pomocí adresy IPv6 a Traffic Manager vrátí záznam typu AAAA v odpovědi na dotaz.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Můžu používat Traffic Manager s více než jednou webovou aplikací ve stejné oblasti?

Traffic Manager se obvykle používá k přesměrování provozu na aplikace nasazené v různých oblastech. Lze jej však také použít v případě, že aplikace má více než jedno nasazení ve stejné oblasti. Koncové body Azure Traffic Manageru nepovolují přidání více než jednoho koncového bodu Webové aplikace ze stejné oblasti Azure do stejného profilu Traffic Manageru.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Jak můžu přesunout koncové body profilu Azure svého profilu Traffic Manageru do jiné skupiny prostředků nebo předplatného?

Koncové body Azure, které jsou přidruženy k profilu Traffic Manageru, jsou sledovány pomocí jejich ID prostředků. Když se prostředek Azure, který se používá jako koncový bod (například veřejná IP adresa, klasická cloudová služba, webapp nebo jiný profil Traffic Manageru používaný vnořeným způsobem), přesune do jiné skupiny prostředků nebo předplatného, změní se jeho ID prostředků. V tomto scénáři je aktuálně nutné aktualizovat profil Traffic Manager nejprve odstraněním a přidáním koncových bodů do profilu.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Je Traffic Manager odolný vůči selhání mno ství Azure?

Traffic Manager je klíčovou součástí doručování vysoce dostupných aplikací v Azure.
Chcete-li zajistit vysokou dostupnost, traffic manager musí mít mimořádně vysokou úroveň dostupnosti a musí být odolné vůči selhání místní oblasti.

Podle návrhu součásti Traffic Manager jsou odolné vůči úplné selhání jakékoli oblasti Azure. Tato odolnost se vztahuje na všechny součásti Traffic Manageru: názvové servery DNS, rozhraní API, vrstvu úložiště a službu sledování koncového bodu.

V nepravděpodobném případě výpadku celé oblasti Azure Traffic Manager se očekává, že i nadále fungovat normálně. Aplikace nasazené ve více oblastech Azure se můžou spolehnout na Traffic Manager, aby nasměrovaly provoz na dostupnou instanci své aplikace.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak volba umístění skupiny prostředků ovlivňuje Traffic Manager?

Traffic Manager je jediná globální služba. Není regionální. Volba umístění skupiny prostředků nemá žádný vliv na profily Traffic Manageru nasazené v této skupině prostředků.

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určit umístění, které určuje výchozí umístění pro prostředky nasazené v této skupině prostředků. Když vytvoříte profil Traffic Manageru, vytvoří se ve skupině prostředků. Všechny profily Traffic Manageru používají jako své umístění **globální** a převažují nad výchozí hodnotou skupiny prostředků.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak zjistím aktuální stav jednotlivých koncových bodů?

Aktuální stav monitorování každého koncového bodu, kromě celkového profilu, se zobrazí na webu Azure Portal. Tyto informace jsou také k dispozici prostřednictvím [rozhraní REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx)monitoru provozu , rutin prostředí PowerShell a rozhraní [příkazového příkazu Azure pro různé platformy](../cli-install-nodejs.md). [PowerShell cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)

Azure Monitor můžete také použít ke sledování stavu koncových bodů a zobrazit jejich vizuální reprezentaci. Další informace o používání Azure Monitoru najdete v [dokumentaci k monitorování Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Můžu sledovat koncové body HTTPS?

Ano. Traffic Manager podporuje zjišťování přes HTTPS. Nakonfigurujte **protokol HTTPS** jako protokol v konfiguraci monitorování.

Správce provozu nemůže poskytnout žádné ověření certifikátu, včetně:

* Certifikáty na straně serveru nejsou ověřeny.
* Certifikáty na straně serveru SNI nejsou ověřeny
* Klientské certifikáty nejsou podporovány.

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Mám při přidávání koncového bodu používat IP adresu nebo název DNS?

Traffic Manager podporuje přidávání koncových bodů pomocí tří způsobů, jak je odkazovat – jako název DNS, jako adresu IPv4 a jako adresu IPv6. Pokud je koncový bod přidán jako adresa IPv4 nebo IPv6, bude odpověď na dotaz typu A nebo AAAA. Pokud byl koncový bod přidán jako název DNS, bude odpověď na dotaz typu CNAME. Přidání koncových bodů jako adresu IPv4 nebo IPv6 je povoleno pouze v případě, že koncový bod je typu **Externí**.
Všechny metody směrování a nastavení monitorování jsou podporovány třemi typy adresování koncových bodů.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jaké typy IP adres lze použít při přidávání koncového bodu?

Traffic Manager umožňuje používat adresy IPv4 nebo IPv6 k určení koncových bodů. Existuje několik omezení, která jsou uvedena níže:

- Adresy, které odpovídají vyhrazeným privátním adresním adresním adresám IP, nejsou povoleny. Tyto adresy zahrnují adresy vRč 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 a RFC 5771
- Adresa nesmí obsahovat žádná čísla portů (můžete zadat porty, které mají být použity v nastavení konfigurace profilu)
- Žádné dva koncové body ve stejném profilu nesmí mít stejnou cílovou IP adresu.

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Je možné v rámci jednoho profilu používat různé typy adresování koncového bodu?

Ne, Traffic Manager neumožňuje kombinovat typy adres koncových bodů v rámci profilu, s výjimkou případu profilu s typem směrování MultiValue, kde můžete kombinovat typy adres IPv4 a IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co se stane, když se typ záznamu příchozího dotazu liší od typu záznamu přidruženého k typu adresování koncových bodů?

Při přijetí dotazu proti profilu, Traffic Manager nejprve najde koncový bod, který je třeba vrátit podle zadané metody směrování a stav koncových bodů. Potom se podívá na typ záznamu požadovaný v příchozídotaz a typ záznamu přidružené ke koncovému bodu před vrácením odpovědi na základě níže uvedené tabulky.

Pro profily s jinou metodou směrování než MultiValue:

|Požadavek na příchozí dotaz|     Typ koncového bodu|     Poskytnutá odpověď|
|--|--|--|
|JAKÉKOLI |    A / AAAA / CNAME |    Cílový koncový bod| 
|A |    A / CNAME |    Cílový koncový bod|
|A |    AAAA |    ŽÁDNÁ DATA |
|AAAA |    AAAA / CNAME |    Cílový koncový bod|
|AAAA |    A |    ŽÁDNÁ DATA |
|CNAME |    CNAME |    Cílový koncový bod|
|CNAME     |A / AAAA |    ŽÁDNÁ DATA |
|

Pro profily s metodou směrování nastavenou na MultiValue:

|Požadavek na příchozí dotaz|     Typ koncového bodu |    Poskytnutá odpověď|
|--|--|--|
|JAKÉKOLI |    Mix of A and AAAA |    Cílové koncové body|
|A |    Mix of A and AAAA |    Pouze cílové koncové body typu A|
|AAAA    |Mix of A and AAAA|     Pouze cílové koncové body typu AAAA|
|CNAME |    Mix of A and AAAA |    ŽÁDNÁ DATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Můžu použít profil s koncovými body adresovanými IPv4 / IPv6 ve vnořený profil?

Ano, můžete s výjimkou, že profil typu MultiValue nemůže být nadřazeným profilem v vnořené sadě profilů.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Zastavil jsem koncový bod webové aplikace v mém profilu Traffic Manager, ale nedostávám žádný provoz ani poté, co jsem restartoval. Jak to mohu opravit?

Když koncový bod webové aplikace Azure je zastaventraffic Manager zastaví kontrolu jeho stavu a restartuje kontroly stavu až poté, co zjistí, že koncový bod byl restartován. Chcete-li zabránit tomuto zpoždění, zakažte a znovu povolte tento koncový bod v profilu Traffic Manager po restartování koncového bodu.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Mohu použít Traffic Manager, i když moje aplikace nemá podporu pro HTTP nebo HTTPS?

Ano. Můžete zadat TCP jako protokol monitorování a Traffic Manager můžete zahájit připojení TCP a čekat na odpověď z koncového bodu. Pokud koncový bod odpoví na požadavek na připojení s odpovědí k navázání připojení, v časovém limitu, pak je tento koncový bod označen jako v pořádku.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jaké konkrétní odpovědi jsou požadovány od koncového bodu při použití monitorování TCP?

Při použití monitorování TCP, Traffic Manager spustí třícestný TCP handshake odesláním syn požadavek na koncový bod na zadaný port. Potom čeká na odpověď SYN-ACK z koncového bodu po určitou dobu (zadanou v nastavení časového času).

- Pokud syn-Ack odpověď je přijata v časovém období určeném v nastavení monitorování, pak tento koncový bod je považován za v pořádku. FIN nebo FIN-ACK je očekávaná odpověď od Traffic Manager při pravidelném ukončení soketu.
- Pokud syn-Ack odpověď je přijata po zadaném časovém limitu, Traffic Manager odpoví rst obnovit připojení.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak rychle traffic manager přesouvá uživatele od koncového bodu není v pořádku?

Traffic Manager poskytuje několik nastavení, která vám mohou pomoci řídit chování při převzetí služeb při selhání profilu Traffic Manageru následujícím způsobem:

- můžete určit, že Traffic Manager sonduje koncové body častěji nastavením intervalu sondování na 10 sekund. Tím je zajištěno, že všechny koncový bod bude v nepořádku lze zjistit co nejdříve. 
- můžete určit, jak dlouho čekat před časovým výpadkem požadavku na kontrolu stavu (minimální hodnota časového výpadku je 5 sekund).
- můžete určit, kolik selhání může dojít před koncový bod je označen jako není v pořádku. Tato hodnota může být nízká jako 0, v takovém případě je koncový bod označen jako není v pořádku, jakmile selže první kontrolu stavu. Použití minimální hodnoty 0 pro tolerovaný počet selhání však může vést k koncové body vyjmuty z rotace z důvodu jakékoli přechodné problémy, které mohou nastat v době zjišťování.
- můžete určit dobu do provozu (TTL) pro odpověď DNS tak nízké, jak 0. To znamená, že překladače DNS nemohou ukládat odpověď do mezipaměti a každý nový dotaz získá odpověď, která obsahuje nejaktuálnější informace o stavu, které správce provozu má.

Pomocí těchto nastavení Traffic Manager může poskytnout převzetí služeb při selhání pod 10 sekund poté, co koncový bod přejde není v pořádku a dotaz DNS je proveden proti odpovídající profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak lze zadat různá nastavení monitorování pro různé koncové body v profilu?

Nastavení monitorování správce provozu jsou na úrovni profilu. Pokud potřebujete použít jiné nastavení monitorování pouze pro jeden koncový bod, lze to provést tím, že tento koncový bod jako [vnořený profil,](traffic-manager-nested-profiles.md) jehož nastavení monitorování se liší od nadřazeného profilu.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak lze přiřadit hlavičky HTTP ke kontrolám stavu Traffic Manageru koncovým bodům?

Traffic Manager umožňuje zadat vlastní záhlaví v http(S) kontroly stavu, které iniciuje do koncových bodů. Pokud chcete zadat vlastní záhlaví, můžete to udělat na úrovni profilu (platí pro všechny koncové body) nebo zadat na úrovni koncového bodu. Pokud je záhlaví definováno na obou úrovních, přepíše úroveň profilu na úrovni profilu.
Jeden běžný případ použití pro toto je určení záhlaví hostitele tak, aby požadavky Traffic Manager může získat správně směrovány do koncového bodu hostovaného v prostředí s více klienty. Dalším případem použití je identifikace požadavků Traffic Manageru z protokolů požadavků http(S) koncového bodu

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakou hlavičku hostitele používají kontroly stavu koncového bodu?

Pokud není k dispozici žádné vlastní nastavení záhlaví hostitele, záhlaví hostitele používané Traffic Manager je název DNS cíle koncového bodu nakonfigurovanév profilu, pokud je k dispozici.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jaké jsou IP adresy, ze kterých pocházejí kontroly stavu?

Klepnutím [sem](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) zobrazíte soubor JSON se seznamem adres IP, ze kterých mohou být odebrány kontroly stavu traffic manageru. Zkontrolujte IP adresy uvedené v souboru JSON a ujistěte se, že příchozí připojení z těchto ADRES IP jsou povoleny v koncových bodech ke kontrole stavu.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Kolik kontrol stavu do mého koncového bodu mohu očekávat od Traffic Manager?

Počet kontrol stavu Traffic Manageru, které se dostaly ke koncovému bodu, závisí na následujících:

- hodnota, kterou jste nastavili pro interval monitorování (menší interval znamená více požadavků přistání na koncovém bodu v daném časovém období).
- počet míst, odkud pocházejí kontroly stavu (IP adresy, ze kterých můžete očekávat tyto kontroly, jsou uvedeny v předchozích nejčastějších dotazech).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak se dostanu upozorněni, když jeden z mých koncových bodů klesne?

Jednou z metrik poskytovaných Traffic Manager je stav koncových bodů v profilu. Můžete to vidět jako agregaci všech koncových bodů uvnitř profilu (například 75 % koncových bodů je v pořádku) nebo na úrovni koncového bodu. Metriky Traffic Manageru jsou zveřejňovány prostřednictvím Azure Monitoru a můžete použít jeho [možnosti upozorňování](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) k získání oznámení, když dojde ke změně stavu vašeho koncového bodu. Další podrobnosti najdete v [tématu Metriky a upozornění Traffic Manageru](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Vnořené profily správce provozu

### <a name="how-do-i-configure-nested-profiles"></a>Jak nakonfiguruji vnořené profily?

Vnořené profily Traffic Manageru lze nakonfigurovat pomocí Azure Resource Manager u klasických api Azure REST, rutin Azure PowerShell a příkazů Azure CLI napříč platformami. Jsou také podporované prostřednictvím nového portálu Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Kolik vrstev hnízdění podporuje Traffic Manger?

Profily můžete vnořit až do hloubky 10 úrovní. "Smyčky" nejsou povoleny.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Můžu kombinovat jiné typy koncových bodů s vnořenými podřízenými profily ve stejném profilu Traffic Manageru?

Ano. Neexistují žádná omezení, jak kombinovat koncové body různých typů v rámci profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak se fakturační model vztahuje na vnořené profily?

Neexistuje žádný negativní cenový dopad použití vnořených profilů.

Fakturace Traffic Manageru má dvě součásti: kontroly stavu koncového bodu a miliony dotazů DNS

* Kontroly stavu koncového bodu: Při konfiguraci jako koncový bod v nadřazeném profilu se neúčtuje žádný poplatek za podřízený profil. Sledování koncových bodů v podřízeném profilu se účtuje obvyklým způsobem.
* Dotazy DNS: Každý dotaz se počítá pouze jednou. Dotaz proti nadřazenému profilu, který vrací koncový bod z podřízeného profilu, se započítá pouze do nadřazeného profilu.

Podrobné informace najdete na [stránce S cenami traffic manageru](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Má vnořené profily dopad na výkon?

Ne. Při použití vnořených profilů nedochází k žádnému dopadu na výkon.

Názvové servery Traffic Manageru procházejí hierarchií profilů interně při zpracování každého dotazu DNS. Dotaz DNS pro nadřazený profil může přijímat odpověď DNS s koncovým bodem z podřízeného profilu. Jeden záznam CNAME se používá bez ohledu na to, zda používáte jeden profil nebo vnořené profily. Není nutné vytvářet záznam CNAME pro každý profil v hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager vypočítá vnořený koncový bod v nadřazeném profilu?

Nadřazený profil neprovádí kontroly stavu dítěte přímo. Místo toho se k výpočtu celkového stavu podřízeného profilu používá stav koncových bodů podřízeného profilu. Tyto informace jsou šířeny do hierarchie vnořených profilů k určení stavu vnořeného koncového bodu. Nadřazený profil používá tento agregovaný stav k určení, zda může být provoz směrován na podřízenou položku.

Následující tabulka popisuje chování kontrolstavu Traffic Manageru pro vnořený koncový bod.

| Stav sledování podřízeného profilu | Nadřazený stav sledování koncového bodu | Poznámky |
| --- | --- | --- |
| Zakázáno Podřízený profil byl zakázán. |Zastaveno |Nadřazený stav koncového bodu je zastaven, není zakázán. Stav Zakázáno je vyhrazen pro označení, že jste zakázali koncový bod v nadřazeném profilu. |
| Degradované. Alespoň jeden podřízený koncový bod profilu je ve stavu Degraded. |Online: počet online koncových bodů v podřízeném profilu je alespoň hodnota MinChildEndpoints.<BR>Kontrola Endpoint: počet online plus CheckingEndpoint koncové body v podřízeném profilu je alespoň hodnota MinChildEndpoints.<BR>Degradováno: jinak. |Provoz je směrován do koncového bodu stavu CheckingEndpoint. Pokud MinChildEndpoints je nastavena příliš vysoká, koncový bod je vždy degradován. |
| Online. Alespoň jeden podřízený koncový bod profilu je stav online. Žádný koncový bod je ve stavu Degraded. |Viz výše. | |
| Kontrola koncových bodů. Alespoň jeden podřízený koncový bod profilu je "CheckingEndpoint". Žádné koncové body jsou online nebo degradované. |Stejně jako výše. | |
| Neaktivní. Všechny koncové body podřízeného profilu jsou zakázány nebo zastaveny, nebo tento profil nemá žádné koncové body. |Zastaveno | |

## <a name="next-steps"></a>Další kroky:

- Přečtěte si další informace o sledování koncového bodu Traffic [Manageru a automatickém převzetí služeb při selhání](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o [metodách směrování provozu](../traffic-manager/traffic-manager-routing-methods.md)traffic manageru .
