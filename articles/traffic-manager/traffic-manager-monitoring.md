---
title: Monitorování koncového bodu Azure Traffic Manager | Microsoft Docs
description: Tento článek vám může porozumět tomu, jak Traffic Manager používá monitorování koncového bodu a automatické převzetí služeb při selhání, aby zákazníci Azure mohli nasadit aplikace s vysokou dostupností
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742705"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

Azure Traffic Manager zahrnuje integrované monitorování koncových bodů a automatické převzetí služeb při selhání koncového bodu. Tato funkce vám pomůže doručovat aplikace s vysokou dostupností, které jsou odolné vůči selhání koncového bodu, včetně selhání oblasti Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurace monitorování koncového bodu

Pokud chcete nakonfigurovat monitorování koncového bodu, musíte v profilu Traffic Manager zadat následující nastavení:

* **Protokol**. Jako protokol, který Traffic Manager používá při zjišťování koncového bodu ke kontrole jeho stavu, vyberte HTTP, HTTPS nebo TCP. Monitorování HTTPS neověřuje, jestli je certifikát TLS/SSL platný – jenom kontroluje, jestli je k dispozici certifikát.
* **Port**. Vyberte port, který se používá pro požadavek.
* **Cesta**. Toto nastavení konfigurace je platné jenom pro protokoly HTTP a HTTPS, pro které je nutné zadat nastavení cesty. Zadáním tohoto nastavení protokolu monitorování protokolu TCP dojde k chybě. V případě protokolu HTTP a HTTPS zadejte relativní cestu a název webové stránky nebo souboru, ke kterému má monitorování přístup. Lomítko (/) je platná položka pro relativní cestu. Tato hodnota znamená, že se soubor nachází v kořenovém adresáři (výchozí).
* **Vlastní nastavení záhlaví**. Toto nastavení konfigurace vám pomůže přidat konkrétní hlavičky HTTP do kontrol stavu, které Traffic Manager odesílá koncovým bodům v rámci profilu. Vlastní hlavičky je možné zadat na úrovni profilu, aby se mohly použít pro všechny koncové body v daném profilu a/nebo na úrovni koncového bodu, které platí jenom pro tento koncový bod. Vlastní hlavičky můžete použít pro kontroly stavu koncových bodů v prostředí s více klienty. Tímto způsobem je lze správně směrovat do jejich cíle zadáním hlavičky hostitele. Toto nastavení můžete použít také přidáním jedinečných hlaviček, které lze použít k identifikaci Traffic Manager vzniklé požadavky HTTP (S) a jejich zpracování různým způsobem. Můžete zadat až osm párů záhlaví: hodnoty oddělené čárkou. Například "Header1: Hodnota1, header2: hodnota2". 
* **Očekávané rozsahy stavových kódů**. Toto nastavení umožňuje zadat více rozsahů kódu úspěšnosti ve formátu 200-299, 301-301. Pokud jsou tyto stavové kódy přijaty jako odpověď z koncového bodu při provedení kontroly stavu, Traffic Manager označí tyto koncové body jako v pořádku. Můžete zadat maximálně osm rozsahů stavových kódů. Toto nastavení se vztahuje pouze na protokol HTTP a HTTPS a na všechny koncové body. Toto nastavení se nachází na úrovni profilu Traffic Manager a ve výchozím nastavení je hodnota 200 definovaná jako kód stavu úspěch.
* **Interval zjišťování** Tato hodnota určuje, jak často se kontroluje stav koncového bodu z Traffic Managerho agenta pro zjišťování. Sem můžete zadat dvě hodnoty: 30 sekund (normální zjišťování) a 10 sekund (rychlé zjišťování). Pokud nejsou zadány žádné hodnoty, profil nastaví výchozí hodnotu na 30 sekund. Další informace o cenách pro rychlé zjišťování najdete na stránce s [cenami Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) .
* **Tolerovat se počet selhání**. Tato hodnota určuje, kolik selhání agentu pro zjišťování Traffic Manager před příznačením tohoto koncového bodu na stav není v pořádku. Jeho hodnota může být v rozsahu od 0 do 9. Hodnota 0 znamená, že u tohoto koncového bodu může být označení není v pořádku. Pokud není zadaná žádná hodnota, použije se výchozí hodnota 3.
* **Vypršel časový limit testu**. Tato vlastnost určuje dobu, po kterou by měl agent Traffic Manager pro zjišťování čekat, než se u koncového bodu zobrazí chyba. Pokud je interval zjišťování nastavený na 30 sekund, pak můžete nastavit hodnotu časového limitu v rozmezí 5 až 10 sekund. Pokud není zadaná žádná hodnota, použije se výchozí hodnota 10 sekund. Pokud je interval zjišťování nastavený na 10 sekund, můžete nastavit hodnotu časového limitu mezi 5 a 9 sekundami. Pokud není zadaná žádná hodnota časového limitu, použije se výchozí hodnota 9 sekund.

    ![Monitorování koncových bodů Traffic Manageru](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Obrázek: Traffic Manager monitorování koncového bodu**

## <a name="how-endpoint-monitoring-works"></a>Jak funguje monitorování koncových bodů

Když je monitorovací protokol nastavený jako HTTP nebo HTTPS, agent Traffic Managerho zjišťování vytvoří požadavek GET na koncový bod pomocí zadaného protokolu, portu a relativní cesty. Koncový bod se považuje za dobrý stav, pokud agent zjišťování obdrží odpověď 200-OK nebo kterákoli z odpovědí nakonfigurovaných v **očekávaném \* rozsahu stavového kódu**. Pokud je odpověď jinou hodnotou nebo neobdrží žádná odpověď v rámci časového limitu, agent Traffic Managerho zjišťování se pokusí znovu provést v závislosti na nastavení Tolerováného počtu selhání. Pokud je toto nastavení 0, neudělaly se žádné opakování. Pokud je počet po sobě jdoucích selhání vyšší než nastavení Tolerováného počtu selhání, je koncový bod označen jako není v pořádku.

Když je monitorovací protokol TCP, agent Traffic Manager probingu vytvoří požadavek na připojení TCP pomocí zadaného portu. Pokud koncový bod odpoví na požadavek s odezvou na navázání připojení, bude tato kontrolu stavu označena jako úspěšná. Agent Traffic Manager pro zjišťování obnoví připojení TCP. V případech, kdy je odezva jiná nebo se v rámci časového limitu nepřijala žádná odpověď, se agent Traffic Manager probingu pokusí podle nastavení Tolerováného počtu selhání. Pokud je toto nastavení 0, neudělaly se žádné opakování. Pokud je počet po sobě jdoucích selhání vyšší než nastavení tolerovat počet selhání, pak je tento koncový bod označen jako není v pořádku.

Ve všech případech Traffic Manager sondy z více míst. Po sobě jdoucí selhání určuje, co se stane v rámci každé oblasti. To je důvod, proč koncovým bodům dostávají sondy stavu z Traffic Manager s vyšší frekvencí, než nastavení použité pro interval zjišťování.

>[!NOTE]
>V případě protokolu HTTP nebo HTTPS je běžný postup na straně koncového bodu implementace vlastní stránky v rámci aplikace – například/Health.aspx. Pomocí této cesty k monitorování můžete provádět kontroly specifické pro aplikace, například zkontrolovat čítače výkonu nebo ověřit dostupnost databáze. Na základě těchto vlastních kontrol vrátí stránka odpovídající stavový kód HTTP.

Všechny koncové body v profilu Traffic Manager sdílí nastavení monitorování. Pokud potřebujete pro různé koncové body použít jiné nastavení monitorování, můžete vytvořit [vnořené profily Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stav koncového bodu a profilu

Můžete povolit a zakázat profily Traffic Manager a koncové body. Důvodem změny stavu koncového bodu může být taky Traffic Manager automatizované nastavení a procesy.

### <a name="endpoint-status"></a>Stav koncového bodu

Můžete povolit nebo zakázat konkrétní koncový bod. Podkladová služba, která může být stále v pořádku, není nijak ovlivněna. Změna stavu koncového bodu řídí dostupnost koncového bodu v profilu Traffic Manager. Pokud je stav koncového bodu zakázaný, Traffic Manager nekontrolují jeho stav a koncový bod není zahrnutý v odpovědi DNS.

### <a name="profile-status"></a>Profile status

Pomocí nastavení stav profilu můžete povolit nebo zakázat konkrétní profil. Stav koncového bodu má vliv na jeden koncový bod a stav profilu má vliv na celý profil, včetně všech koncových bodů. Když profil zakážete, koncovým bodům se nekontrolují stav a v odpovědi DNS nejsou zahrnuté žádné koncové body. Pro dotaz DNS se vrátí kód [NXDOMAIN](https://tools.ietf.org/html/rfc2308) odezvy.

### <a name="endpoint-monitor-status"></a>Stav monitorování koncového bodu

Stav monitorování koncového bodu je Traffic Manager generovaná hodnota, která zobrazuje stav koncového bodu. Toto nastavení nemůžete změnit ručně. Stav monitorování koncového bodu je kombinací výsledků monitorování koncového bodu a nakonfigurovaného stavu koncového bodu. Možné hodnoty stavu monitorování koncového bodu jsou uvedeny v následující tabulce:

| Profile status | Stav koncového bodu | Stav monitorování koncového bodu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |Povoleno |Inactive |Profil byl zakázán. I když je stav koncového bodu povolený, má přednost stav profilu (zakázáno). Koncové body v zakázaných profilech nejsou monitorovány. Pro dotaz DNS se vrátí kód NXDOMAIN odezvy. |
| &lt;libovolné&gt; |Zakázáno |Zakázáno |Koncový bod byl zakázán. Zakázané koncové body nejsou monitorovány. Koncový bod není zahrnutý v odpovědích DNS, protože nepřijímá přenosy. |
| Povoleno |Povoleno |Online |Koncový bod je monitorovaný a je v pořádku. Je zahrnutý v odpovědích DNS a může přijímat provoz. |
| Povoleno |Povoleno |Snížený výkon |Kontrola stavu monitorování koncového bodu se nezdařila. Koncový bod není zahrnutý v odpovědích DNS a nepřijímá přenosy. <br>Výjimka je v případě, že jsou všechny koncové body degradovány. V takovém případě jsou všechny z nich považovány za vrácené v reakci na dotaz).</br>|
| Povoleno |Povoleno |CheckingEndpoint |Koncový bod je monitorovaný, ale zatím se nepřijaly výsledky prvního testu. CheckingEndpoint je dočasný stav, který se obvykle objevuje hned po přidání nebo povolení koncového bodu v profilu. Koncový bod v tomto stavu je zahrnutý v odpovědích DNS a může přijímat přenosy. |
| Povoleno |Povoleno |Zastaveno |Webová aplikace, na kterou koncový bod odkazuje, není spuštěná. Ověřte nastavení webové aplikace. K tomuto stavu může dojít také v případě, že koncový bod je typu vnořený koncový bod a podřízený profil je zakázán nebo je neaktivní. <br>Koncový bod se stavem zastaveno není monitorován. Není zahrnutý v odpovědích DNS a nepřijímá přenos. Výjimka je v případě, že jsou všechny koncové body degradovány. V takovém případě se všechny z nich budou považovat za vrácené v reakci na dotaz.</br>|

Podrobnosti o tom, jak se počítá stav monitorování koncového bodu pro vnořené koncové body, najdete v tématu [vnořené Traffic Manager profily](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stav zastaveného monitorování koncového bodu může nastat v App Service, pokud vaše webová aplikace neběží na úrovni Standard nebo vyšší. Další informace najdete v tématu [Traffic Manager Integration with App Service](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Stav monitorování profilu

Stav monitorování profilu je kombinace nakonfigurovaného stavu profilu a hodnoty stavu monitorování koncového bodu pro všechny koncové body. Možné hodnoty jsou popsány v následující tabulce:

| Stav profilu (jak je nakonfigurováno) | Stav monitorování koncového bodu | Stav monitorování profilu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |&lt;libovolný &gt; nebo profil bez definovaných koncových bodů. |Zakázáno |Profil byl zakázán. |
| Povoleno |Stav nejméně jednoho koncového bodu je degradován. |Snížený výkon |Zkontrolujte hodnoty stavu jednotlivých koncových bodů a určete, které koncové body vyžadují další pozornost. |
| Povoleno |Stav aspoň jednoho koncového bodu je online. Žádné koncové body nemají stav snížené úrovně. |Online |Služba přijímá provoz. Nevyžaduje se žádná další akce. |
| Povoleno |Stav nejméně jednoho koncového bodu je CheckingEndpoint. Žádné koncové body nejsou v režimu online nebo snížený stav. |CheckingEndpoints |K tomuto stavu přechodu dojde, pokud je profil vytvořen nebo povolen. Stav koncového bodu je kontrolován při prvním spuštění. |
| Povoleno |Stavy všech koncových bodů v profilu jsou buď zakázané, nebo zastavené, nebo profil nemá žádné definované koncové body. |Inactive |Žádné koncové body nejsou aktivní, ale profil je stále povolen. |

## <a name="endpoint-failover-and-recovery"></a>Převzetí služeb při selhání a obnovení koncového bodu

Traffic Manager pravidelně kontroluje stav každého koncového bodu, včetně koncových bodů, které nejsou v pořádku. Traffic Manager detekuje, kdy se koncový bod narazí do stavu v pořádku a převede ho zpátky do rotace.

Koncový bod není v pořádku, když nastane kterákoli z následujících událostí:

- Pokud je protokol monitorování HTTP nebo HTTPS:
    - Odpověď, která není 200, nebo odpověď, která nezahrnuje rozsah stavu zadaný v nastavení **očekávané rozsahy stavových kódů** , se zobrazí jako přijaté. (Včetně jiného kódu 2xx nebo přesměrování 301/302).
- Pokud je monitorovací protokol TCP: 
    - V reakci na žádost SYN, která byla odeslána Traffic Manager k pokusu o připojení, obdrží odpověď jinou než ACK nebo SYN-ACK.
- Prodlev. 
- Jakékoli další potíže s připojením, které mají za následek nedostupnost tohoto koncového bodu.

Další informace o řešení neúspěšných kontrol najdete v tématu [řešení potíží se stavem sníženou na Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Časová osa na následujícím obrázku je podrobný popis procesu monitorování Traffic Managerho koncového bodu, který má následující nastavení:

* Protokol monitorování je HTTP.
* Interval zjišťování je 30 sekund.
* Počet dovolených selhání je 3.
* Hodnota časového limitu je 10 sekund.
* Hodnota TTL služby DNS je 30 sekund.

![Pořadí převzetí služeb při selhání koncového bodu Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

**Obrázek: převzetí služeb při selhání koncového bodu Traffic Manageru a posloupnost obnovení**

1. **Získat**. U každého koncového bodu Traffic Manager monitorovací systém požadavek GET na cestu zadanou v nastavení monitorování.
2. **200 OK nebo vlastní rozsah kódu zadaný Traffic Manager nastavení monitorování profilu**. Systém monitorování očekává, že se v rozsahu zadaném v nastavení monitorování vrátí do 10 sekund stav HTTP 200 OK nebo stavový kód. Při přijetí této odpovědi rozpozná, že je služba k dispozici.
3. **30 sekund mezi kontrolami**. Kontrolu stavu koncového bodu se opakuje každých 30 sekund.
4. **Služba není k dispozici**. Služba nebude k dispozici. Traffic Manager neví, dokud neproběhne další kontroly stavu.
5. **Pokusí se získat přístup k cestě monitorování**. Monitorovací systém provede požadavek GET, ale neobdrží odpověď v období časového limitu 10 sekund. Pak se pak několikrát pokusí o tři sekundy v intervalu 30 sekund. Pokud jeden z pokusů proběhne úspěšně, počet pokusů se resetuje.
6. **Stav nastavený na Degradováno**. Po čtvrtém po sobě jdoucí selhání systém monitorování označí stav nedostupného koncového bodu jako snížený.
7. **Provoz se přesměruje na jiné koncové body**. Traffic Manager názvové servery DNS se aktualizují a Traffic Manager už v reakci na dotazy DNS nevrátí koncový bod. Nová připojení jsou směrována k ostatním dostupným koncovým bodům. Nicméně předchozí odpovědi DNS, které obsahují tento koncový bod, mohou být i nadále uloženy v mezipaměti rekurzivními servery DNS a klienty DNS. Klienti pokračují v používání koncového bodu, dokud nevyprší platnost mezipaměti služby DNS. Po vypršení platnosti mezipaměti DNS budou klienti vytvářet nové dotazy DNS a směrovat je na jiné koncové body. Doba trvání mezipaměti je řízena nastavením TTL v profilu Traffic Manager, například 30 sekund.
8. **Kontrola stavu pokračuje**. Traffic Manager nadále kontroluje stav koncového bodu, i když má snížený stav. Traffic Manager detekuje, kdy se koncový bod vrátí do stavu.
9. **Služba se vrátí zpět do režimu online**. Služba bude k dispozici. Koncový bod udržuje v Traffic Manager stav snížené úrovně, dokud systém monitorování neprovede další kontrolu stavu.
10. **Provoz do služby pokračuje**. Traffic Manager odešle požadavek GET a obdrží odpověď na stav 200 OK. Služba se vrátila do stavu v pořádku. Traffic Manager názvové servery a začnou v odpovědích DNS začínat název DNS služby. Provoz se vrátí do koncového bodu jako odpovědi DNS uložené v mezipaměti, které vracejí jiné koncové body, a stávající připojení k ostatním koncovým bodům skončí.

    > [!NOTE]
    > Vzhledem k tomu, že Traffic Manager funguje na úrovni DNS, nemůže mít vliv na existující připojení ke koncovému bodu. Když přesměruje provoz mezi koncovými body (buď změnou nastavení profilu, nebo během převzetí služeb při selhání nebo navrácení služeb po obnovení), Traffic Manager směruje nová připojení k dostupným koncovým bodům. Ostatní koncové body však mohou nadále přijímat přenosy přes existující připojení, dokud nebudou tyto relace ukončeny. Aby bylo možné povolit vyprázdnění provozu z existujících připojení, aplikace by měly omezit dobu trvání relace použitou u každého koncového bodu.

## <a name="traffic-routing-methods"></a>Metody směrování provozu

Pokud má koncový bod stav zhoršený, už se nevrací jako odpověď na dotazy DNS. Místo toho se vybere a vrátí alternativní koncový bod. Metoda směrování provozu nakonfigurovaná v profilu určuje způsob výběru alternativního koncového bodu.

* **Priorita**. Koncové body tvoří seznam s určenou prioritou. První dostupný koncový bod v seznamu se vždycky vrátí. Pokud je stav koncového bodu zhoršený, vrátí se další dostupný koncový bod.
* **Vážená**. Všechny dostupné koncové body se náhodně vybírají na základě jejich přiřazených vah a závaží dalších dostupných koncových bodů.
* **Výkon**. Vrátí se koncový bod nejbližší koncovému uživateli. Pokud tento koncový bod není k dispozici, Traffic Manager přesune provoz do koncových bodů v nejbližší nejbližší oblasti Azure. Pomocí [vnořených profilů Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)můžete nakonfigurovat alternativní plány převzetí služeb při selhání pro přenosy výkonu.
* **Geografické**. Koncový bod namapovaný na poskytování geografického umístění na základě žádosti o IP adresu požadavku na dotaz je vrácen. Pokud tento koncový bod není k dispozici, není vybrán jiný koncový bod pro převzetí služeb při selhání, protože zeměpisná poloha může být mapována pouze na jeden koncový bod v profilu. (Další podrobnosti najdete v [nejčastějších dotazech](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Doporučujeme, abyste při použití geografického směrování používali zákazníkům vnořené Traffic Manager profily s více než jedním koncovým bodem jako koncovými body profilu.
* **Vícehodnotové** Vrátí se několik koncových bodů mapovaných k adresám IPv4/IPv6. Při přijetí dotazu pro tento profil jsou v pořádku koncové body v závislosti na **maximálním počtu záznamů v hodnotě odpovědi** , kterou jste zadali. Výchozí počet odpovědí je dva koncové body.
* **Podsíť** Vrátí se koncový bod mapovaný na sadu rozsahů IP adres. Při přijetí požadavku z této IP adresy se vrátí koncový bod, který je pro tuto IP adresu namapovaný. 

Další informace najdete v tématu [Traffic Manager metody směrování provozu](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedna výjimka pro normální chování při směrování provozu nastane, pokud mají všechny opravňující koncové body stav snížené úrovně. Traffic Manager se snaží o "nejlepší úsilí" a *reagovat, jako by ve skutečnosti byly všechny degradované koncové body stavu ve stavu online*. Toto chování je vhodnější jako alternativa, což by nevrátilo žádný koncový bod v odpovědi DNS. Zakázané nebo zastavené koncové body nejsou monitorovány, proto nejsou považovány za opravňující k provozu.
>
> Tato podmínka je obvykle způsobena nesprávnou konfigurací služby, například:
>
> * Seznam řízení přístupu [ACL] blokuje Traffic Manager kontroly stavu.
> * Nesprávná konfigurace portu monitorování nebo protokolu v profilu Traffic Manageru.
>
> V důsledku tohoto chování je to, že pokud Traffic Manager kontroly stavu nejsou správně nakonfigurované, může se zobrazit při směrování provozu, ale i když *Traffic Manager pracuje* správně. V takovém případě však neproběhne selhání koncového bodu, což má vliv na celkovou dostupnost aplikace. Je důležité ověřit, že profil zobrazuje online stav, nikoli stav snížené úrovně. Online stav znamená, že kontroly stavu Traffic Manager fungují podle očekávání.

Další informace o řešení potíží s neúspěšnými kontrolami stavu najdete v tématu [řešení potíží s degradací stavu v Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Nejčastější dotazy

* [Je Traffic Manager odolné vůči selhání oblasti Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Jak Volba umístění skupiny prostředků ovlivní Traffic Manager?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Návody zjistit aktuální stav každého koncového bodu?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Můžu monitorovat koncové body HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Používá se při přidávání koncového bodu IP adresa nebo název DNS?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Jaké typy IP adres můžu použít při přidávání koncového bodu?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Můžu v jednom profilu použít jiné typy adresování koncových bodů?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Co se stane, když se typ záznamu příchozího dotazu liší od typu záznamu přidruženého k typu adresování koncových bodů?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Můžu použít profil s adresovánými koncovými body IPv4/IPv6 ve vnořeném profilu?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Ukončil (a) jsem koncový bod webové aplikace v profilu Traffic Manager, ale nepřijímám žádný provoz ani po jeho restartování. Jak mohu tuto situaci opravit?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Můžu použít Traffic Manager i v případě, že moje aplikace nemá podporu pro HTTP nebo HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Jaké konkrétní odpovědi z koncového bodu vyžadují při použití monitorování protokolu TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Jak rychle Traffic Manager přesunout uživatele z poškozeného koncového bodu?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Jak lze zadat různá nastavení monitorování pro různé koncové body v profilu?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Jak mohu přiřadit hlavičky HTTP ke kontrolám Traffic Manager stavu do mých koncových bodů?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Jakou hlavičku hostitele používají kontroly stavu koncových bodů?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Jaké jsou IP adresy, ze kterých pocházejí kontroly stavu?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Kolik kontrol stavu do mého koncového bodu je možné očekávat od Traffic Manager?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Jak získám oznámení, pokud se některý z mých koncových bodů přestane nastavovat?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Další kroky

Informace [o tom, jak Traffic Manager funguje](traffic-manager-how-it-works.md)

Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager

Informace o tom, jak [vytvořit profil Traffic Manager](traffic-manager-manage-profiles.md)

[Řešení potíží se stavem sníženého](traffic-manager-troubleshooting-degraded.md) koncového bodu Traffic Manager