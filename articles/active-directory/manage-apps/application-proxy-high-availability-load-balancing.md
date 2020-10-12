---
title: Vysoká dostupnost a vyrovnávání zatížení – Azure Proxy aplikací služby AD
description: Jak funguje distribuce provozu s vaším nasazením proxy aplikací. Obsahuje tipy pro optimalizaci výkonu konektoru a použití vyrovnávání zatížení pro back-endové servery.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab3b340654fd6d824edef0a33d1ea363a913654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764583"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikací

Tento článek vysvětluje, jak funguje distribuce provozu s vaším nasazením proxy aplikací. Probereme tato témata:

- Způsob distribuce provozu mezi uživateli a konektory spolu s tipy pro optimalizaci výkonu konektoru

- Jak přenos toků mezi konektory a back-end aplikační servery s doporučeními pro vyrovnávání zatížení mezi několika back-endové servery

## <a name="traffic-distribution-across-connectors"></a>Distribuce provozu mezi konektory

Konektory vytvářejí svá připojení na základě zásad pro vysokou dostupnost. Není nijak zaručeno, že provoz bude vždycky rovnoměrně rozložen mezi konektory a neexistuje spřažení relací. Použití se ale liší a požadavky se náhodně odesílají do instancí služby proxy aplikací. V důsledku toho je provoz obvykle distribuován v konektorech téměř rovnoměrně. Diagram a kroky níže znázorňují způsob, jakým jsou mezi uživateli a konektory navázány připojení.

![Diagram znázorňující připojení mezi uživateli a konektory](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Uživatel v klientském zařízení se pokusí o přístup k místní aplikaci publikované prostřednictvím proxy aplikací.
2. Požadavek prochází Azure Load Balancer k určení, která instance služby proxy aplikace by měla požadavek přijmout. Pro každou oblast je k přijetí žádosti k dispozici desítka instancí. Tato metoda pomáhá rovnoměrně distribuovat provoz napříč instancemi služby.
3. Požadavek se odešle do [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Service Bus signály k dostupnému konektoru. Konektor pak vybere požadavek od Service Bus.
   - V kroku 2 požadavky přecházejí na různé instance služby proxy aplikací, takže připojení jsou pravděpodobně vytvořena pomocí různých konektorů. V důsledku toho se konektory skoro v rámci skupiny používají téměř rovnoměrně.
5. Konektor předá požadavek back-end serveru aplikace. Pak aplikace pošle odpověď zpátky do konektoru.
6. Konektor dokončí odpověď otevřením odchozího připojení k instanci služby, ze které pochází požadavek. Pak se toto připojení okamžitě zavře. Ve výchozím nastavení je každý konektor omezený na 200 současných odchozích připojení.
7. Odpověď je pak předána zpátky klientovi z instance služby.
8. Následné požadavky ze stejného připojení opakují výše uvedené kroky.

Aplikace má často mnoho prostředků a při jejich načítání otevírá několik připojení. Každé připojení prochází pomocí výše uvedených kroků, aby bylo možné přidělit instanci služby, vyberte nový dostupný konektor, pokud připojení ještě dříve nebylo spárováno s konektorem.


## <a name="best-practices-for-high-availability-of-connectors"></a>Osvědčené postupy pro vysokou dostupnost konektorů

- Vzhledem k tomu, jak je přenos distribuován mezi konektory pro vysokou dostupnost, je nezbytné vždy mít ve skupině konektorů alespoň dva konektory. Tři konektory jsou upřednostňovány k poskytování další vyrovnávací paměti mezi konektory. K určení správného počtu konektorů, které jste potřebovali, použijte dokumentaci pro plánování kapacity.

- Umístěte konektory na různá odchozí připojení, abyste se vyhnuli jedinému bodu selhání. Pokud konektory používají stejné odchozí připojení, může mít problém se sítí s připojením vliv na všechny konektory, které ji používají.

- Vyhněte se restartování konektorů při připojení k produkčním aplikacím. To by mohlo mít negativní vliv na distribuci provozu mezi konektory. Restartování konektorů způsobí nedostupnost více konektorů a vynutí připojení k zbývajícímu dostupnému konektoru. Výsledkem je nerovnoměrné použití konektorů na začátku.

- Vyhněte se všem formám vložené kontroly na odchozí komunikaci TLS mezi konektory a Azure. Tento typ vložené kontroly způsobuje snížení toku komunikace.

- Ujistěte se, že jsou pro vaše konektory spuštěné automatické aktualizace. Pokud je spuštěná služba proxy aplikace Aktualizátor konektorů, vaše konektory se automaticky aktualizují a získají nejnovější upgrady. Pokud se služba Aktualizátor konektorů na serveru nezobrazuje, je nutné přeinstalovat konektor, aby se získaly nějaké aktualizace.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Přenosový tok mezi konektory a back-endové aplikační servery

Další klíčovou oblastí, kde je vysoká dostupnost, je spojení mezi konektory a back-endové servery. Když se aplikace publikuje prostřednictvím služby Azure Proxy aplikací služby AD, přejdou přenosy uživatelů na aplikace na tři segmenty směrování:

1. Uživatel se připojí ke veřejnému koncovému bodu služby Azure Proxy aplikací služby AD v Azure. Připojení se naváže mezi původní IP adresou klienta (veřejnou) a IP adresou koncového bodu proxy aplikace.
2. Konektor proxy aplikace vyžádá požadavek HTTP klienta ze služby proxy aplikace.
3. Konektor proxy aplikace se připojí k cílové aplikaci. Konektor používá k navázání připojení vlastní IP adresu.

![Diagram uživatele připojujícího se k aplikaci prostřednictvím proxy aplikace](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Hledisko předávaného X-pro pole záhlaví
V některých situacích (jako je auditování, Vyrovnávání zatížení atd.) znamená, že sdílení původní IP adresy externího klienta s místním prostředím je požadavek. Za účelem vyřešení požadavku konektor Azure Proxy aplikací služby AD přidá do požadavku protokolu HTTP pole s povýšenou hlavičkou X-pro s původní IP adresou klienta (Public). Příslušné síťové zařízení (Nástroj pro vyrovnávání zatížení, brána firewall) nebo webový server nebo aplikace back-end mohou číst a používat tyto informace.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Osvědčené postupy pro vyrovnávání zatížení mezi více aplikačními servery
Pokud má skupina konektorů přiřazená aplikaci proxy aplikace dva nebo více konektorů a používáte back-end webovou aplikaci na více serverech (serverové farmy), je nutná vhodná strategie vyrovnávání zatížení. Dobrá strategie zajišťuje, aby servery vybraly požadavky klientů rovnoměrně a zabránily nadměrnému nebo nevytížení serverů v serverové farmě.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scénář 1: aplikace back-end nevyžaduje trvalou relaci.
Nejjednodušším scénářem je, že back-end webová aplikace nevyžaduje relaci vytrvalost (trvalá relace). Jakoukoli žádost od uživatele může zpracovat jakákoli instance back-endové aplikace v serverové farmě. Můžete použít nástroj pro vyrovnávání zatížení vrstvy 4 a nakonfigurovat ho bez spřažení. Mezi možnosti patří služba Vyrovnávání zatížení sítě Microsoftu a Azure Load Balancer nebo nástroj pro vyrovnávání zatížení od jiného dodavatele. Alternativně je možné nakonfigurovat službu DNS pro kruhové dotazování.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scénář 2: back-end aplikace vyžaduje trvalou relaci.
V tomto scénáři vyžaduje back-end webová aplikace vytrvalost relace (trvalá relace) během ověřené relace. Všechny požadavky od uživatele musí být zpracovány instancí back-endové aplikace, která běží na stejném serveru v serverové farmě.
Tento scénář může být složitější, protože klient obvykle vytváří více připojení ke službě proxy aplikací. Žádosti přes různá připojení můžou přijít do různých konektorů a serverů ve farmě. Vzhledem k tomu, že každý konektor používá pro tuto komunikaci svou vlastní IP adresu, nemůže nástroj pro vyrovnávání zatížení zajistit, aby se relace vytrvalost na základě IP adresy konektorů. Spřažení zdrojové IP adresy nelze použít ani jednu z nich.
Tady je několik možností pro scénář 2:

- Možnost 1: Založte trvalou relaci pro soubory cookie relace nastavené nástrojem pro vyrovnávání zatížení. Tato možnost se doporučuje, protože umožňuje, aby bylo zatížení rovnoměrně roztaženo mezi back-end servery. K této funkci vyžaduje nástroj pro vyrovnávání zatížení vrstvy 7 a který může zpracovávat přenosy HTTP a ukončit připojení TLS. Můžete použít Azure Application Gateway (spřažení relace) nebo nástroj pro vyrovnávání zatížení od jiného dodavatele.

- Možnost 2: Založte trvalost relace pro pole záhlaví X-předané. Tato možnost vyžaduje, aby nástroj pro vyrovnávání zatížení vrstvy 7 s touto funkcí byl a mohl zpracovávat přenosy HTTP a ukončit připojení TLS.  

- Možnost 3: Nakonfigurujte back-end aplikaci tak, aby nevyžadovala trvalost relace.

Informace o požadavcích na Vyrovnávání zatížení back-endové aplikace najdete v dokumentaci od dodavatele softwaru.

## <a name="next-steps"></a>Další kroky

- [Povolit proxy aplikací](application-proxy-add-on-premises-application.md)
- [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
- [Povolit podmíněný přístup](application-proxy-integrate-with-sharepoint-server.md)
- [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)
- [Přečtěte si, jak architektura Azure AD podporuje vysokou dostupnost](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
