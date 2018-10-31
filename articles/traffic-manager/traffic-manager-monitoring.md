---
title: Monitorování koncových bodů v Azure Traffic Manager | Dokumentace Microsoftu
description: V tomto článku vám pomůžou pochopit, jak Traffic Manager pomocí monitorování koncových bodů a koncový bod automatické převzetí služeb při selhání pomáhá zákazníkům Azure nasazovat aplikací s vysokou dostupností
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 15b609bbf4ab048722f2512371eeffece92b3245
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138136"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncových bodů Traffic Manageru

Azure Traffic Manager zahrnuje integrovaný koncový bod monitorování a automatické koncový bod převzetí služeb při selhání. Tato funkce pomáhá zajistit vysokou dostupnost aplikace, které jsou odolné vůči selhání koncového bodu, včetně selhání oblasti Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurace monitorování koncových bodů

Pokud chcete nakonfigurovat monitorování koncových bodů, je nutné zadat následující nastavení ve vašem profilu Traffic Manageru:

* **Protokol**. Zvolte možnost protokolu HTTP, HTTPS nebo TCP jako protokol pro Traffic Manager používá při zjišťování váš koncový bod zkontrolovat její stav. Monitorování protokolu HTTPS neověřuje, že zda váš certifikát SSL není platný – pouze zkontroluje, že certifikát je k dispozici.
* **Port**. Zvolte port používaný pro daný požadavek.
* **Cesta**. Toto nastavení konfigurace je platná pouze pro protokoly HTTP a HTTPS, u které určení cesty je požadované nastavení. Poskytuje toto nastavení pro protokol TCP monitorování protokolu dojde k chybě. Pro protokol HTTP a HTTPS zadejte relativní cestu a název webové stránky nebo soubor, který přistupuje k monitorování. Lomítkem (/) je platná položka pro relativní cestu. Tato hodnota znamená, že se soubor nachází v kořenovém adresáři (výchozí).
* **Nastavení vlastních hlaviček** tuto konfiguraci nastavení konkrétní hlavičky protokolu HTTP umožňuje přidat stav kontroluje, že Traffic Manager odešle do koncových bodů v rámci profilu. Vlastní hlavičky se dá nastavit na úrovni profilu použitelný pro všechny koncové body v profilu and / or úrovni koncového bodu lze použít pouze na tento koncový bod. Můžete použít vlastní záhlaví pro kontroly stavu koncových bodů v prostředí s více tenanty správně směroval do své cíle zadáním hlavičku hostitele s. Toto nastavení lze také použít přidáním jedinečný hlavičky, které lze použít k identifikaci Traffic Manageru pochází požadavky HTTP (S) a zpracuje je jinak.
* **Byl očekáván stavový kód rozsahy** toto nastavení umožňuje určit více rozsahů úspěchu kód ve formátu 200 299, 301 301. Pokud tyto stavové kódy jsou přijata jako odpověď od koncového bodu, když je zahájeno kontrolou stavu, Traffic Manager označí těchto koncových bodů, jako v pořádku. Můžete zadat maximálně 8 rozsah kódu stavu. Toto nastavení platí jenom pro protokol HTTP a HTTPS a pro všechny koncové body. Toto nastavení je na úrovni profilu Traffic Manageru a ve výchozím nastavení je hodnota 200 definována jako stavový kód úspěchu.
* **Interval testování**. Tato hodnota určuje, jak často se kontroluje jeho stav od agenta testování Traffic Manager koncový bod. Můžete zadat dvě hodnoty tady: 30 sekund (běžné zjišťování) a 10 sekund (rychlé zjišťování). Pokud se nezadají žádné hodnoty, profil, který nastaví na výchozí hodnota je 30 sekund. Přejděte [ceny za Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) stránku a další informace o cenách rychlé zjišťování.
* **Tolerovaný počet selhání**. Tato hodnota určuje, kolik chyb agenta testování Traffic Manageru toleruje před označením tohoto koncového bodu jako není v pořádku. Jeho hodnota musí být mezi 0 a 9 rozsahu. Hodnota 0 znamená, že tento koncový bod se označí jako špatný může způsobit selhání jednoho monitorování. Pokud není zadána žádná hodnota, použije výchozí hodnotu 3.
* **Časový limit testu**. Tato vlastnost určuje množství času, agent zjišťování Traffic Manageru měli počkat, předtím, než kontrolu selhání při odeslání ke koncovému bodu sondu stavu zaškrtnutí. Pokud Interval zjišťování je nastavené na 30 sekund, můžete nastavit hodnotu časového limitu 5 – 10 sekund. Pokud není zadána žádná hodnota, použije se výchozí hodnota je 10 sekund. Pokud Interval zjišťování je nastaven na 10 sekund, můžete nastavit hodnotu časového limitu od 9 do 5 sekund. Pokud není zadána žádná hodnota časového limitu, použije výchozí hodnotu 9 sekund.

    ![Monitorování koncových bodů Traffic Manageru](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Obrázek: Monitorování koncových bodů Traffic Manageru**

## <a name="how-endpoint-monitoring-works"></a>Jak funguje monitorování koncových bodů

Pokud protokol monitorování je nastavená na HTTP nebo HTTPS, agent zjišťování Traffic Manager odešle požadavek GET na koncový bod pomocí protokol, port a relativní cestě uvedené. Pokud získá zpět odpověď 200 OK nebo některý z odpovědi gurovaný **byl očekáván stavový kód \*rozsahy**, pak tento koncový bod se považuje za v pořádku. Pokud je odpověď na jinou hodnotu, nebo pokud není žádná odpověď v časovém limitu zadán, pak Traffic Manageru, testování, agent se pokusí opětovně podle nastavení Tolerovaný počet selhání (znovu pokusí se provést Pokud toto nastavení je 0). Pokud počet po sobě jdoucích selhání je vyšší než nastavení Tolerovaný počet selhání, se označí jako špatný tohoto koncového bodu. 

Pokud je monitorovací protokol TCP, agent zjišťování Traffic Manageru zahájí žádost o připojení TCP přes port zadaný. Pokud koncový bod odpoví na požadavek odpovědí k navázání připojení, kontroly stavu je označen jako úspěšný a agent zjišťování Traffic Manager resetuje připojení TCP. Pokud je odpověď na jinou hodnotu, nebo pokud je přijata žádná odpověď v časovém limitu zadán, Traffic Manager, testování, agent se pokusí opětovně podle nastavení Tolerovaný počet selhání (znovu pokusí probíhají Pokud toto nastavení je 0). Pokud počet po sobě jdoucích selhání je vyšší než nastavení Tolerovaný počet selhání, se označí není v pořádku tohoto koncového bodu.

Ve všech případech se Traffic Manager testy z více míst a stanovení po sobě jdoucích selhání dochází v rámci každé oblasti. Zároveň to znamená, že koncové body dostávají sond stavu z Traffic Manageru s vyšší frekvencí než nastavení použité pro Interval zjišťování.

>[!NOTE]
>Pro protokol HTTP nebo HTTPS monitorování protokolu je implementovat vlastní stránku v rámci vaší aplikace – například /health.aspx běžnou praxí na straně koncových bodů. Použití této cesty pro monitorování, můžete provést kontroly specifické pro aplikaci, jako je kontrola čítače výkonu nebo ověření dostupnosti databáze. Na stránce založené na těchto vlastních kontrol, vrátí odpovídající stavový kód HTTP.

Všechny koncové body v profilu služby Traffic Manager sdílet nastavení monitorování. Pokud budete muset použít jiné nastavení monitorování pro různé koncové body, můžete vytvořit [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stav koncového bodu a profilu

Můžete povolit a zakázat koncové body a profily Traffic Manageru. Nicméně změny stavu koncových bodů také může dojít k jako výsledek Traffic Manageru automatizované nastavení a procesy.

### <a name="endpoint-status"></a>Stav koncového bodu

Můžete povolit nebo zakázat určitého koncového bodu. Základní služby, který může být v pořádku, není ovlivněno. Změna stavu koncového bodu řídí dostupnosti koncových bodů v profilu služby Traffic Manager. Stav koncového bodu zakázán, Traffic Manager nekontroluje jeho stavu a koncový bod není zahrnutý v odpovědi DNS.

### <a name="profile-status"></a>Stav profilu

Stav nastavení profilu můžete povolit nebo zakázat konkrétní profil. Při stavu koncových bodů má vliv na jeden koncový bod, ovlivní stav profilu celého profilu, včetně všech koncových bodů. Při zakázání profilu koncových bodů nejsou vyhledány stavu a žádné koncové body jsou zahrnuty v odpovědi DNS. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) kód odpovědi se vrátí dotaz DNS.

### <a name="endpoint-monitor-status"></a>Stav monitorování koncového bodu

Stav monitorování koncového bodu je hodnota generované Traffic Manageru, která zobrazuje stav koncového bodu. Nelze změnit tato nastavení ručně. Stav monitorování koncového bodu je kombinací stav konfigurovaný koncový bod a výsledků monitorování koncových bodů. V následující tabulce jsou uvedeny možné hodnoty stavu monitorování koncových bodů:

| Stav profilu | Stav koncového bodu | Stav monitorování koncového bodu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |Povoleno |Neaktivní |Profil je zakázané. I když je povolen stav koncového bodu, přednost má stav profilu (zakázáno). Koncové body v profilech zakázané nejsou monitorovány. Kód odpovědi NXDOMAIN se vrátí dotaz DNS. |
| &lt;Všechny&gt; |Zakázáno |Zakázáno |Koncový bod je zakázané. Zakázané koncových bodů nejsou monitorovány. Koncový bod není zahrnutý v odpovědi DNS, proto, že neobdrží provoz. |
| Povoleno |Povoleno |Online |Koncový bod se sleduje a je v pořádku. Je součástí odpovědí DNS a může přijímat provoz. |
| Povoleno |Povoleno |Sníženo |Monitorování kontroly stavu koncových bodů se nedaří. Koncový bod není zahrnutý v odpovědi DNS a nepřijímá provoz. <br>Jedinou výjimkou je to je-li všechny koncové body jsou degradované, v takovém případě všechny z nich jsou považovány za který se má vrátit v odpovědi na dotaz).</br>|
| Povoleno |Povoleno |CheckingEndpoint |Je monitorovaný koncový bod, ale ještě nebyly přijaty výsledky první test paměti. CheckingEndpoint se o dočasný stav, který se obvykle nachází bezprostředně po přidání nebo povolení koncového bodu v profilu. Koncový bod v tomto stavu je zahrnutý v odpovědi DNS a může přijímat provoz. |
| Povoleno |Povoleno |Zastaveno |Cloudové služby nebo webové aplikace, odkazující na koncový bod není spuštěná. Zkontrolujte nastavení cloudové služby nebo webové aplikace. To může také dojít, pokud koncový bod je koncový bod typu vnořené a podřízené profilu je zakázán nebo je neaktivní. <br>Koncový bod s ve stavu Zastaveno není monitorován. Není součástí odpovědí DNS a ne přijímat provoz. Jedinou výjimkou je to je, pokud všechny koncové body jsou degradované, v takovém případě všechny z nich bude považovat za který se má vrátit v odpovědi na dotaz.</br>|

Podrobnosti o tom, jak se počítá stav monitorování koncového bodu pro vnořené koncové body, naleznete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

>[!NOTE]
> Zastavit koncový bod monitorování stavu může dojít ve službě App Service, pokud vaše webová aplikace neběží na úrovni Standard nebo vyšší. Další informace najdete v tématu [integrace Traffic Manageru pomocí služby App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Profil sledování stavu

Stav monitorování profilu je kombinací stav nakonfigurovaný profil a koncový bod monitorování stavu pro všechny koncové body. Možné hodnoty jsou popsány v následující tabulce:

| Stav profilu (jak je nakonfigurovaná) | Stav monitorování koncového bodu | Profil sledování stavu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |&lt;žádné&gt; nebo profil s žádné definované koncové body. |Zakázáno |Profil je zakázané. |
| Povoleno |Stav aspoň jeden koncový bod má snížený výkon. |Sníženo |Zkontrolujte hodnoty stavu jednotlivých koncových bodů k určení, jaké koncové body vyžadují další pozornost. |
| Povoleno |Stav aspoň jeden koncový bod je Online. Žádné koncové body mají stav snížený. |Online |Služba přijímá provoz. Nevyžaduje se žádná další akce. |
| Povoleno |Stav aspoň jeden koncový bod je CheckingEndpoint. Žádné koncové body jsou ve stavu Online nebo snížený. |CheckingEndpoints |Tento přechod stavu nastane, pokud profilu, pokud se vytvořil nebo povolil. Stav koncového bodu se kontroluje poprvé. |
| Povoleno |Stavy všechny koncové body v profilu jsou zakázané nebo Stopped nebo profil, který nemá žádné definované koncové body. |Neaktivní |Žádné koncové body jsou aktivní, ale profil, který je stále povolen. |

## <a name="endpoint-failover-and-recovery"></a>Koncový bod převzetí služeb při selhání a obnovení

Traffic Manager pravidelně kontroluje stav každé koncového bodu, včetně koncových bodů není v pořádku. Traffic Manager zjistí, že koncový bod bude v pořádku a přináší zpět do otočení.

Koncový bod není v pořádku, když dojde k některé z následujících událostí:
- Pokud je monitorovací protokol HTTP nebo HTTPS:
    - Než 200 odpovědi nebo odpovědi, která neobsahuje stav rozsahu zadaného v **byl očekáván stavový kód rozsahy** nastavení, přijetí (včetně kódu různé 2xx nebo přesměrovat 301/302).
- Pokud je monitorovací protokol TCP: 
    - V reakci na žádost o SYNCHRONIZACI odeslán Traffic Managerem pokusu o navázání připojení je přijata odpověď než PĚT nebo SYN potvrzení.
- Vypršel časový limit. 
- Jakékoli jiné potíže s připojením výsledkem je koncový bod se není dostupný.

Další informace o řešení problémů s neúspěšné kontroly najdete v tématu [řešení potíží s Degradovaným stavem Azure Traffic Manageru](traffic-manager-troubleshooting-degraded.md). 

Časová osa na následujícím obrázku je podrobný popis procesu monitorování koncových bodů Traffic Manageru, který má následující nastavení: sledování protokol je HTTP, interval zjišťování je 30 sekund, počet neúspěšných přípustný je 3, hodnota časového limitu je 10 sekund a hodnotu TTL pro DNS je 30 sekund.

![Pořadí převzetí služeb při selhání a navrácení služeb po obnovení koncových bodů Traffic Manageru](./media/traffic-manager-monitoring/timeline.png)

**Obrázek: Traffic manager koncový bod převzetí služeb při selhání a obnovení pořadí**

1. **ZÍSKAT**. Pro každý koncový bod monitorování systému Traffic Manageru provede požadavek GET na cestě zadané v nastavení monitorování.
2. **Zadaný profil služby Traffic Manager nastavení sledování rozsahu 200 OK nebo vlastní kód** . Systém monitorování očekává, že HTTP 200 OK nebo nebo vlastní kód rozsah zadaný profil služby Traffic Manager monitorování nastavení zpráv, který se má vrátit do 10 sekund. Když tuto odpověď přijme, rozpozná, že služba je k dispozici.
3. **30 sekundách mezi kontrolami**. Kontrola stavu koncového bodu je opakovat každých 30 sekund.
4. **Služba není k dispozici**. Služba přestane být k dispozici. Traffic Manager nebude vědět, až do další kontroly stavu.
5. **Pokusí o přístup k cestě monitorování**. Monitorovací systém provádí požadavek GET, ale neobdrží odpověď v časovém limitu 10 sekund (případně než 200 může být přijata odpověď). Zkusí třikrát více každých 30 sekund. Pokud pokusy je úspěšné, pak počet pokusů se vynuluje.
6. **Stav nastaven na snížený**. Po čtvrtém po sobě jdoucích selhání systém monitorování označuje stav koncového bodu není k dispozici jako Snížený.
7. **Provoz je přesměrováni do jiné koncové body**. Aktualizují se názvové servery DNS Traffic Manageru a Traffic Manager koncový bod by již nevracelo v odpovědi na dotazy DNS. Nová připojení jsou směrované na jiné, k dispozici koncové body. Ale předchozí odpovědi DNS, které obsahují tento koncový bod může přesto uložit do mezipaměti rekurzivních serverů DNS a klienti DNS. Klienti se nadále používat koncový bod do vypršení platnosti mezipaměti DNS. Vypršení platnosti mezipaměti DNS, klienti zadávat nové dotazy DNS a jsou směrované do různých koncových bodů. Doba uložení do mezipaměti je řízena nastavením TTL v profilu služby Traffic Manager, například 30 sekund.
8. **Doplněk pro kontroly stavu pokračovat**. Zkontrolovat stav koncového bodu, pokud má stav snížený pokračuje v Traffic Manageru. Traffic Manager zjistí, když se koncový bod se vrátí do stavu.
9. **Služba vrátí do režimu online**. Služba je k dispozici. Koncový bod zachovává stav snížený v Traffic Manageru, dokud monitorovací systém provádí další kontrolu stavu.
10. **Provoz do služby obnoví**. Traffic Manager odešle požadavek GET a obdrží odpověď stavový kód 200 OK. Služba vrátila do stavu v pořádku. Aktualizují se názvové servery Traffic Manageru a začnou distribuovat název DNS služby v odpovědi DNS. Provoz vrátí ke koncovému bodu jako odpovědí uložených v mezipaměti DNS který vrací ostatní koncové body stálou platností a jako existující připojení k jiné koncové body jsou ukončeny.

    > [!NOTE]
    > Vzhledem k tomu, že Traffic Manager pracuje na úrovni DNS, ho nemůže ovlivnit existujících připojení pro libovolný koncový bod. Když ho směruje provoz mezi koncovými body (nebo změněných profilu nastavení, při převzetí služeb při selhání a navrácení služeb po obnovení), Traffic Manager přesměruje nových připojení na dostupné koncové body. Další koncové body však může i nadále přijímat provoz přes existující připojení, dokud tyto relace budou ukončeny. Pokud chcete povolit provoz k vyprázdnění z existující připojení, by měla aplikace omezit doba trvání relace, které jsou součástí každého koncového bodu.

## <a name="traffic-routing-methods"></a>Metody směrování provozu

Koncový bod má stav snížený, se už vrátila v odpovědi na dotazy DNS. Alternativní koncový bod je místo toho zvolit a vrátil. Metody směrování provozu nakonfigurovali v profilu Určuje, jak je vybrán alternativní koncový bod.

* **Priorita**. Koncové body tvoří seznam seřazený podle priority. Vrátí se vždycky první dostupný koncový bod v seznamu. Pokud je Degradovaný stav koncového bodu, bude vrácena další dostupný koncový bod.
* **Váha**. Žádný dostupný koncový bod je vybrán náhodně podle jejich přiřazené váhy a váhy k dispozici koncové body.
* **Výkon**. Koncový bod nejblíže koncového uživatele se vrátí. Pokud tohoto koncového bodu není k dispozici, Traffic Manager přesune provozu do koncových bodů v dalším nejbližší oblasti Azure. Plány alternativní převzetí služeb při selhání pro směrování provozu výkonu můžete nakonfigurovat pomocí [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografické**. Mapovat k obsluze zeměpisné polohy na základě dotazu požadavku koncového bodu je vrácena IP adresy. Pokud je tento koncový bod není k dispozici, jiný koncový bod nebude vybrán převzetí služeb při selhání, protože zeměpisnou polohu lze mapovat pouze na jeden koncový bod v profilu (Další podrobnosti najdete v [nejčastější dotazy k](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Jako osvědčený postup, při použití geografické směrování doporučujeme zákazníkům umožňují použít vnořené profily Traffic Manageru s více než jeden koncový bod jako koncové body profilu.
* **Více hodnot** několik koncových bodů namapované na IPv4/IPv6 adres se vrátí. Po přijetí dotaz pro tento profil se vrátí v dobrém stavu koncových bodů na základě **maximální počet záznamů v odpovědi** hodnotu, která jste zadali. Výchozí počet odpovědí je dva koncové body.
* **Podsíť** koncový bod mapovány na skupinu rozsahů IP adres je vrácena. Při přijetí požadavku z této IP adresy, koncový bod vrátil, je ten mapovány pro tuto IP adresu. 

Další informace najdete v tématu [metody směrování provozu Traffic Manageru](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedinou výjimkou je to normální chování směrování provozu nastane, pokud všechny oprávněné koncové body mají degradovaný stav. Traffic Manager umožňuje pokusit "best effort" a *reaguje, jako by všechny snížený stavu koncových bodů jsou ve skutečnosti ve stavu online*. Toto chování je vhodnější alternativní řešení, která bude vracet libovolný koncový bod v odpovědi DNS. Zakázaný nebo Stopped koncových bodů nejsou monitorovány, proto nejsou považovány za vhodné pro provoz.
>
> K tomuto stavu obvykle způsobuje nesprávná konfigurace služby, jako například:
>
> * Seznam řízení přístupu [ACL] blokování kontroly stavu Traffic Manageru.
> * Nesprávná konfigurace monitorování portu nebo protokol v profil Traffic Manageru.
>
> Důsledkem tohoto chování je, že pokud se Traffic Manager kontroly stavu nejsou správně nakonfigurované, může se zdát, od provozu, jako by směrování Traffic Manageru *je* funguje správně. Ale v takovém případě převzetí služeb při selhání koncového bodu nelze dojít, což má vliv na dostupnost aplikací. Je důležité zkontrolovat, že v profilu se zobrazí Online stavu, není stav snížený. Online stav označuje, že jsou kontroly stavu Traffic Manager funguje podle očekávání.

Další informace o řešení problémů se nepovedlo kontroly stavu, najdete v článku [řešení potíží s Degradovaným stavem Azure Traffic Manageru](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Další postup

Přečtěte si [jak funguje Traffic Manager](traffic-manager-how-it-works.md)

Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager

Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-manage-profiles.md)

[Řešení potíží se stavem snížený](traffic-manager-troubleshooting-degraded.md) na koncový bod služby Traffic Manager
