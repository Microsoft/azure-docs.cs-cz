---
title: Azure Traffic Manager – Nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: duau
ms.openlocfilehash: fa8dba12a050e42e258e4224f29e379ff53f09d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576678"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Nejčastější dotazy Traffic Manager

## <a name="traffic-manager-basics"></a>Základy Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Jaká IP adresa Traffic Manager použít?

Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Odesílá odpovědi DNS na přesměrování klientů do příslušného koncového bodu služby. Klienti se pak připojují přímo k koncovému bodu služby, nikoli prostřednictvím Traffic Manager.

Proto Traffic Manager neposkytuje koncový bod nebo IP adresu, ke kterým se klienti připojují. Pokud chcete pro vaši službu statickou IP adresu, musí být nakonfigurovaná ve službě, ne v Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jaké typy přenosů se dají směrovat pomocí Traffic Manager?
Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager koncový bod může být libovolná internetová služba hostovaná v nebo mimo Azure. Proto Traffic Manager můžou směrovat provoz, který pochází z veřejného Internetu, do sady koncových bodů, které jsou taky na internetu. Pokud máte koncové body, které jsou uvnitř privátní sítě (například interní verze [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)), nebo pokud chcete, aby uživatelé prováděli žádosti DNS z těchto interních sítí, nebudete moci použít Traffic Manager ke směrování tohoto provozu.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Podporuje Traffic Manager "rychlé" relace?

Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Pomocí odpovědí DNS směruje klienty do příslušného koncového bodu služby. Klienti se připojují přímo ke koncovému bodu služby, nikoli prostřednictvím Traffic Manager. Proto Traffic Manager nevidí přenos HTTP mezi klientem a serverem.

Kromě toho zdrojová IP adresa dotazu DNS přijatého Traffic Manager patří do rekurzivní služby DNS, nikoli z klienta. Proto Traffic Manager nemá žádný způsob, jak sledovat jednotlivé klienty a nemůže implementovat "rychlé" relace. Toto omezení je běžné pro všechny systémy správy provozu založené na DNS a není specifické pro Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Proč se mi při použití Traffic Manager zobrazuje chyba protokolu HTTP?

Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Pomocí odpovědí DNS směruje klienty do příslušného koncového bodu služby. Klienti se pak připojují přímo k koncovému bodu služby, nikoli prostřednictvím Traffic Manager. Traffic Manager nevidí přenos HTTP mezi klientem a serverem. Všechny chyby HTTP, které vidíte, se proto musí nacházet z vaší aplikace. Aby se klient mohl připojit k aplikaci, všechny kroky překladu DNS jsou dokončené. Zahrnuje všechny interakce, které Traffic Manager mají v toku provozu aplikací.

Další šetření by proto mělo být zaměřeno na aplikaci.

Hlavička hostitele HTTP, která je odeslána z prohlížeče klienta, je nejběžnější zdrojem problémů. Ujistěte se, že aplikace je nakonfigurovaná tak, aby přijímala správnou hlavičku hostitele pro název domény, který používáte. Koncových bodů pomocí Azure App Service najdete v tématu [Konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Jaký je dopad na výkon při používání Traffic Manager?

Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Vzhledem k tomu, že se klienti připojují k koncovým bodům služby přímo, nedochází při použití Traffic Manager po navázání tohoto připojení k žádnému dopadu na výkon.

Vzhledem k tomu, že Traffic Manager se integruje s aplikacemi na úrovni DNS, vyžaduje se vložení dalšího vyhledávání DNS do řetězce překladu DNS. Dopad Traffic Manager na dobu překladu DNS je minimální. Traffic Manager používá globální síť názvových serverů [a používá síť](https://en.wikipedia.org/wiki/Anycast) s více jmény k zajištění toho, aby se dotazy DNS vždycky směrovaly na nejbližší dostupný názvový server. Kromě toho ukládání odpovědí DNS do mezipaměti znamená, že další latence DNS vzniklé pomocí Traffic Manager platí jenom pro zlomek relací.

Metoda Performance směruje provoz do nejbližšího dostupného koncového bodu. Výsledkem netto je, že celkový dopad na výkon spojený s touto metodou by měl být minimální. Jakékoli zvýšení latence DNS by mělo být posunuto o nižší latenci sítě ke koncovému bodu.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jaké aplikační protokoly můžu používat s Traffic Manager?

Jak je vysvětleno, [jak Traffic Manager funguje](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager funguje na úrovni DNS. Po dokončení vyhledávání DNS se klienti připojují přímo ke koncovému bodu aplikace, nikoli prostřednictvím Traffic Manager. Připojení proto může používat libovolný aplikační protokol. Pokud jako protokol monitorování vyberete TCP, můžete monitorování stavu koncového bodu Traffic Manager provést bez použití protokolů aplikací. Pokud se rozhodnete, že budete mít stav ověřený pomocí aplikačního protokolu, musí být koncový bod schopný reagovat na požadavky GET HTTP nebo HTTPS.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Můžu použít Traffic Manager s názvem domény "holé"?

Ano. Informace o tom, jak vytvořit záznam aliasu pro název vaší domény, aby odkazoval na profil Azure Traffic Manager, najdete v tématu [Konfigurace záznamu aliasu pro podporu názvů vrcholu domény pomocí Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Považuje Traffic Manager adresa klientské podsítě při zpracování dotazů DNS? 

Ano, kromě zdrojové IP adresy dotazu DNS, který přijímá (obvykle se jedná o IP adresu překladače DNS), při provádění vyhledávání pro metody směrování geografických, výkon a podsítí se Traffic Manager považuje také za adresu klientské podsítě, pokud je zahrnutý v dotazu překladačem, který provádí požadavek jménem koncového uživatele.  
Konkrétně [RFC 7871 – podsíť klienta v dotazech DNS](https://tools.ietf.org/html/rfc7871) , která poskytuje [mechanismus rozšíření pro DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) , který může předat adresu klientské podsítě z překladačů, které ji podporují.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co je DNS TTL a jak má vliv na uživatele?

Když se na Traffic Manager dotaz DNS, nastaví hodnotu v odpovědi s názvem Time-to-Live (TTL). Tato hodnota, jejíž jednotka je v sekundách, označuje překladače DNS po dobu ukládání této odpovědi do mezipaměti. I když není zaručené ukládání tohoto výsledku Překladači DNS do mezipaměti, umožníte jim, aby jim odpověděl na všechny následné dotazy z mezipaměti, a ne na Traffic Manager servery DNS. To má vliv na odezvy následujícím způsobem:

- vyšší hodnota TTL snižuje počet dotazů, které jsou na Traffic Manager serverech DNS, což může snížit náklady na zákazníka, protože počet zpracovaných dotazů je Fakturovatelné využití.
- vyšší hodnota TTL může potenciálně zkrátit dobu potřebnou k vyhledání DNS.
- vyšší hodnota TTL také znamená, že vaše data nereflektují nejnovější informace o stavu, které Traffic Manager získali prostřednictvím svých agentů pro zjišťování.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak vysoká nebo nízká můžu nastavit hodnotu TTL pro Traffic Manager odezvy?

Na úrovni profilu můžete nastavit, aby hodnota TTL služby DNS byla menší než 0 sekund a 2 147 483 647 sekund (maximální rozsah odpovídá [specifikaci RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Hodnota TTL (0) znamená, že převádění DNS pro příjem dat neukládají odpovědi na dotazy do mezipaměti a všechny dotazy se očekávají, že budou mít přístup k Traffic Manager serverům DNS pro řešení.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak můžu pochopit objem dotazů přicházejících do svého profilu? 

Jedna z metrik, kterou poskytuje Traffic Manager, je počet dotazů, které jsou v profilu odpovědi. Tyto informace můžete získat na základě agregace na úrovni profilu nebo ji můžete rozdělit a zobrazit tak objem dotazů, ve kterých byly vráceny konkrétní koncové body. Kromě toho můžete nastavit výstrahy, které vás upozorní, pokud svazek s odpovědí na dotaz překračuje podmínky, které jste nastavili. Další podrobnosti [Traffic Manager metriky a upozorněních](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager způsob směrování geografického provozu

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jaké jsou některé případy použití, kde se geografické směrování hodí?

Typ geografického směrování se dá použít v jakémkoli scénáři, kdy zákazník Azure potřebuje odlišit své uživatele na základě geografických oblastí. Například pomocí metody směrování geografických přenosů můžete uživatelům z určitých oblastí poskytnout jiné uživatelské prostředí než ty z jiných oblastí. Dalším příkladem je splnění místních nároků na svrchovanost dat, které vyžadují, aby se uživatelé z konkrétní oblasti mohli dodávat jenom koncovými body v této oblasti.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Návody rozhodnout, jestli mám použít metodu směrování výkonu nebo metodu geografického směrování?

Hlavním rozdílem mezi těmito dvěma oblíbenými metodami směrování je to, že v rámci směrování výkonu je vaším primárním cílem odeslání provozu do koncového bodu, který může poskytnout nejnižší latenci volajícího, zatímco v geografickém směrování primární cíl je vymáhat geografickou ochranou pro vaše volající, aby je bylo možné úmyslně směrovat do konkrétního koncového bodu. K překrytí dochází, protože existuje korelace mezi geografickou těsnou a nižší latencí, přestože to není vždy true. V různých zeměpisných oblastech může být koncový bod, který umožňuje lepší latenci volajícího a v takovém případě směrování výkonu odešle uživateli do tohoto koncového bodu, ale geografické směrování je vždycky pošle do koncového bodu, který jste namapovali pro svou geografickou oblast. Pokud ho chcete dál vyjasnit, vezměte v úvahu následující příklad – s geografickým směrováním můžete provádět neobvyklá mapování, jako je například odesílání všech přenosů z Asie do koncových bodů v USA a veškerý provoz v Asii do koncových bodů v Asii. V takovém případě geografické směrování záměrně provede přesně to, co jste nakonfigurovali a optimalizace výkonu neplatí. 
>[!NOTE]
>Může nastat situace, kdy budete možná potřebovat možnosti výkonu i geografického směrování. pro tyto scénáře může být vhodná volba pro vnořené profily. Můžete například nastavit nadřazený profil s geografickým směrováním, kde odesíláte veškerý provoz z Severní Amerika do vnořeného profilu, který obsahuje koncové body v USA, a použít směrování výkonu k odeslání těchto dat do nejlepšího koncového bodu v této sadě. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jaké jsou oblasti podporované Traffic Manager pro geografické směrování?

Hierarchii země nebo oblasti, kterou používá Traffic Manager, najdete [tady](traffic-manager-geographic-regions.md). I když je tato stránka stále aktuální se změnami, můžete také programově načíst stejné informace pomocí [Traffic Manager Azure REST API](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak Traffic Manager zjišťuje, ze kterého uživatele se dotazuje?

Traffic Manager prochází se zdrojovou IP adresou dotazu (pravděpodobně se jedná o místní Překladač DNS, který provádí dotazování jménem uživatele), a k určení umístění používá mapu interní IP adresa pro oblast. Tato mapa se průběžně aktualizuje, aby se zohlednily změny v Internetu. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Je zaručeno, že Traffic Manager dokáže správně určit přesné geografické umístění uživatele v každém případě?

Ne, Traffic Manager nemůže zaručit, že geografická oblast odvozená ze zdrojové IP adresy dotazu DNS bude vždycky odpovídat umístění uživatele z následujících důvodů:

- Nejdříve, jak je popsáno v předchozích nejčastějších dotazech, se zobrazuje zdrojová IP adresa, kterou překládá DNS při vyhledávání jménem uživatele. I když je zeměpisné umístění překladače DNS dobrým proxy serverem pro zeměpisnou polohu uživatele, může se také lišit v závislosti na tom, jaké jsou nároky na službu DNS Resolver a konkrétní služba překladače DNS, kterou si zákazník rozhodl použít. Příklad: zákazník umístěný v Malajsii může v nastavení zařízení zadat službu DNS Resolver, jejíž server DNS v Singapuru může vyzvednutím rozlišení dotazů pro daného uživatele nebo zařízení. V takovém případě Traffic Manager uvidí jenom IP adresu překladače, která odpovídá umístění Singapuru. Podívejte se také na předchozí Nejčastější dotazy týkající se podpory adres klientských podsítí na této stránce.

- Druhý Traffic Manager k překladu IP adres na geografickou oblast používat interní mapu. I když je tato mapa ověřená a průběžně se aktualizuje, aby se zvýšila její přesnost a účet pro vývojovou povahu internetu, stále existuje možnost, že naše informace nepředstavuje přesnou reprezentaci geografického umístění všech IP adres.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Musí být koncový bod fyzicky umístěný ve stejné oblasti, ve které je nakonfigurovaný pro geografické směrování?

Ne, umístění koncového bodu neukládá žádná omezení, na které lze namapovat oblasti. Například koncový bod v US-Central oblasti Azure může mít směrovaného uživatele z Indie.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Je možné přiřazovat geografické oblasti koncovým bodům v profilu, který není nakonfigurován tak, aby provedl geografické směrování?

Ano, pokud metoda směrování profilu není geografická, můžete k přiřazení geografických oblastí k koncovým bodům v tomto profilu použít [REST API Traffic Manager Azure](/rest/api/trafficmanager/) . V případě negeografických profilů typu směrování se tato konfigurace ignoruje. Pokud takový profil změníte na typ geografické směrování později, Traffic Manager můžou tato mapování použít.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Proč se při pokusu o změnu metody směrování stávajícího profilu na geografickou chybu zobrazuje chyba?

Všechny koncové body v profilu s geografickým směrováním musí mít namapovanou aspoň jednu oblast. Pokud chcete převést stávající profil na typ geografické směrování, musíte nejdřív přidružit geografické oblasti ke všem jeho koncovým bodům pomocí [Traffic Manager Azure REST API](/rest/api/trafficmanager/) před změnou typu směrování na geografickou. Pokud používáte portál, nejprve tyto koncové body odstraňte, změňte metodu směrování profilu na geografickou a přidejte koncové body spolu s jejich mapováním na geografickou oblast.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Proč doporučujeme, aby zákazníci vytvářeli vnořené profily místo koncových bodů v profilu s povoleným geografickým směrováním?

Oblast se dá přiřadit jenom k jednomu koncovému bodu v profilu, pokud používá metodu geografického směrování. Pokud se tento koncový bod nejedná o vnořený typ s připojeným podřízeným profilem, pokud tento koncový bod není v pořádku, Traffic Manager nadále posílat provoz na něj, protože alternativa k tomu, že neodesílá žádný provoz, není lepší. Traffic Manager neproběhne převzetí služeb při selhání jiným koncovým bodem, a to i v případě, že je přiřazená oblast "nadřazená" oblasti přiřazené ke koncovému bodu, který není v pořádku (například pokud koncový bod s oblastí Španělsko není v pořádku, neproběhne převzetí služeb při selhání do jiného koncového bodu, který má oblast Evropa přiřazenou). K tomu je potřeba zajistit, aby Traffic Manager respektovat geografické hranice, které zákazník má ve svém profilu nastavený. Aby bylo možné převzít služby při selhání do jiného koncového bodu v případě, že koncový bod není v pořádku, doporučujeme, aby byly geografické oblasti přiřazeny k vnořeným profilům s více koncovými body namísto jednotlivých koncových bodů. Tímto způsobem se může provoz převzetí služeb při selhání do jiného koncového bodu v rámci stejného vnořeného podřízeného profilu v případě selhání koncového bodu ve vnořeném podřízeném profilu.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existují nějaká omezení verze rozhraní API, která podporuje tento typ směrování?

Ano, jenom rozhraní API verze 2017-03-01 a novější podporuje typ geografického směrování. Jakékoli starší verze rozhraní API nelze použít k vytvoření profilů geografického typu směrování nebo k přiřazení geografických oblastí koncovým bodům. Pokud se k načtení profilů z předplatného Azure používá starší verze rozhraní API, nevrátí se žádný profil geografického směrování. Při použití starších verzí rozhraní API se navíc u všech vrácených profilů, které mají koncové body s přiřazením geografické oblasti, nezobrazuje příslušné přiřazení geografické oblasti.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metoda směrování provozu Traffic Manager podsíti

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Jaké jsou případy použití, kde je směrování podsítě užitečné?

Směrování podsítě vám umožní odlišit prostředí, které zadáváte, pro konkrétní skupiny uživatelů identifikované zdrojovou IP adresou svých požadavků DNS. V příkladu se zobrazí jiný obsah, pokud se uživatelé připojují k webu z firemního SÍDELu. Druhý by omezil přístup uživatelů z některých poskytovatelů internetových služeb jenom na koncové body, které podporují jenom připojení typu IPv4, pokud mají tyto poskytovatelé služeb při použití IPv6 stejný výkon.
Dalším důvodem pro použití metody směrování podsítí je spojení s dalšími profily ve vnořené sadě profilů. Pokud například chcete použít metodu geografického směrování pro geografickou oplocení uživatelů, ale u konkrétního poskytovatele internetových služeb, u kterého chcete provést jinou metodu směrování, můžete mít profil s metodou směrování podsítě jako nadřazený profil a přepsat tohoto poskytovatele internetových služeb, aby používal konkrétní podřízený profil a měl standardní zeměpisný profil pro všechny ostatní.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Jak Traffic Manager znát IP adresu koncového uživatele?

Zařízení koncových uživatelů obvykle používají Překladač DNS k provádění vyhledávání DNS jménem. Odchozí IP adresa takového překladače je to, co Traffic Manager uvidí jako zdrojová IP adresa. Kromě toho metoda směrování podsítě také zjistí, jestli jsou informace o podsíti (ECS) rozšíření EDNS0, které se předaly s požadavkem. Pokud jsou k dispozici informace služby ECS, jedná se o adresu použitou k určení směrování. Při absenci informací služby ECS se zdrojová IP adresa dotazu používá pro účely směrování.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak lze při používání směrování podsítí zadat IP adresy?

IP adresy, které se mají přidružit ke koncovému bodu, je možné zadat dvěma způsoby. Nejprve můžete použít Desítkový zápis desítkových teček s počátečními a koncovými adresami k určení rozsahu (například 1.2.3.4-5.6.7.8 nebo 3.4.5.6-3.4.5.6). Za druhé můžete použít zápis CIDR k určení rozsahu (například 1.2.3.0/24). Můžete zadat více rozsahů a použít oba typy zápisu v sadě rozsahu. Platí několik omezení.

-    Nemůžete mít překrývající se rozsahy adres, protože jednotlivé IP adresy musí být namapované jenom na jeden koncový bod.
-    Počáteční adresa nemůže být větší než koncová adresa.
-    V případě zápisu CIDR by měla být IP adresa před znakem "/" počáteční adresou tohoto rozsahu (například 1.2.3.0/24 je platná, ale 1.2.3.4.4/24 není platná).

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak můžu při používání směrování podsítí zadat záložní koncový bod?

Pokud máte v profilu s směrováním podsítí k dispozici koncový bod, na který se nemapují žádné podsítě, všechny požadavky, které se neshodují s jinými koncovými body, se přesměrují sem. Důrazně doporučujeme, abyste ve svém profilu měli takový záložní koncový bod, protože Traffic Manager vrátí odpověď NXDOMAIN v případě, že žádost přichází a není namapovaná na žádné koncové body, nebo pokud je namapovaná na koncový bod, ale koncový bod není v pořádku.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co se stane, když je v profilu typu směrování podsítě zakázán koncový bod?

Pokud máte v profilu se směrováním podsítě, v případě, že máte koncový bod, který je zakázaný, se Traffic Manager chová, jako by tento koncový bod a mapování podsítě neexistovaly. Pokud se přijme dotaz, který by odpovídal mapování IP adresy, a koncový bod je zakázaný, Traffic Manager vrátí záložní koncový bod (jeden bez mapování), nebo pokud takový koncový bod není k dispozici, vrátí odpověď NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Metoda směrování provozu Traffic Manager s více hodnotami

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Jaké jsou případy použití, ve kterých je vhodné použít směrování s více hodnotami?

Vícenásobné směrování v jedné odpovědi na dotaz vrátí několik zdravých koncových bodů. Hlavní výhodou je, že pokud koncový bod není v pořádku, má klient více možností pro opakování bez dalšího volání DNS (což může vracet stejnou hodnotu z nadřazené mezipaměti). Tento postup se týká dostupnosti citlivých aplikací, které chtějí minimalizovat prostoje.
Další možností použití metody směrování s více hodnotami je, že pokud je koncový bod "duálním" připojením "na adresy IPv4 i IPv6 a chcete volajícímu povolit obě možnosti výběru při inicializaci připojení ke koncovému bodu.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Kolik koncových bodů se vrátí při použití směrování s více hodnotami?

Můžete zadat maximální počet koncových bodů, které mají být vráceny, a při přijetí dotazu nevrátí více než tento počet funkčních koncových bodů. Maximální možná hodnota této konfigurace je 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Získá se stejná sada koncových bodů, když se použije směrování s více hodnotami?

Nemůžeme zaručit, že se v každém dotazu vrátí stejná sada koncových bodů. To je ovlivněno i tím, že některé koncové body můžou být v nesprávném stavu, v jakém okamžiku nebudou zahrnuty do odpovědi.

## <a name="real-user-measurements"></a>Měření reálných uživatelů

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jaké jsou výhody použití Měření reálných uživatelů?

Při použití metody směrování výkonu Traffic Manager vybere nejlepší oblast Azure, ke které se má koncový uživatel připojit, a to tak, že zkontroluje zdrojovou podsíť IP a EDNS klienta (Pokud je předaná) a zkontroluje se s tím, že služba udržuje informace o latenci sítě. Měření reálných uživatelů tuto možnost vylepšit pro vaši základnu koncových uživatelů tím, že jejich zkušenost přispívá k této tabulce latence a zároveň zajišťuje, aby tato tabulka byla vhodně rozložená na sítě koncových uživatelů, ze kterých se koncoví uživatelé připojují k Azure. To vede k vyšší přesnosti při směrování koncového uživatele.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Můžu Měření reálných uživatelů používat s oblastmi mimo Azure?

Měření reálných uživatelů míry a oznamuje pouze latenci pro dosažení oblastí Azure. Pokud používáte směrování na základě výkonu s koncovými body hostovanými v oblastech mimo Azure, můžete tuto funkci i nadále využívat tím, že získáte informace o latenci týkající se zástupce oblasti Azure, kterou jste vybrali k přidružení k tomuto koncovému bodu.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Jakou metodu směrování přináší výhody Měření reálných uživatelů?

Další informace získané prostřednictvím Měření reálných uživatelů platí pouze pro profily, které používají metodu směrování výkonu. Odkaz Měření reálných uživatelů je při prohlížení přes Azure Portal dostupný ze všech profilů.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Potřebuji Měření reálných uživatelů každý profil samostatně?

Ne, stačí ho pro každé předplatné povolit a všechny informace o latenci měřené a hlášené jsou k dispozici pro všechny profily.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Návody vypnout Měření reálných uživatelů pro moje předplatné?

Pokud zastavíte shromažďování a odesílání měření latence z klientské aplikace, můžete zastavit účtování poplatků souvisejících s Měření reálných uživatelů. Například při měření JavaScriptu vloženého na webové stránky můžete zastavit používání této funkce odebráním JavaScriptu nebo vypnutím volání při vykreslování stránky.

Můžete také vypnout Měření reálných uživatelů odstraněním klíče. Po odstranění klíče se zahodí všechna měření poslaná do Traffic Manager s tímto klíčem.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Můžu Měření reálných uživatelů použít s klientskými aplikacemi jinými než webovými stránkami?

Ano, Měření reálných uživatelů je navržená tak, aby ingestoval data shromážděná prostřednictvím různých typů klientů koncových uživatelů. Tyto nejčastější dotazy budou aktualizovány, protože jsou podporovány nové typy klientských aplikací.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Kolik měření se provádí pokaždé, když se vykreslí webová stránka s povoleným Měření reálných uživatelů?

Když se Měření reálných uživatelů používá s dodaným jazykem JavaScript měření, každé vykreslení stránky má za následek 6 měření. Ty se pak nahlásí zpátky do služby Traffic Manager. Tato funkce se vám bude účtovat na základě počtu měření hlášených Traffic Manager službě. Například pokud uživatel přejde pryč z vaší webové stránky v době, kdy byla provedena měření, ale před jeho Nahlášením, měření se nepovažují za účely fakturace.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Je zpoždění před spuštěním skriptu Měření reálných uživatelů na naší webové stránce?

Ne, před vyvoláním skriptu neexistuje žádné programové zpoždění.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Můžu použít Měření reálných uživatelů jenom v oblastech Azure, které chci změřit?

Ne, při každém vyvolání skript Měření reálných uživatelů měří sadu šesti oblastí Azure podle určení služby. Tato sada se mění mezi různými vyvoláními a když dojde k velkému počtu takových vyvolání, pokrytí měření se rozpíná napříč různými oblastmi Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Můžu omezit počet provedených měření na konkrétní číslo?

Měření JavaScriptu je vložené na vaší webové stránce a vy budete mít úplnou kontrolu nad tím, kdy se má spustit a jak ho používat. Pokud služba Traffic Manager obdrží požadavek na seznam oblastí Azure, které se mají změřit, vrátí se sada oblastí.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Můžu zobrazit měření prováděná klientskou aplikací jako součást Měření reálných uživatelů?

Vzhledem k tomu, že se logika měření spouští z klientské aplikace, máte plnou kontrolu nad tím, co se děje, včetně zobrazení latencí. Traffic Manager neoznamuje agregované zobrazení měření přijatých pod klíčem připojeným k vašemu předplatnému.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Můžu změnit skript měření, který poskytuje Traffic Manager?

I když ovládáte, co je na webové stránce vložené, důrazně vám nemůžeme dělat změny ve skriptu měření, abyste zajistili, že měří a hlásí správně latence.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Bude možné, že ostatní uživatelé uvidí klíč, který používá Měření reálných uživatelů?

Při vložení skriptu měření na webovou stránku bude možné, aby ostatní viděli skript a klíč Měření reálných uživatelů (RUM). Je ale důležité znát, že tento klíč se liší od ID předplatného a je vygenerovaný Traffic Manager, který se má použít jenom pro tento účel. Znalost klíče rumu neohrožuje zabezpečení vašich účtů Azure.

### <a name="can-others-abuse-my-rum-key"></a>Můžou jiné zneužít svůj klíč rumu?

I když je možné, že ostatní používají váš klíč k odesílání chybných informací do Azure, několik špatných měření nebude směrování měnit, protože se bere v úvahu spolu se všemi ostatními měřeními, které obdržíme. Pokud potřebujete změnit klíče, můžete znovu vygenerovat klíč, ve kterém se starý klíč zahodí.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Potřebuji do všech mých webových stránek přidat měření JavaScriptu?

Měření reálných uživatelů poskytuje větší hodnotu jako počet zvýšení počtu měření. V takovém případě je to vaše rozhodnutí, jestli je potřeba je umístit na všechny webové stránky nebo vybrat několik. Naše doporučení je potřeba začít tím, že ho umístíte na navštívenou stránku, kde se očekává, že na této stránce zůstane na stránce pět sekund nebo déle.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Můžou se informace o mých koncových uživatelích identifikovat pomocí Traffic Manager, když používám Měření reálných uživatelů?

Když se použije poskytnutá jednotka JavaScript, Traffic Manager bude mít přehled o IP adrese klienta koncového uživatele a zdrojové IP adrese, které používají Překladač DNS. Traffic Manager používá IP adresu klienta jenom poté, co se zkrátí, aby nedokázal identifikovat konkrétního koncového uživatele, který měření poslal.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Musí webová stránka měřící Měření reálných uživatelů používat Traffic Manager pro směrování?

Ne, nemusí používat Traffic Manager. Strana směrování Traffic Manager pracuje nezávisle na části měření reálného uživatele, i když je to skvělé, aby je bylo možné mít ve stejné webové vlastnosti, nemusí být.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Potřebuji pro použití s Měření reálných uživatelů hostovat jakoukoli službu v oblastech Azure?

Ne, nemusíte hostovat žádnou komponentu na straně serveru v Azure, aby mohla Měření reálných uživatelů fungovat. Obrázek v jednom obrazovém pixelu, který stáhla jednotka JavaScript a služba běžící v různých oblastech Azure, je hostovaný a spravovaný pomocí Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Zvýší se využití šířky pásma Azure při použití Měření reálných uživatelů?

Jak je uvedeno v předchozí odpovědi, komponenty na straně serveru služby Měření reálných uživatelů jsou vlastněné a spravované pomocí Azure. To znamená, že využití šířky pásma Azure se nezvýší, protože používáte Měření reálných uživatelů. Nezahrnuje použití šířky pásma mimo jiné poplatky za Azure. Šířku pásma, kterou používáme, minimalizujete tak, že stáhnete jenom jeden obrázek v pixelech, abyste zjistili latenci v oblasti Azure. 

## <a name="traffic-view"></a>Zobrazení přenosů

### <a name="what-does-traffic-view-do"></a>Co Zobrazení přenosů udělat?

Zobrazení přenosů je funkce Traffic Manager, která vám pomůže pochopit Další informace o vašich uživatelích a jejich možnosti. Používá dotazy přijaté Traffic Manager a tabulky informací o latenci sítě, které služba udržuje, a poskytuje následující:

- Oblasti, ze kterých se vaši uživatelé připojují k koncovým bodům v Azure.
- Objem uživatelů připojující z těchto oblastí.
- Oblasti Azure, do kterých jsou směrovány.
- Možnosti jejich latence pro tyto oblasti Azure.

Tyto informace můžete využít k tomu, abyste na portálu mohli využívat i tabulková zobrazení pro překrytí geografických map a tabulková zobrazení, aby bylo možné je stáhnout jako neupravená data.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak můžu využít Zobrazení přenosů?

Zobrazení přenosů poskytuje celkový přehled o provozu, který profily Traffic Manager obdrží. Konkrétně se dá použít k pochopení, kde se vaše uživatelská základna připojuje, a stejně tak důležité, co je jejich průměrná latence. Tyto informace pak můžete použít k vyhledání oblastí, ve kterých se musíte zaměřit, například rozšiřováním nároků Azure na oblast, která může těmto uživatelům poskytnout nižší latenci. Dalším přehledem, který můžete odvodit z použití Zobrazení přenosů, je zobrazení vzorců provozu do různých oblastí, které vám zase pomůžou při rozhodování o zvyšování nebo zmenšení zásob v těchto oblastech.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Jak se Zobrazení přenosů liší od metrik Traffic Manager dostupných prostřednictvím služby Azure monitor?

Azure Monitor lze použít k pochopení agregované úrovně provozu, který obdrží váš profil a jeho koncových bodů. Umožňuje také sledovat stav koncových bodů tím, že zveřejňuje výsledky kontroly stavu. Pokud potřebujete jít nad rámec těchto informací a pochopit, jak se koncoví uživatelé připojují k Azure na regionální úrovni, můžete k tomu použít Zobrazení přenosů.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Používá Zobrazení přenosů informace o podsíti klienta EDNS?

Dotazy DNS obsluhované službou Azure Traffic Manager považují informace služby ECS za účelem zvýšení přesnosti směrování. Když ale vytváříte datovou sadu, která zobrazuje, odkud se uživatelé připojují, Zobrazení přenosů používá jenom IP adresu překladače DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Kolik dní dat Zobrazení přenosů použít?

Zobrazení přenosů vytvoří svůj výstup zpracováním dat ze sedmi dnů předcházejících dni před jeho zobrazením. Toto je přesunutí okna a při každém návštěvě se použijí nejnovější data.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak Zobrazení přenosů zpracovávat externí koncové body?

Když použijete externí koncové body hostované mimo oblasti Azure v Traffic Manager Profile, můžete si vybrat, že se má namapovat na oblast Azure, která je proxy serverem pro jeho charakteristiky latence (ve skutečnosti je to potřeba, pokud používáte metodu směrování výkonu). Pokud má toto mapování oblasti Azure, při vytváření výstupu Zobrazení přenosů se použijí metriky latence oblasti Azure. Pokud není zadaná žádná oblast Azure, informace o latenci budou v datech pro tyto externí koncové body prázdné.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Potřebuji povolit Zobrazení přenosů pro každý profil v předplatném?

Během období Preview bylo Zobrazení přenosů povolené na úrovni předplatného. V rámci vylepšení, které jsme provedli před všeobecnou dostupností, teď můžete povolit Zobrazení přenosů na úrovni profilu, což vám umožní podrobnější povolení této funkce. Ve výchozím nastavení se Zobrazení přenosů pro profil zakáže.

>[!NOTE]
>Pokud jste povolili Zobrazení přenosů na úrovni předplatného během období Preview, je teď potřeba ho znovu povolit pro každý profil v rámci tohoto předplatného.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak můžu Zobrazení přenosů vypnout?

Zobrazení přenosů pro libovolný profil můžete vypnout pomocí portálu nebo REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Jak funguje Zobrazení přenosů fakturace?

Ceny Zobrazení přenosů jsou založené na počtu datových bodů použitých k vytvoření výstupu. V současné době je jediným podporovaným datovým typem dotazy, které váš profil obdrží. Kromě toho se vám bude účtovat pouze zpracování, které bylo provedeno, když máte Zobrazení přenosů povoleno. To znamená, že pokud povolíte Zobrazení přenosů v určitém časovém období v měsíci a vypnete ho v jinou dobu, zpracují se jenom datové body, které se zpracovaly v době, kdy jste vystavili povolený počet funkcí.

## <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Můžu použít Traffic Manager s koncovými body z více předplatných?

Použití koncových bodů z více předplatných není možné u Azure Web Apps. Azure Web Apps vyžaduje, aby se vlastní název domény používaný pro Web Apps používal jenom v rámci jednoho předplatného. Není možné použít Web Apps z více předplatných se stejným názvem domény.

U ostatních typů koncových bodů je možné použít Traffic Manager s koncovými body z více než jednoho předplatného. V Správce prostředků se koncové body z jakéhokoli předplatného dají přidat do Traffic Manager, pokud osoba, která konfiguruje profil Traffic Manager, má ke koncovému bodu oprávnění ke čtení. Tato oprávnění se dají udělit pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Koncové body z jiných předplatných je možné přidat pomocí [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Můžu použít Traffic Manager s testovacími sloty cloudové služby?

Ano. Sloty "fázování" cloudové služby je možné nakonfigurovat v Traffic Manager jako externí koncové body. Kontrolám stavu se pořád účtuje sazba Koncové body Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Podporuje Traffic Manager koncové body IPv6?

Traffic Manager aktuálně neposkytují názvové servery IPv6-Addressing. Traffic Manager je však stále možné používat klienty IPv6 připojujícími se k koncovým bodům IPv6. Klient neprovádí požadavky DNS přímo na Traffic Manager. Místo toho klient používá rekurzivní službu DNS. Klient jenom s protokolem IPv6 odesílá požadavky do rekurzivní služby DNS přes IPv6. Rekurzivní služba by měla být schopná kontaktovat Traffic Manager názvové servery pomocí protokolu IPv4.

Traffic Manager odpoví názvem DNS nebo IP adresou koncového bodu. Pro podporu koncového bodu IPv6 jsou k dispozici dvě možnosti. Koncový bod můžete přidat jako název DNS, který má přidružený záznam AAAA a Traffic Manager bude mít za následek kontrolu stavu tohoto koncového bodu a vrátí ho jako typ záznamu CNAME v odpovědi na dotaz. Tento koncový bod můžete přidat také přímo pomocí adresy IPv6 a Traffic Manager v odpovědi na dotaz vrátí záznam typu AAAA.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Můžu použít Traffic Manager s více než jednou webovou aplikací ve stejné oblasti?

Traffic Manager se obvykle používá k směrování provozu do aplikací nasazených v různých oblastech. Dá se ale použít i v případě, že aplikace má ve stejné oblasti více než jedno nasazení. Traffic Manager koncových bodů Azure nepovoluje přidání více než jednoho koncového bodu webové aplikace ze stejné oblasti Azure do stejného Traffic Manager profilu.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Návody přesunout koncové body Azure profilu Traffic Manager do jiné skupiny prostředků nebo předplatného?

Koncové body Azure přidružené k profilu Traffic Manager jsou sledovány pomocí jejich ID prostředků. Pokud se prostředek Azure, který se používá jako koncový bod (například veřejná IP adresa, klasický cloudová služba, WebApp nebo jiný profil Traffic Manager), přesune do jiné skupiny prostředků nebo předplatného, změní se jeho ID. V tomto scénáři je potřeba aktualizovat profil Traffic Manager tím, že nejprve odstraníte a pak přidáte zpátky koncové body do profilu.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Je Traffic Manager odolné vůči selhání oblasti Azure?

Traffic Manager je klíčovou součástí doručování vysoce dostupných aplikací v Azure.
Aby byla zajištěna vysoká dostupnost, Traffic Manager musí mít mimořádně vysokou úroveň dostupnosti a být odolná vůči místnímu selhání.

Díky navrhování Traffic Manager komponenty jsou odolné proti kompletnímu selhání jakékoli oblasti Azure. Tato odolnost se vztahuje na všechny součásti Traffic Manager: názvové servery DNS, rozhraní API, vrstva úložiště a služba monitorování koncového bodu.

V nepravděpodobném případě výpadku celé oblasti Azure očekává Traffic Manager, že bude fungovat normálně. Aplikace nasazené ve více oblastech Azure se můžou spoléhat na Traffic Manager k přímému přenosu dat do dostupné instance jejich aplikace.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak Volba umístění skupiny prostředků ovlivní Traffic Manager?

Traffic Manager je jediná globální služba. Nejedná se o oblast. Volba umístění skupiny prostředků nemá žádný rozdíl na Traffic Manager profily nasazené v této skupině prostředků.

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určovaly umístění, které určuje výchozí umístění pro prostředky nasazené v této skupině prostředků. Když vytvoříte profil Traffic Manager, vytvoří se ve skupině prostředků. Všechny profily Traffic Manager jako jejich umístění používají **globální** nastavení skupiny prostředků.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Návody zjistit aktuální stav každého koncového bodu?

Aktuální stav monitorování každého koncového bodu (kromě celkového profilu) se zobrazí v Azure Portal. Tyto informace jsou také k dispozici prostřednictvím [REST API](/rest/api/trafficmanager/)monitorování provozu, [rutin prostředí PowerShell](/powershell/module/az.trafficmanager)a [rozhraní příkazového řádku Azure CLI pro různé platformy](/cli/azure/install-classic-cli).

Můžete také použít Azure Monitor ke sledování stavu koncových bodů a k zobrazení vizuální reprezentace. Další informace o používání Azure Monitor najdete v [dokumentaci ke službě Azure Monitoring](../azure-monitor/data-platform.md).

### <a name="can-i-monitor-https-endpoints"></a>Můžu monitorovat koncové body HTTPS?

Ano. Traffic Manager podporuje zjišťování prostřednictvím protokolu HTTPS. Nakonfigurujte **https** jako protokol v konfiguraci monitorování.

Traffic Manager nemůže poskytnout žádné ověření certifikátu, včetně:

* Certifikáty na straně serveru nejsou ověřené.
* SNI certifikátů na straně serveru nejsou ověřené.
* Klientské certifikáty nejsou podporovány.

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Používá se při přidávání koncového bodu IP adresa nebo název DNS?

Traffic Manager podporuje přidávání koncových bodů pomocí tří způsobů, jak je odkazovat – jako název DNS jako adresa IPv4 a jako adresa IPv6. Pokud je koncový bod přidán jako adresa IPv4 nebo IPv6, odpověď na dotaz bude typu záznamu A nebo AAAA (v uvedeném pořadí). Pokud byl koncový bod přidán jako název DNS, bude odpověď dotazu typu záznam CNAME. Přidávání koncových bodů jako adresy IPv4 nebo IPv6 je povolené jenom v případě, že je koncový bod typu **externí**.
Všechny metody směrování a nastavení monitorování podporují tři typy adresování koncových bodů.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jaké typy IP adres můžu použít při přidávání koncového bodu?

Traffic Manager umožňuje zadat koncové body pomocí adres IPv4 nebo IPv6. Níže jsou uvedena některá omezení:

- Adresy, které odpovídají vyhrazeným adresním prostorům privátních IP adres, nejsou povoleny. Tyto adresy zahrnují ty, které se vyvolaly v RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 a RFC 5771.
- Adresa nesmí obsahovat žádná čísla portů (můžete zadat porty, které se mají použít v nastavení konfigurace profilu).
- Žádné dva koncové body ve stejném profilu nemohou mít stejnou cílovou IP adresu.

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Můžu v jednom profilu použít jiné typy adresování koncových bodů?

Ne, Traffic Manager neumožňuje kombinovat typy adresování koncových bodů v rámci profilu, s výjimkou případu, kdy je profil s typem směrování s více hodnotami, kde můžete kombinovat typy adres IPv4 a IPv6.

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co se stane, když se typ záznamu příchozího dotazu liší od typu záznamu přidruženého k typu adresování koncových bodů?

Když se na profil přijme dotaz, Traffic Manager nejdřív najde koncový bod, který se musí vrátit podle zadané metody směrování a stavu koncových bodů. Potom se podívá na typ záznamu požadovaný v příchozím dotazu a typ záznamu, který je přidružený ke koncovému bodu, a teprve potom vrátí odpověď na základě níže uvedené tabulky.

Pro profily s jinou metodou směrování než s více hodnotami:

|Příchozí žádost o dotaz|     Typ koncového bodu|     Poskytnutá odpověď|
|--|--|--|
|JAKÝMIKOLI |    A/AAAA/CNAME |    Cílový koncový bod| 
|A |    A/CNAME |    Cílový koncový bod|
|A |    AAAA |    DATA |
|AAAA |    AAAA/CNAME |    Cílový koncový bod|
|AAAA |    A |    DATA |
|CNAME |    CNAME |    Cílový koncový bod|
|CNAME     |A/AAAA |    DATA |
|

Pro profily s metodou směrování nastavenou na hodnotu vícehodnotové:

|Příchozí žádost o dotaz|     Typ koncového bodu |    Poskytnutá odpověď|
|--|--|--|
|JAKÝMIKOLI |    Kombinace a a AAAA |    Cílové koncové body|
|A |    Kombinace a a AAAA |    Pouze cílové koncové body typu A|
|AAAA    |Kombinace a a AAAA|     Pouze cílové koncové body typu AAAA|
|CNAME |    Kombinace a a AAAA |    DATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Můžu použít profil s adresovánými koncovými body IPv4/IPv6 ve vnořeném profilu?

Ano, můžete s výjimkou, že profil s hodnotou typu vícehodnotové nemůže být nadřazeným profilem ve vnořené sadě profilů.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Ukončil (a) jsem koncový bod webové aplikace v profilu Traffic Manager, ale nepřijímám žádný provoz ani po jeho restartování. Jak mohu tuto situaci opravit?

Když je koncový bod webové aplikace Azure zastavený, Traffic Manager zastaví kontrolu jeho stavu a restartuje kontroly stavu až po zjištění, že byl koncový bod restartován. Chcete-li zabránit této prodlevě, zakažte a znovu povolte tento koncový bod v profilu Traffic Manager po restartování koncového bodu.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Můžu použít Traffic Manager i v případě, že moje aplikace nepodporuje protokol HTTP nebo HTTPS?

Ano. Jako monitorovací protokol můžete zadat TCP a Traffic Manager může iniciovat připojení TCP a čekat na odpověď z koncového bodu. Pokud koncový bod odpoví na požadavek připojení s odpovědí na navázání připojení, v rámci časového limitu je tento koncový bod označený jako dobrý.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jaké konkrétní odpovědi z koncového bodu vyžadují při použití monitorování protokolu TCP?

Když se použije monitorování protokolu TCP, Traffic Manager spustí třícestný protokol TCP handshake odesláním žádosti SYN do koncového bodu na zadaném portu. Pak počká na odpověď SYN-ACK z koncového bodu po určitou dobu (zadanou v nastavení časového limitu).

- Pokud je odpověď SYN-ACK přijata v rámci časového limitu zadaného v nastavení monitorování, pak je tento koncový bod považován za dobrý. FIN nebo FIN-ACK je očekávaná odpověď z Traffic Manager, když pravidelně ukončuje soket.
- Pokud je po uplynutí zadaného časového limitu přijata odpověď SYN-ACK, Traffic Manager bude při resetování připojení reagovat s parametrem RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak rychle Traffic Manager přesunout uživatele z poškozeného koncového bodu?

Traffic Manager poskytuje několik nastavení, která vám pomůžou řídit chování při převzetí služeb při selhání profilu Traffic Manager následujícím způsobem:

- můžete určit, že se v Traffic Manager budou koncové body pokládat častěji nastavením intervalu zjišťování na 10 sekund. Tím se zajistí, že libovolný koncový bod, který není v pořádku, bude možné zjistit co nejdříve. 
- můžete určit, jak dlouho se má čekat před vypršením časového limitu požadavku na kontrolu stavu (minimální hodnota časového limitu je 5 sekund).
- můžete určit, kolik selhání může dojít, než je koncový bod označený jako není v pořádku. Tato hodnota může být menší než 0. v takovém případě je koncový bod označen jako není v pořádku, protože při tom dojde k chybě při první kontrole stavu. Použití minimální hodnoty 0 pro tolerovatcí počet selhání může ale vést k tomu, že se koncovým bodům vyčerpá koncové body z důvodu jakýchkoli přechodných problémů, ke kterým může dojít v době zjišťování.
- můžete zadat hodnotu TTL (Time to Live) pro odpověď DNS, aby byla menší než 0. To znamená, že překladače DNS nemůžou odpověď ukládat do mezipaměti a každý nový dotaz dostane odpověď, která zahrnuje nejaktuálnější informace o stavu, které Traffic Manager.

Pomocí těchto nastavení Traffic Manager může poskytnout převzetí služeb při selhání za 10 sekund po nesprávném ukončení koncového bodu a provede se dotaz DNS na odpovídajícím profilu.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak lze zadat různá nastavení monitorování pro různé koncové body v profilu?

Nastavení monitorování Traffic Manager se nachází na úrovni profilu. Pokud potřebujete použít jiné nastavení monitorování jenom pro jeden koncový bod, můžete to udělat tak, že tento koncový bod nastavíte jako [vnořený profil](traffic-manager-nested-profiles.md) , jehož nastavení monitorování se liší od nadřazeného profilu.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak mohu přiřadit hlavičky HTTP ke kontrolám Traffic Manager stavu do mých koncových bodů?

Traffic Manager umožňuje zadat vlastní hlavičky v rámci kontrol stavu HTTP (S), které zahájí vaše koncové body. Pokud chcete zadat vlastní hlavičku, můžete to udělat na úrovni profilu (platí pro všechny koncové body) nebo zadat na úrovni koncového bodu. Pokud je hlavička definovaná na obou úrovních, pak ta, která je určená na úrovni koncového bodu, přepíše úroveň profilu jedna.
Jedním z běžných případů použití pro toto je zadání hlaviček hostitele, aby se Traffic Manager požadavky mohly správně směrovat do koncového bodu hostovaného ve více tenantů prostředí. Dalším případem použití tohoto řešení je identifikovat Traffic Manager požadavky z protokolů požadavků HTTP (S) koncového bodu.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakou hlavičku hostitele používají kontroly stavu koncových bodů?

Pokud není k dispozici žádné vlastní nastavení záhlaví hostitele, bude Hlavička hostitele používaná v Traffic Manager název DNS cíle koncového bodu nakonfigurovaného v profilu, pokud je k dispozici.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jaké jsou IP adresy, ze kterých pocházejí kontroly stavu?

Kliknutím [sem](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) zobrazíte soubor JSON, který obsahuje seznam IP adres, ze kterých můžou nacházet Traffic Manager kontroly stavu. Zkontrolujte IP adresy uvedené v souboru JSON, abyste zajistili, že příchozí připojení z těchto IP adres jsou v koncových bodech povolená pro kontrolu stavu.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Kolik kontrol stavu do mého koncového bodu je možné očekávat od Traffic Manager?

Počet kontrol Traffic Manager stavu, které dosáhnou svého koncového bodu, závisí na následujícím:

- hodnota, kterou jste nastavili pro interval monitorování (menší interval) znamená více požadavků, které jsou v daném časovém intervalu vypsány v daném časovém období.
- počet umístění, z nichž pocházejí kontroly stavu (IP adresy, ze kterých můžete očekávat tyto kontroly, jsou uvedené v předchozích nejčastějších dotazech).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak získám oznámení, pokud se některý z mých koncových bodů přestane nastavovat?

Jedna z metrik poskytovaných nástrojem Traffic Manager je stavem koncových bodů v profilu. Tohle vidíte jako agregaci všech koncových bodů v profilu (například 75% vašich koncových bodů je v pořádku) nebo na úrovni jednotlivých koncových bodů. Traffic Manager metriky jsou zpřístupněny prostřednictvím Azure Monitor a pomocí [možností upozorňování](../azure-monitor/alerts/alerts-metric.md) můžete dostávat oznámení, když dojde ke změně stavu koncového bodu. Další podrobnosti najdete v tématu [Traffic Manager metrik a upozornění](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager vnořené profily

### <a name="how-do-i-configure-nested-profiles"></a>Návody nakonfigurovat vnořené profily?

Vnořené Traffic Manager profily se dají konfigurovat pomocí Azure Resource Manager a klasických rozhraní Azure REST API, Azure PowerShell rutiny a příkazy Azure CLI pro různé platformy. Jsou podporovány také pomocí nového Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Kolik vrstev vnoření podporuje Traffic Manager?

Profily můžete vnořovat až do 10 úrovní. ' Smyčky ' nejsou povoleny.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Můžu ve stejném profilu Traffic Manager kombinovat jiné typy koncových bodů s vnořenými podřízenými profily?

Ano. Neexistují žádná omezení způsobu kombinování koncových bodů různých typů v rámci profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak model fakturace platí pro vnořené profily?

Použití vnořených profilů nemá žádný negativní dopad na ceny.

Traffic Manager fakturace má dvě komponenty: kontroly stavu koncových bodů a miliony dotazů DNS.

* Kontroly stavu koncových bodů: pro podřízený profil se neúčtují žádné poplatky, pokud je v nadřazeném profilu nakonfigurovaný jako koncový bod. Monitorování koncových bodů v podřízeném profilu se fakturuje obvyklým způsobem.
* Dotazy DNS: každý dotaz se počítá jenom jednou. Dotaz na nadřazený profil, který vrací koncový bod z podřízeného profilu, se počítá pouze s nadřazeným profilem.

Úplné podrobnosti najdete na stránce s [cenami Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Je pro vnořené profily dopad na výkon?

No. Při použití vnořených profilů se nevyskytl dopad na výkon.

Traffic Manager názvové servery přecházejí hierarchii profilu interně při zpracování každého dotazu DNS. Dotaz DNS na nadřazený profil může přijmout odpověď DNS s koncovým bodem z podřízeného profilu. Jeden záznam CNAME se používá bez ohledu na to, jestli používáte jeden profil nebo vnořené profily. Pro každý profil v hierarchii není nutné vytvářet záznam CNAME.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager počítá stav vnořeného koncového bodu v nadřazeném profilu?

Nadřazený profil neprovádí kontroly stavu přímo u podřízeného objektu. Místo toho se k výpočtu celkového stavu podřízeného profilu použijí i stav koncových bodů podřízeného profilu. Tyto informace se šíří v hierarchii vnořeného profilu, aby bylo možné zjistit stav vnořeného koncového bodu. Nadřazený profil používá tento agregovaný stav k určení, zda lze provoz směrovat na podřízený uzel.

Následující tabulka popisuje chování Traffic Managerch kontrol stavu pro vnořený koncový bod.

| Stav monitoru pro podřízený profil | Stav monitorování nadřazeného koncového bodu | Poznámky |
| --- | --- | --- |
| Zakázáno Podřízený profil byl zakázán. |Zastaveno |Stav nadřazeného koncového bodu je zastaven, není zakázán. Zakázaný stav je rezervovaný pro indikaci, že jste v nadřazeném profilu zakázali koncový bod. |
| Degradovaný. Nejméně jeden koncový bod podřízeného profilu je v neomezeném stavu. |Online: počet online koncových bodů v podřízeném profilu je aspoň hodnota MinChildEndpoints.<BR>CheckingEndpoint: počet koncových bodů online plus CheckingEndpoint v podřízeném profilu je alespoň hodnota MinChildEndpoints.<BR>Snížený výkon: jinak. |Provoz se směruje na koncový bod stavového CheckingEndpoint. Pokud je nastavení MinChildEndpoints příliš vysoké, koncový bod je vždy snížený. |
| Online. Nejméně jeden koncový bod podřízeného profilu je online stav. V neomezeném stavu není žádný koncový bod. |Viz výše. | |
| CheckingEndpoints. Aspoň jeden podřízený koncový bod profilu je "CheckingEndpoint". Žádné koncové body nejsou online nebo jsou degradovány. |Platí to samé jako výše. | |
| Termín. Všechny koncové body podřízeného profilu jsou buď zakázány, nebo zastaveny, nebo tento profil nemá žádné koncové body. |Zastaveno | |

## <a name="next-steps"></a>Další kroky:

- Další informace o Traffic Manager [monitorování koncového bodu a automatické převzetí služeb při selhání](../traffic-manager/traffic-manager-monitoring.md).
- Přečtěte si další informace o [metodách směrování provozu](../traffic-manager/traffic-manager-routing-methods.md)Traffic Manager.