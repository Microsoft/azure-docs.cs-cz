---
title: Monitorování koncového bodu v Azure Traffic Manageru | Microsoft Docs
description: Tento článek vám může pomoct pochopit, jak Traffic Manager používá pro Azure zákazníky, nasazení aplikací s vysokou dostupností monitorování koncového bodu a koncový bod automatické převzetí služeb při selhání
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: c54454dd2e7b56820834e4f3cd7452be10d5ddca
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorování koncového bodu Traffic Manageru

Azure Traffic Manager zahrnuje monitorování vestavěným koncovým bodem a koncový bod automatické převzetí služeb při selhání. Tato funkce umožňuje poskytovat vysokou dostupnost aplikace, které jsou odolné vůči selhání koncový bod, včetně selhání oblast Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurace monitorování koncového bodu

Konfigurace koncového bodu monitorování, zadejte následující nastavení na svůj profil Traffic Manageru:

* **Protokol**. Vyberte protokol HTTP, HTTPS nebo TCP jako protokol Traffic Manager používá při zjišťování váš koncový bod. Zkontrolujte jeho stav. Monitorování HTTPS neověřuje, že zda váš certifikát SSL není platný – pouze zkontroluje certifikát, který je přítomen.
* **Port**. Zvolte port používaný pro požadavek.
* **Cesta**. Toto nastavení konfigurace je platná pouze pro protokoly HTTP a HTTPS, pro které určení cesty nastavení je povinné. Poskytuje toto nastavení pro TCP monitorování protokolu dojde k chybě. Pro protokol TCP zadejte relativní cesta a název webovou stránku nebo soubor, který přistupuje k monitorování. Lomítko (/) je zadána platná hodnota relativní cesty. Tato hodnota znamená, zda je soubor v kořenovém adresáři (výchozí).
* **Zkušební fáze Interval**. Tato hodnota určuje, jak často se kontroluje koncový bod na jeho stav od agentů testování Traffic Manager. Můžete zadat sem dvě hodnoty: 30 sekund (normální zjišťování) a 10 sekund (rychlé zkušební fáze). Pokud jsou k dispozici žádné hodnoty, profil se nastaví na výchozí hodnota je 30 sekund. Přejděte [Traffic Manager cenová](https://azure.microsoft.com/pricing/details/traffic-manager) stránka Další informace o cenách rychlé testování.
* **Počet selhání tolerovat**. Tato hodnota určuje, kolik selhání agenta testování Traffic Manager toleruje před označením tohoto koncového bodu jako chybné. Jeho hodnota může být v rozsahu od 0 do 9. Hodnota 0 znamená selhání jedné monitorování může způsobit tohoto koncového bodu označen jako chybný. Pokud není zadaná žádná hodnota, použije se výchozí hodnota 3.
* **Časový limit monitorování**. Tato vlastnost určuje množství času, testování agenta Traffic Manager vyčkat před vzhledem k tomu, který zkontrolujte selhání při odeslání sondu kontrolu stavu ke koncovému bodu. Pokud Interval zjišťování je nastaven na hodnotu 30 sekund, můžete nastavit hodnotu časového limitu od 5 do 10 sekund. Pokud není zadaná žádná hodnota, použije se výchozí hodnota je 10 sekund. Pokud Interval zjišťování je nastaven na 10 sekund, můžete nastavit hodnotu časového limitu od 5 do 9 sekund. Pokud není zadaná žádná hodnota časového limitu, používá výchozí hodnotu 9 sekund.

![Monitorování koncového bodu Traffic Manageru](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Obrázek 1: Traffic Manager koncového bodu monitorování**

## <a name="how-endpoint-monitoring-works"></a>Jak funguje monitorování koncového bodu

Pokud monitorování protokol nastaven jako HTTP nebo HTTPS, testování agenta Traffic Manager provede požadavek GET na koncový bod pomocí protokol, port a relativní cesta zadána. Získá zpět odpověď 200 – OK, že koncový bod se považuje v pořádku. Pokud je odpověď na jinou hodnotu, nebo pokud není žádná odpověď v rámci časový limit zadaný pak Traffic Manager zjišťování, agent se pokusí opětovně navázat podle nastavení tolerovat počet selhání (znovu pokusí se provést v případě, toto nastavení je 0). Počet po sobě jdoucích selhání je vyšší než nastavení tolerovat počet selhání, tohoto koncového bodu je označen jako chybný. 

Pokud monitorování protokol TCP, testování agenta Traffic Manager zahájí žádost o připojení protokolu TCP pomocí zadaný port. Pokud koncový bod odpoví na žádost s odpověď k navázání připojení, kontroly stavu je označena jako úspěšné a testování agenta Traffic Manager obnoví připojení TCP. Pokud je odpověď na jinou hodnotu, nebo pokud není žádná odpověď v rámci časového limitu zadán, Traffic Manager zjišťování, agent se pokusí opětovně navázat podle nastavení tolerovat počet selhání (znovu pokusí probíhají Pokud toto nastavení je 0). Pokud počet po sobě jdoucích selhání je vyšší než nastavení tolerovat počet selhání, pak tohoto koncového bodu je označen jako chybný.

Ve všech případech Traffic Manager sondy z více umístění a se stane po sobě jdoucích selhání rozhodnutí v každé oblasti. Také to znamená, že koncových bodů obdrželi sondy stavu z Traffic Manager s frekvencí vyšší než nastavení používané pro Interval zjišťování.

>[!NOTE]
>Pro HTTP nebo HTTPS monitorování protokol je implementace vlastní stránky v rámci vaší aplikace – například /health.aspx běžnou praxí na straně koncový bod. Pomocí této cesty pro monitorování, můžete provést kontroly specifické pro aplikace, jako je kontrola čítače výkonu a ověřování databáze dostupnosti. Podle těchto vlastní kontroly, vrátí stránku odpovídající stavový kód HTTP.

Všechny koncové body v profilu Traffic Manageru sdílet nastavení monitorování. Pokud budete muset použít jiné nastavení monitorování pro různé koncové body, můžete vytvořit [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stav koncového bodu a profilu

Můžete povolit nebo zakázat koncové body a profily Traffic Manageru. Však ke změně stavu koncových bodů také může docházet k výsledku Traffic Manager automatizované procesy a nastavení.

### <a name="endpoint-status"></a>Stav koncového bodu

Můžete povolit nebo zakázat konkrétní koncový bod. Základní služby, která nemusí být ještě v pořádku, je poškozena. Změna stavu koncový bod řídí dostupnost koncového bodu v profil služby Traffic Manager. Pokud je koncový bod stav Zakázáno, Traffic Manager nekontroluje, jeho stav a koncový bod není zahrnutý v odpovědi DNS.

### <a name="profile-status"></a>Stav profilu

Nastavení stavu profilu můžete povolit nebo zakázat konkrétní profil. Při stav koncového bodu má vliv na jeden koncový bod, ovlivní stav profilu celého profilu, včetně všechny koncové body. Pokud zakážete profil, koncových bodů nejsou kontroly stavu a žádné koncové body jsou zahrnuty v odpovědi DNS. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) kód odpovědi se vrátí pro dotaz DNS.

### <a name="endpoint-monitor-status"></a>Stav monitorování koncových bodů

Stav monitorování koncového bodu je hodnota generované Traffic Manager, která zobrazuje stav koncového bodu. Nelze změnit, tato nastavení ručně. Sledujte stav koncového bodu je kombinací výsledky monitorování koncového bodu a stav nakonfigurovaný koncový bod. V následující tabulce jsou uvedeny možné hodnoty koncového bodu monitorování stavu:

| Stav profilu | Stav koncového bodu | Stav monitorování koncových bodů | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |Povoleno |Neaktivní |Profil je zakázané. I když je stav koncového bodu je povoleno, přednost má stav profilu (zakázáno). Koncové body v zakázaném profily nejsou monitorovány. Zadání kódu odpovědi NXDOMAIN se vrátí pro dotaz DNS. |
| &lt;všechny&gt; |Zakázáno |Zakázáno |Koncový bod je zakázané. Zakázané koncových bodů nejsou monitorovány. Koncový bod není součástí odpovědí DNS, proto, že neobdrží provoz. |
| Povoleno |Povoleno |Online |Koncový bod je monitorována a je v pořádku. To je součástí odpovědí DNS a může přijímat provoz. |
| Povoleno |Povoleno |Sníženo |Monitorování kontroly stavu koncový bod se nedaří. Koncový bod není zahrnutý v odpovědi DNS a nepřijímá provoz. <br>"Výjimkou je, pokud jsou degradovány všechny koncové body, v takovém případě všechny z nich jsou považovány za má být vrácen v odpovědi na dotaz).</br>|
| Povoleno |Povoleno |CheckingEndpoint |Koncový bod je monitorována, ale výsledky první sondu ještě nebyly přijaty. CheckingEndpoint je dočasný stav, ke kterému dochází obvykle okamžitě po přidání nebo povolení koncového bodu v profilu. Koncový bod v tomto stavu je zahrnutý v odpovědi DNS a může přijímat provoz. |
| Povoleno |Povoleno |Zastaveno |Cloudové služby nebo webovou aplikaci, odkazující na koncový bod není spuštěna. Zkontrolujte nastavení cloudové služby nebo webovou aplikaci. To také může dojít, pokud koncový bod je typ vnořené koncového bodu a podřízené profilu je zakázán nebo je neaktivní. <br>Koncový bod ve stavu Zastaveno není monitorován. To není zahrnutý v odpovědi DNS a neobdrží provoz. Výjimkou je, pokud jsou degradovány všechny koncové body, v takovém případě všechny z nich bude považovat za má být vrácen v odpovědi na dotaz.</br>|

Podrobnosti o výpočtu stav monitorování koncových bodů pro vnořené koncové body najdete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

>[!NOTE]
> Koncový bod zastavit monitorování stavu může dojít v App Service, pokud webová aplikace neběží na vrstvě Standard nebo vyšší. Další informace najdete v tématu [Traffic Manager integrace s App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Stav monitorování profilu

Stav monitorování profilu je kombinací stav nakonfigurované profilu a koncového bodu monitorování stavu hodnoty pro všechny koncové body. Možné hodnoty jsou popsané v následující tabulce:

| Stav profilu (jak je nakonfigurováno) | Stav monitorování koncových bodů | Stav monitorování profilu | Poznámky |
| --- | --- | --- | --- |
| Zakázáno |&lt;všechny&gt; nebo profil s žádné koncové body definované. |Zakázáno |Profil je zakázané. |
| Povoleno |Je Degradovaný stav aspoň jeden koncový bod. |Sníženo |Zkontrolujte hodnoty stavu jednotlivých koncový bod k určení, které koncové body vyžadovat další pozornost. |
| Povoleno |Stav aspoň jeden koncový bod je Online. Žádné koncové body mají stav snížený. |Online |Služba přijímá provoz. Není vyžadována žádná další akce. |
| Povoleno |Stav aspoň jeden koncový bod je CheckingEndpoint. Ve stavu Online nebo snížený nejsou žádné koncové body. |CheckingEndpoints |Tento přechod stavu nastane, když profil Pokud vytvořené nebo povolené. První se kontroluje stav koncového bodu. |
| Povoleno |Stavy všech koncových bodů v profilu jsou zakázán nebo zastaven nebo v profilu nemá žádné koncové body definované. |Neaktivní |Žádné koncové body jsou aktivní, ale stále profilovou. |

## <a name="endpoint-failover-and-recovery"></a>Koncový bod převzetí služeb při selhání a obnovení

Traffic Manager pravidelně kontroluje stav každý koncový bod, včetně koncových bodů není v pořádku. Traffic Manager zjistí, že koncový bod se změní na v pořádku a přenese zpět do otočení.

Koncový bod je není v pořádku, pokud dojde k některé z následujících událostí:
- Pokud je monitorování protokol HTTP nebo HTTPS:
    - Bez 200 odezvu obdrží (včetně kódu různých 2xx nebo 301/302 přesměrování).
- Pokud je monitorování protokol TCP: 
    - Odpověď než ACK nebo SYN ACK byl přijat v reakci na žádost o SYNCHRONIZACI odeslaná správcem provoz k pokusu o navázání připojení.
- Časový limit. 
- Jakýkoli další připojení problém, což je koncový bod není právě dostupná.

Další informace o odstraňování potíží selhání kontroly najdete v tématu [řešení potíží s Degradovaný stav na Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Následující časová osa na obrázku 2 je podrobný popis monitorování procesu Traffic Manager koncového bodu, který má následující nastavení: monitorování protokolu, je HTTP, testování interval je 30 sekund, počet chyb. povolená je 3, hodnota časového limitu je 10 sekund a DNS TTL je 30 sekund.

![Traffic Manager pořadí převzetí služeb při selhání a navrácení služeb po obnovení koncového bodu](./media/traffic-manager-monitoring/timeline.png)

**Obrázek 2: Provoz manager koncový bod převzetí služeb při selhání a obnovení pořadí**

1. **GET**. Pro každý koncový bod Traffic Manager monitorování systému provede požadavek GET na cestě zadaný v nastavení monitorování.
2. **200 OK**. Systém monitorování očekává zprávu HTTP 200 OK má být vrácen do 10 sekund. Při přijetí této odpovědi, rozpozná, že služba je k dispozici.
3. **30 sekundách mezi kontrolami**. Kontrola stavu koncový bod se opakuje každých 30 sekund.
4. **Služba není k dispozici**. Služba nebude k dispozici. Traffic Manager nebude vědět, dokud další kontrolu stavu.
5. **Pokusí o přístup k cesta monitorování**. Systém monitorování provede požadavek GET, ale neobdrží odpověď během časového limitu 10 sekund (případně jiný 200 může být přijata odpověď). Zkusí další třikrát v intervalech 30 sekund. Pokud jeden z pokusů je úspěšné, počet pokusů se vynuluje.
6. **Nastavit stav snížený**. Po čtvrtém po sobě jdoucích selhání systém monitorování označuje stav koncového bodu není k dispozici jako Snížený.
7. **Provoz je přesměrováni do dalších koncových bodů**. Názvové servery DNS Traffic Manager se aktualizují a Traffic Manager už vrátí koncový bod v odpovědi na dotazy DNS. Nová připojení jsou směrované na jiných, k dispozici koncové body. Rekurzivní servery DNS a klienty DNS však může stále mezipaměti předchozí odpovědí DNS, které obsahují tento koncový bod. Klienti dále používat koncový bod do vypršení platnosti mezipaměti DNS. Jako mezipaměť DNS vyprší, klienti zkontrolujte nové dotazy DNS a jsou přesměrováni na různými koncovými body. Doba uložení do mezipaměti je řízena nastavením TTL v profil služby Traffic Manager, například 30 sekund.
8. **Kontroluje stav pokračovat**. Traffic Manager bude kontrolovat stav koncového bodu, když má stav snížený. Traffic Manager zjistí, když se koncový bod se vrátí do stavu.
9. **Služba přejde do režimu online**. Služba bude k dispozici. Koncový bod zachovává jeho stav snížený v Traffic Manageru, dokud monitorování systém provede jeho další kontrolu stavu.
10. **Přenosy do služby obnoví**. Správce provozu se odešle požadavek GET a obdrží odpověď 200 OK stav. Služba vrátila do stavu v pořádku. Traffic Manager názvové servery, které jsou aktualizovány a jejich začít možné předat název DNS služby v odpovědi DNS. Provoz vrátí ke koncovému bodu jako odpovědí uložených v mezipaměti DNS který vrací další koncové body platnost nevyprší a jako existující připojení k jiné koncové body budou ukončeny.

    > [!NOTE]
    > Protože Traffic Manager funguje na úrovni DNS, nelze ovlivnit existující připojení pro libovolný koncový bod. Když ho přesměruje přenosy mezi koncovými body (buď nastavení změněné profilu, nebo při převzetí služeb při selhání a navrácení služeb po obnovení), Traffic Manager určí, že nová připojení k koncové body k dispozici. Ostatní koncové body, může však i nadále přijímat přenosy přes existující připojení, dokud tyto relace jsou ukončeny. Pokud chcete povolit provoz na vyprazdňování z existující připojení, aplikace měli omezit dobu trvání relace použít s každý koncový bod.

## <a name="traffic-routing-methods"></a>Metody směrování provozu

Koncový bod má stav snížený, je už vrátila v reakci na dotazy DNS. Místo toho je alternativní koncový bod vybrali a vrácena. Metodu směrování provozu nastavenou v profilu Určuje, jak je zvolen alternativní koncový bod.

* **Priorita**. Koncové body představují seznam seřazený podle priority. První koncový bod k dispozici v seznamu se vždy vrátí. Pokud je Degradovaný stav koncový bod, je vrácena další dostupný koncový bod.
* **Vážené**. Žádný dostupný koncový bod je vybrán náhodně na základě jejich přiřazené váhu a váhu dalších dostupných koncových bodů.
* **Výkon**. Koncový bod nejbližší pro koncového uživatele se vrátí. Pokud tohoto koncového bodu není k dispozici, Traffic Manager přesune provozu do koncových bodů v další nejbližší oblast Azure. Plány alternativní převzetí služeb při selhání pro směrování provozu výkonu můžete nakonfigurovat pomocí [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Zeměpisná**. Mapovat k obsluze geografické umístění na základě dotazu požadavku koncového bodu je vrácena IP adresy. Pokud tohoto koncového bodu není k dispozici, jiný koncový bod nebude vybrán k převzetí služeb při selhání, protože geografické umístění lze mapovat pouze na jeden koncový bod v profilu (Další podrobnosti najdete v [– nejčastější dotazy](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Jako osvědčený postup, při použití geografické směrování doporučujeme zákazníkům používat vnořené profily Traffic Manageru s více než jeden koncový bod jako koncové body profilu.

Další informace najdete v tématu [metody směrování provozu Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedinou výjimkou je to normální chování směrování provozu dochází, pokud všechny vhodné koncové body degradovaném stavu. Díky Traffic Manager pokusí "best effort" a *odpoví, jako by všechny snížený stav koncových bodů ve skutečnosti ve stavu online*. Toto chování je vhodnější alternativu, která by mohla není v odpovědi DNS vrátit žádný koncový bod. Nejsou monitorovány zakázán nebo zastaven koncových bodů, proto nejsou považovány za vhodné pro provoz.
>
> Tento stav je obvykle způsobeny nesprávná konfigurace služby, jako například:
>
> * Seznam řízení přístupu [ACL] blokování kontroly stavu Traffic Manager.
> * Nesprávná konfigurace monitorování portu nebo protokol v profil správce provozu.
>
> Důsledků toto chování je, že pokud kontroly stavu Traffic Manager nejsou správně konfigurována, může se zdát, z provozu, jako když směrování Traffic Manager *je* funguje správně. Však v takovém případě převzetí služeb při selhání koncový bod nemůže dojít, které ovlivní celkové aplikace k dispozici. Je důležité zkontrolovat, že profil ukazuje Online stavu, není stav snížený. Online stav označuje, že kontroly stavu Traffic Manager funguje podle očekávání.

Další informace o řešení potíží se nezdařilo kontroly stavu, najdete v části [řešení potíží s Degradovaný stav na Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Další postup

Další informace [fungování Traffic Manager](traffic-manager-how-traffic-manager-works.md)

Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager

Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-manage-profiles.md)

[Řešení potíží s stav snížený](traffic-manager-troubleshooting-degraded.md) na koncový bod Traffic Manager
