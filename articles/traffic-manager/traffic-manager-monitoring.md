---
title: Sledování koncového bodu Azure Traffic Manageru | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Traffic Manager používá monitorování koncových bodů a automatické převzetí služeb při selhání koncového bodu k tomu, aby zákazníkům Azure pomohl nasadit aplikace s vysokou dostupností.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: 61aafbe8cb12e93d72f5efd01155f06fb3ec0c28
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757257"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

Azure Traffic Manager zahrnuje integrované monitorování koncového bodu a automatické převzetí služeb při selhání koncového bodu. Tato funkce vám pomůže poskytovat aplikace s vysokou dostupností, které jsou odolné vůči selhání koncového bodu, včetně selhání oblasti Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurace monitorování koncového bodu

Chcete-li konfigurovat monitorování koncového bodu, musíte v profilu Traffic Manageru zadat následující nastavení:

* **protokolu**. Zvolte HTTP, HTTPS nebo TCP jako protokol, který Traffic Manager používá při zjišťování koncového bodu ke kontrole jeho stavu. Monitorování protokolu HTTPS neověřuje, zda je certifikát TLS/SSL platný – pouze kontroluje, zda je certifikát k dispozici.
* **Přístav**. Zvolte port použitý pro požadavek.
* **Cesta**. Toto nastavení konfigurace je platné pouze pro protokoly HTTP a HTTPS, pro které je vyžadováno určení nastavení cesty. Poskytnutí tohoto nastavení protokolu monitorování Protokolu TCP má za následek chybu. Pro protokol HTTP a HTTPS uveďte relativní cestu a název webové stránky nebo souboru, ke kterému přistupuje monitorování. Lomítko (/) je platná položka pro relativní cestu. Tato hodnota znamená, že soubor je v kořenovém adresáři (výchozí).
* **Vlastní nastavení záhlaví** Toto nastavení konfigurace vám pomůže přidat konkrétní hlavičky HTTP do kontrol stavu, které Traffic Manager odesílá koncovým bodům v rámci profilu. Vlastní záhlaví lze zadat na úrovni profilu, které mají být použitelné pro všechny koncové body v tomto profilu a / nebo na úrovni koncového bodu použitelné pouze pro tento koncový bod. Vlastní záhlaví pro kontrolu stavu koncových bodů v prostředí s více klienty správně směrovány do cíle zadáním hlavičky hostitele. Toto nastavení můžete také použít přidáním jedinečných záhlaví, která lze použít k identifikaci aplikace Traffic Manager, které pocházejí z požadavků HTTP(S), a jejich odlišně je zpracujete. Můžete zadat až osm párů záhlaví:hodnota seprated čárkou. Například "header1:value1,header2:value2". 
* **Očekávané rozsahy čísel stavového kódu** Toto nastavení umožňuje zadat více rozsahů kódu úspěchu ve formátu 200-299, 301-301. Pokud tyto stavové kódy jsou přijaty jako odpověď z koncového bodu při zahájení kontroly stavu, Traffic Manager označí tyto koncové body jako v pořádku. Můžete zadat maximálně 8 rozsahů stavového kódu. Toto nastavení platí pouze pro protokolHTTP a HTTPS a pro všechny koncové body. Toto nastavení je na úrovni profilu Traffic Managera a ve výchozím nastavení je hodnota 200 definována jako kód stavu úspěchu.
* **Interval sondování**. Tato hodnota určuje, jak často je koncový bod kontrolován pro jeho stav z agenta zjišťování Traffic Manager. Zde můžete zadat dvě hodnoty: 30 sekund (normální sondování) a 10 sekund (rychlé sondování). Pokud nejsou k dispozici žádné hodnoty, profil nastaví výchozí hodnotu 30 sekund. Další informace o cenách rychlého zjišťování najdete na stránce [Ceny dopravního manažera.](https://azure.microsoft.com/pricing/details/traffic-manager)
* **Tolerovaný počet poruch**. Tato hodnota určuje, kolik selhání agent a traffic manager zjišťování toleruje před označením tohoto koncového bodu jako není v pořádku. Jeho hodnota může být v rozsahu od 0 do 9. Hodnota 0 znamená, že selhání jednoho monitorování může způsobit, že koncový bod bude označen jako není v pořádku. Pokud není zadána žádná hodnota, použije výchozí hodnotu 3.
* **Časový čas sondy**. Tato vlastnost určuje dobu, po kterou by měl agent zjišťování traffic manageru čekat, než se domnívá, že kontrola selhání při odeslání sondy kontroly stavu do koncového bodu. Pokud je interval sondování nastaven na 30 sekund, můžete nastavit hodnotu časového času mezi 5 a 10 sekundami. Pokud není zadána žádná hodnota, použije výchozí hodnotu 10 sekund. Pokud je interval sondování nastaven na 10 sekund, můžete nastavit hodnotu časového času mezi 5 a 9 sekundami. Pokud není zadána žádná hodnota časového limitu, použije výchozí hodnotu 9 sekund.

    ![Monitorování koncových bodů Traffic Manageru](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Obrázek: Sledování koncového bodu Traffic Manageru**

## <a name="how-endpoint-monitoring-works"></a>Jak funguje monitorování koncových bodů

Pokud je monitorovací protokol nastaven jako HTTP nebo HTTPS, agent zjišťování Traffic Manager provede požadavek GET do koncového bodu pomocí daného protokolu, portu a relativní cesty. Pokud získá zpět odpověď 200 OK nebo některou z odpovědí nakonfigurovaných v **rozsahech očekávaný \*stavový kód**, pak je tento koncový bod považován za v pořádku. Pokud je odpověď jinou hodnotou, nebo pokud není přijata žádná odpověď v zadaném časovém období, pokusí se agent sondování Traffic Manager u přepočtu podle nastavení Tolerovaný počet selhání (pokud je toto nastavení 0). Pokud počet po sobě jdoucích selhání je vyšší než tolerovaný počet selhání nastavení, pak koncový bod je označen jako není v pořádku. 

Pokud je monitorovací protokol TCP, agent zjišťování Traffic Manager zahájí požadavek na připojení TCP pomocí zadaného portu. Pokud koncový bod odpoví na požadavek s odpovědí na navázat připojení, tato kontrola stavu je označena jako úspěch a traffic manager sondování agent obnoví připojení TCP. Pokud je odpověď jinou hodnotou nebo pokud není přijata žádná odpověď v zadaném časovém rámci, pokusí se agent sondování Traffic Manageru znovu podle nastavení Tolerovaný počet selhání (pokud je toto nastavení 0), nejsou provedeny žádné opakované pokusy). Pokud počet po sobě jdoucích selhání je vyšší než tolerovaný počet selhání nastavení, pak koncový bod je označen jako není v pořádku.

Ve všech případech Traffic Manager sondy z více míst a následné stanovení selhání se děje v rámci každé oblasti. To také znamená, že koncové body přijímají sondy stavu z Traffic Manageru s vyšší frekvencí než nastavení používané pro interval zjišťování.

>[!NOTE]
>Pro protokol monitorování HTTP nebo HTTPS je běžnou praxí na straně koncového bodu implementace vlastní stránky v rámci aplikace – například /health.aspx. Pomocí této cesty pro monitorování můžete provádět kontroly specifické pro aplikaci, jako je například kontrola čítačů výkonu nebo ověřování dostupnosti databáze. Na základě těchto vlastních kontrol stránka vrátí odpovídající stavový kód HTTP.

Všechna koncové body v profilu Traffic Manager sdílejí nastavení monitorování. Pokud potřebujete použít různá nastavení monitorování pro různé koncové body, můžete vytvořit [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stav koncového bodu a profilu

Můžete povolit a zakázat profily traffic manageru a koncové body. Ke změně stavu koncového bodu však může dojít také v důsledku automatického nastavení a procesů Traffic Manageru.

### <a name="endpoint-status"></a>Stav koncového bodu

Můžete povolit nebo zakázat konkrétní koncový bod. Základní služba, která může být stále v pořádku, není ovlivněna. Změna stavu koncového bodu řídí dostupnost koncového bodu v profilu Traffic Manageru. Pokud je stav koncového bodu zakázán, Traffic Manager nekontroluje jeho stav a koncový bod není zahrnut v odpovědi DNS.

### <a name="profile-status"></a>Profile status

Pomocí nastavení stavu profilu můžete povolit nebo zakázat konkrétní profil. Zatímco stav koncového bodu ovlivňuje jeden koncový bod, stav profilu ovlivňuje celý profil, včetně všech koncových bodů. Když zakážete profil, koncové body nejsou kontrolovány na stav a žádné koncové body jsou zahrnuty v odpovědi DNS. Pro dotaz DNS je vrácen kód odpovědi [NXDOMAIN.](https://tools.ietf.org/html/rfc2308)

### <a name="endpoint-monitor-status"></a>Stav monitoru koncového bodu

Stav sledování koncového bodu je hodnota generovaná traffic managerem, která zobrazuje stav koncového bodu. Toto nastavení nelze změnit ručně. Stav sledování koncového bodu je kombinací výsledků monitorování koncového bodu a nakonfigurovaného stavu koncového bodu. Možné hodnoty stavu sledování koncového bodu jsou uvedeny v následující tabulce:

| Profile status | Stav koncového bodu | Stav monitoru koncového bodu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |Povoleno |Inactive |Profil byl zakázán. Přestože je stav koncového bodu povolen, má přednost stav profilu (Zakázáno). Koncové body v zakázaných profilech nejsou sledovány. Pro dotaz DNS je vrácen kód odpovědi NXDOMAIN. |
| &lt;libovolné&gt; |Zakázáno |Zakázáno |Koncový bod byl zakázán. Zakázané koncové body nejsou sledovány. Koncový bod není zahrnuta v odpovědích DNS, proto nepřijímá přenosy. |
| Povoleno |Povoleno |Online |Koncový bod je sledován a je v pořádku. Je součástí dns odpovědí a může přijímat provoz. |
| Povoleno |Povoleno |Snížený výkon |Kontroly stavu monitorování koncových bodů selhávají. Koncový bod není součástí odpovědí DNS a nepřijímá přenosy. <br>Výjimkou je, pokud jsou všechny koncové body degradovány, v takovém případě jsou všechny tyto body považovány za vrácené v odpovědi na dotaz).</br>|
| Povoleno |Povoleno |Kontrola koncového bodu |Koncový bod je sledován, ale výsledky první sondy nebyly dosud přijaty. CheckingEndpoint je dočasný stav, který se obvykle vyskytuje bezprostředně po přidání nebo povolení koncového bodu v profilu. Koncový bod v tomto stavu je součástí odpovědí DNS a může přijímat přenosy. |
| Povoleno |Povoleno |Zastaveno |Webová aplikace, na kterou koncový bod odkazuje, není spuštěná. Zkontrolujte nastavení webové aplikace. K tomu může dojít také v případě, že koncový bod je typu vnořený koncový bod a podřízený profil je zakázánnebo je neaktivní. <br>Koncový bod se stavem Zastaveno není sledován. Není zahrnuta v odpovědích DNS a nepřijímá přenosy. Výjimkou je, pokud jsou všechny koncové body degradovány, v takovém případě budou všechny tyto body považovány za vrácené v odpovědi na dotaz.</br>|

Podrobnosti o výpočtu stavu sledování koncového bodu pro vnořené koncové body naleznete v [nosných profilech Traffic Manageru](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stav monitoru zastaveného koncového bodu může nastat ve službě App Service, pokud vaše webová aplikace není spuštěna na úrovni Standard nebo vyšší. Další informace naleznete v [tématu Traffic Manager integrace s App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Stav sledování profilu

Stav monitorování profilu je kombinací stavu nakonfigurovaného profilu a hodnot stavu sledování koncového bodu pro všechny koncové body. Možné hodnoty jsou popsány v následující tabulce:

| Stav profilu (podle konfigurace) | Stav monitoru koncového bodu | Stav sledování profilu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |&lt;nebo&gt; profil bez definovaných koncových bodů. |Zakázáno |Profil byl zakázán. |
| Povoleno |Stav alespoň jednoho koncového bodu je degradován. |Snížený výkon |Zkontrolujte hodnoty stavu jednotlivých koncových bodů a určete, které koncové body vyžadují další pozornost. |
| Povoleno |Stav alespoň jednoho koncového bodu je Online. Žádné koncové body mají stav degradované. |Online |Služba přijímá provoz. Nevyžaduje se žádná další akce. |
| Povoleno |Stav alespoň jednoho koncového bodu je CheckingEndpoint. Žádné koncové body jsou ve stavu Online nebo Degraded. |Kontrola koncových bodů |Tento stav přechodu nastane, když profil, pokud je vytvořen nebo povolen. Stav koncového bodu je kontrolována poprvé. |
| Povoleno |Stavy všech koncových bodů v profilu jsou buď Zakázáno nebo Zastaveno, nebo profil nemá žádné definované koncové body. |Inactive |Žádné koncové body jsou aktivní, ale profil je stále povolena. |

## <a name="endpoint-failover-and-recovery"></a>Převzetí služeb při selhání koncového bodu a obnovení

Traffic Manager pravidelně kontroluje stav každého koncového bodu, včetně koncových bodů není v pořádku. Traffic Manager zjistí, kdy koncový bod se stane v pořádku a přenese jej zpět do rotace.

Koncový bod není v pořádku, pokud dojde k některé z následujících událostí:

- Pokud je monitorovací protokol HTTP nebo HTTPS:
    - Je přijata odpověď bez 200 nebo odpověď, která neobsahuje rozsah stavu zadaný v nastavení **Rozsahy očekávaného stavového kódu** (včetně jiného kódu 2xx nebo přesměrování 301/302).
- Pokud je monitorovací protokol TCP: 
    - Odpověď jiná než ACK nebo SYN-ACK je přijata v reakci na požadavek SYN odeslaný Traffic Manager k pokusu o navázání připojení.
- Časový limit. 
- Jakékoli jiné problémy s připojením, které mají za následek koncový bod není k zastižení.

Další informace o neúspěšných kontrolách při potížích najdete [v tématu Poradce při potížích se sníženým stavem ve Správci provozu Azure](traffic-manager-troubleshooting-degraded.md). 

Časová osa na následujícím obrázku je podrobný popis procesu monitorování koncového bodu Traffic Manager, který má následující nastavení: monitorovací protokol je HTTP, interval zjišťování je 30 sekund, počet tolerovaných selhání je 3, hodnota časového intervalu je 10 sekund a TTL DNS je 30 sekund.

![Sekvence převzetí služeb při selhání a posloupnost navrácení služeb po selhání správce provozu](./media/traffic-manager-monitoring/timeline.png)

**Obrázek: Služba převzetí služeb při selhání správce provozu a posloupnost obnovení**

1. **Získat**. Pro každý koncový bod systém sledování Traffic Manager provádí požadavek GET na cestě zadané v nastavení monitorování.
2. **200 OK nebo vlastní rozsah kódu zadané Traffic Manager nastavení monitorování profilu** . Monitorovací systém očekává, že zpráva o nastavení monitorování profilu správce sítě HTTP 200 OK nebo vlastní rozsah kódu zadaný do 10 sekund. Když obdrží tuto odpověď, rozpozná, že služba je k dispozici.
3. **30 sekund mezi kontrolami**. Kontrola stavu koncového bodu se opakuje každých 30 sekund.
4. **Služba není k dispozici**. Služba přestane být k dispozici. Traffic Manager se to dozví až při další kontrole stavu.
5. **Pokusí se o přístup k cestě monitorování**. Monitorovací systém provede požadavek GET, ale neobdrží odpověď v časovém odnož 10 sekund (alternativně může být přijata odpověď bez 200). To pak se snaží ještě třikrát, v 30 sekundových intervalech. Pokud je jeden z pokusů úspěšný, je počet pokusů obnoven.
6. **Stav nastavený na Degraded**. Po čtvrtém po sobě jdoucí selhání monitorovací systém označí stav nedostupného koncového bodu jako degradovaný.
7. **Přenos je odkloněn do jiných koncových bodů**. Názvové servery DNS služby Traffic Manager jsou aktualizovány a traffic manager již nevrací koncový bod v reakci na dotazy DNS. Nová připojení jsou směrována na jiné dostupné koncové body. Předchozí odpovědi DNS, které obsahují tento koncový bod, však mohou být stále ukládány do mezipaměti rekurzivními servery DNS a klienty DNS. Klienti nadále používají koncový bod, dokud nevyprší platnost mezipaměti DNS. Po vypršení platnosti mezipaměti DNS provádějí klienti nové dotazy DNS a jsou směrováni do různých koncových bodů. Doba trvání mezipaměti je řízena nastavením TTL v profilu Traffic Manager, například 30 sekund.
8. **Zdravotní kontroly pokračují**. Traffic Manager pokračuje ve kontrole stavu koncového bodu, zatímco má stav degradován. Traffic Manager zjistí, kdy se koncový bod vrátí do stavu.
9. **Služba se vrací do režimu online**. Služba bude k dispozici. Koncový bod si zachová svůj stav degradované ve Správci provozu, dokud monitorovací systém neprovede další kontrolu stavu.
10. **Provoz do služby pokračuje**. Traffic Manager odešle požadavek GET a obdrží odpověď na stav 200 OK. Služba se vrátila do stavu v pořádku. Názvové servery Traffic Manageru jsou aktualizovány a začnou rozdávat název DNS služby v odpovědích DNS. Provoz se vrátí do koncového bodu jako odpovědi DNS uložené v mezipaměti, které vracejí další koncové body a jako existující připojení k jiným koncovým bodům jsou ukončeny.

    > [!NOTE]
    > Vzhledem k tomu, že Traffic Manager pracuje na úrovni DNS, nemůže ovlivnit existující připojení k libovolnému koncovému bodu. Pokud směruje provoz mezi koncovými body (buď změnou nastavení profilu, nebo během převzetí služeb při selhání nebo navrácení služeb po obnovení), Traffic Manager směruje nová připojení na dostupné koncové body. Ostatní koncové body však může nadále přijímat přenosy prostřednictvím existujících připojení, dokud tyto relace jsou ukončeny. Chcete-li povolit přenosy k vyčerpání z existujících připojení, aplikace by měly omezit dobu trvání relace používané s každým koncovým bodem.

## <a name="traffic-routing-methods"></a>Metody směrování provozu

Pokud koncový bod má stav degradován, je již vrácena v reakci na dotazy DNS. Místo toho je vybrán alternativní koncový bod a vrácena. Metoda směrování provozu nakonfigurovaná v profilu určuje, jak je vybrán alternativní koncový bod.

* **Priorita**. Koncové body tvoří seznam s prioritou. První dostupný koncový bod v seznamu je vždy vrácena. Pokud je stav koncového bodu degradován, je vrácen další dostupný koncový bod.
* **Vážený**. Všechny dostupné koncové body se volí náhodně na základě přiřazených hmotností a hmotností ostatních dostupných cílových parametrů.
* **Výkon**. Koncový bod nejblíže ke koncovému uživateli je vrácena. Pokud tento koncový bod není k dispozici, Traffic Manager přesune provoz do koncových bodů v nejbližší oblasti Azure. Alternativní plány převzetí služeb při selhání pro směrování provozu výkonu můžete nakonfigurovat pomocí [vnořených profilů traffic manageru](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografické**. Koncový bod mapovaný tak, aby sloužil geografické umístění na základě požadavku na dotaz IP je vrácena. Pokud tento koncový bod není k dispozici, jiný koncový bod nebude vybrán k převzetí služeb při selhání, protože geografické umístění lze mapovat pouze na jeden koncový bod v profilu (další podrobnosti jsou v [faq](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Jako osvědčený postup doporučujeme při použití geografického směrování doporučujeme zákazníkům používat vnořené profily Traffic Manageru s více než jedním koncovým bodem jako koncovými body profilu.
* **Vícehodnot** Je vráceno více koncových bodů mapovaných na adresy IPv4/IPv6. Při přijetí dotazu pro tento profil jsou vráceny koncové body v pořádku na základě **maximálnípočet záznamů v** hodnotě odpovědi, které jste zadali. Výchozí počet odpovědí je dva koncové body.
* **Podsíť** Koncový bod mapovaný na sadu rozsahů IP adres je vrácena. Při přijetí požadavku z této adresy IP je vrácený koncový bod ten, který je mapován pro tuto adresu IP. 

Další informace naleznete v tématu [Traffic Manager traffic-routing methods](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedna výjimka z normálního chování směrování provozu nastane, když všechny způsobilé koncové body mají snížený stav. Traffic Manager provede pokus o "nejlepší úsilí" a *reaguje, jako by všechny koncové body stavu degradované jsou skutečně ve stavu online*. Toto chování je vhodnější než alternativa, která by neměla vrátit žádný koncový bod v odpovědi DNS. Zakázané nebo zastavené koncové body nejsou sledovány, proto nejsou považovány za způsobilé pro provoz.
>
> Tato podmínka je obvykle způsobena nesprávnou konfigurací služby, například:
>
> * Seznam řízení přístupu [ACL] blokuje kontroly stavu Traffic Manager.
> * Nesprávná konfigurace monitorovacího portu nebo protokolu v profilu správce provozu.
>
> Důsledkem tohoto chování je, že pokud kontroly stavu Traffic Manager nejsou správně nakonfigurovány, může se zobrazit z směrování provozu, jako by *Traffic* Manager pracuje správně. V tomto případě však nemůže dojít k převzetí služeb při selhání koncového bodu, což má vliv na celkovou dostupnost aplikace. Je důležité zkontrolovat, zda profil zobrazuje stav Online, nikoli stav zhoršený. Stav Online označuje, že kontroly stavu Traffic Manager fungují podle očekávání.

Další informace o řešení potíží se nezdařilo kontroly stavu, [najdete v tématu Řešení potíží se sníženým stavem ve Správci provozu Azure](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Nejčastější dotazy

* [Je Traffic Manager odolný vůči selhání mno ství Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Jak volba umístění skupiny prostředků ovlivňuje Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Jak zjistím aktuální stav jednotlivých koncových bodů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Můžu sledovat koncové body HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Mám při přidávání koncového bodu používat IP adresu nebo název DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Jaké typy IP adres lze použít při přidávání koncového bodu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Je možné v rámci jednoho profilu používat různé typy adresování koncového bodu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Co se stane, když se typ záznamu příchozího dotazu liší od typu záznamu přidruženého k typu adresování koncových bodů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Můžu použít profil s koncovými body adresovanými IPv4 / IPv6 ve vnořený profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Zastavil jsem koncový bod webové aplikace v mém profilu Traffic Manager, ale nedostávám žádný provoz ani poté, co jsem restartoval. Jak to mohu opravit?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Mohu použít Traffic Manager, i když moje aplikace nemá podporu pro HTTP nebo HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Jaké konkrétní odpovědi jsou požadovány od koncového bodu při použití monitorování TCP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Jak rychle traffic manager přesouvá uživatele od koncového bodu není v pořádku?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Jak lze zadat různá nastavení monitorování pro různé koncové body v profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Jak lze přiřadit hlavičky HTTP ke kontrolám stavu Traffic Manageru koncovým bodům?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Jakou hlavičku hostitele používají kontroly stavu koncového bodu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Jaké jsou IP adresy, ze kterých pocházejí kontroly stavu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Kolik kontrol stavu do mého koncového bodu mohu očekávat od Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Jak se dostanu upozorněni, když jeden z mých koncových bodů klesne?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Další kroky

[Zjistěte, jak Traffic Manager funguje](traffic-manager-how-it-works.md)

Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) podporovaných traffic managerem

Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-manage-profiles.md)

[Poradce při potížích se zhoršenou stavovinou](traffic-manager-troubleshooting-degraded.md) v koncovém bodě Traffic Manageru
