---
title: Vysoká dostupnost a vyrovnávání zatížení – proxy aplikace Azure AD
description: Jak funguje distribuce provozu s nasazením proxy aplikace. Obsahuje tipy, jak optimalizovat výkon konektoru a použít vyrovnávání zatížení pro back-endové servery.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481309"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikací

Tento článek vysvětluje, jak funguje distribuce provozu s nasazením proxy aplikace. Probereme to takto:

- Jak je provoz distribuován mezi uživatele a konektory, spolu s tipy pro optimalizaci výkonu konektoru

- Jak se toky provozu mezi konektory a servery back-end aplikací, s doporučeními pro vyrovnávání zatížení mezi více back-end servery

## <a name="traffic-distribution-across-connectors"></a>Rozložení provozu mezi konektory

Konektory vytvořit jejich připojení na základě zásad pro vysokou dostupnost. Neexistuje žádná záruka, že provoz bude vždy rovnoměrně rozloženmezi konektory a neexistuje žádná spřažení relace. Použití se však liší a požadavky jsou náhodně odesílány do instancí služby Proxy aplikace. V důsledku toho je provoz obvykle distribuován téměř rovnoměrně mezi konektory. Následující diagram a kroky znázorňují, jak jsou navázána připojení mezi uživateli a konektory.

![Diagram znázorňující připojení mezi uživateli a konektory](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Uživatel na klientském zařízení se pokusí získat přístup k místní aplikaci publikované prostřednictvím proxy aplikace.
2. Požadavek prochází Azure Balancer k určení, která instance služby Proxy aplikace by měla přijmout požadavek. Podle oblasti jsou k dispozici desítky instancí, které mohou požadavek přijmout. Tato metoda pomáhá rovnoměrně distribuovat provoz mezi instancemi služby.
3. Požadavek je odeslán do [služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Service Bus zkontroluje, zda připojení dříve používalo existující konektor ve skupině konektorů. Pokud ano, znovu použije připojení. Pokud ještě není s připojením spárován žádný konektor, vybere náhodně dostupný konektor, ke kterým bude signalizovat. Konektor pak vyzvedne požadavek z service bus.

   - V kroku 2 požadavky přejít na různé instance služby proxy aplikace, takže připojení jsou pravděpodobnější, že budou provedeny s různými konektory. V důsledku toho jsou konektory téměř rovnoměrně používány v rámci skupiny.

   - Připojení je obnoveno pouze v případě, že je připojení přerušeno nebo dojde k době nečinnosti 10 minut. Například připojení může být přerušeno při restartování služby počítače nebo konektoru nebo při přerušení sítě.

5. Konektor předá požadavek na server back-end aplikace. Potom aplikace odešle odpověď zpět do konektoru.
6. Konektor dokončí odpověď otevřením odchozí připojení k instanci služby, odkud přišel požadavek. Poté je toto připojení okamžitě uzavřeno. Ve výchozím nastavení je každý konektor omezen na 200 souběžných odchozích připojení.
7. Odpověď je pak předána zpět klientovi z instance služby.
8. Následné požadavky ze stejného připojení opakují výše uvedené kroky, dokud nebude toto připojení přerušeno nebo dokud nebude nečinné po dobu 10 minut.

Aplikace má často mnoho prostředků a otevře více připojení, když je načten. Každé připojení prochází výše uvedenými kroky, aby se stala přidělena instance služby, vyberte nový konektor k dispozici, pokud připojení ještě nebyla dříve spárována s konektorem.


## <a name="best-practices-for-high-availability-of-connectors"></a>Doporučené postupy pro vysokou dostupnost konektorů

- Vzhledem ke způsobu, jakým je provoz distribuován mezi konektory pro vysokou dostupnost, je nezbytné mít vždy alespoň dva konektory ve skupině konektorů. Tři konektory jsou upřednostňovány poskytnout další vyrovnávací paměť mezi konektory. Chcete-li zjistit správný počet konektorů, které potřebujete, postupujte podle dokumentace plánování kapacity.

- Umístěte konektory na různá odchozí připojení, abyste se vyhnuli jedinému bodu selhání. Pokud konektory používají stejné odchozí připojení, může mít problém se sítí s připojením vliv na všechny konektory, které jej používají.

- Vyhněte se vynucení restartování konektorů při připojení k produkčním aplikacím. To by mohlo negativně ovlivnit distribuci provozu mezi konektory. Restartování konektorů způsobí, že nebude k dispozici více konektorů a vynutí připojení k zbývající dostupné spojnici. Výsledkem je zpočátku nerovnoměrné použití konektorů.

- Vyhněte se všem formám inline kontroly odchozí komunikace TLS mezi konektory a Azure. Tento typ inline kontroly způsobí degradaci toku komunikace.

- Ujistěte se, že automatické aktualizace jsou spuštěny pro vaše konektory. Pokud je spuštěna služba Aktualizace konektoru proxy aplikace, vaše konektory se automaticky aktualizují a obdrží nejnovější inovaci. Pokud na serveru nevidíte službu Aktualizace konektoru, je třeba přeinstalovat konektor, abyste získali všechny aktualizace.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Tok provozu mezi konektory a servery back-end aplikací

Další klíčovou oblastí, kde je vysoká dostupnost faktorem, je připojení mezi konektory a servery back-end. Když je aplikace publikována prostřednictvím proxy aplikace Azure AD, provoz od uživatelů k aplikacím toky prostřednictvím tří směrování:

1. Uživatel se připojí k veřejné koncové službě Proxy aplikací Azure AD v Azure. Připojení je navázáno mezi původní IP adresou (veřejnou) klienta a IP adresou koncového bodu Proxy aplikace.
2. Konektor proxy aplikace vytáhne požadavek HTTP klienta ze služby Proxy aplikace.
3. Konektor proxy aplikace se připojuje k cílové aplikaci. Konektor používá vlastní IP adresu pro navázání připojení.

![Diagram připojení uživatele k aplikaci přes proxy aplikace](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Aspekty pole záhlaví X-Forwarded-For
V některých situacích (jako je auditování, vyrovnávání zatížení atd.) je sdílení původní IP adresy externího klienta s místním prostředím požadavkem. Chcete-li tento požadavek vyřešit, přidá konektor proxy aplikací Azure AD do požadavku HTTP pole záhlaví X-Forwarded-For s původní IP adresou klienta (veřejnou). Příslušné síťové zařízení (nástroj pro vyrovnávání zatížení, brána firewall) nebo webový server nebo back-endová aplikace pak mohou číst a používat informace.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Doporučené postupy pro vyrovnávání zatížení mezi více aplikačními servery
Pokud skupina konektorů, která je přiřazena k aplikaci Proxy aplikace, má dva nebo více konektorů a spouštějíte webovou aplikaci back-end na více serverech (serverová farma), je vyžadována dobrá strategie vyrovnávání zatížení. Dobrá strategie zajišťuje, že servery vyzvednout požadavky klientů rovnoměrně a zabraňuje nadměrnému nebo nedostatečnému využití serverů v serverové farmě.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scénář 1: Back-endová aplikace nevyžaduje trvalost relace
Nejjednodušší scénář je, kde back-end webové aplikace nevyžaduje relace lepivost (trvalost relace). Jakýkoli požadavek od uživatele může být zpracován libovolnou instancí back-endové aplikace v serverové farmě. Můžete použít vývyčnici zatížení vrstvy 4 a nakonfigurovat ji bez spřažení. Některé možnosti zahrnují služby Microsoft Network Load Balancing a Azure Load Balancer nebo nástroje pro vyrovnávání zatížení od jiného dodavatele. Alternativně lze nakonfigurovat službu DNS kruhového dotazování.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scénář 2: Back-endová aplikace vyžaduje trvalost relace
V tomto scénáři back-end webové aplikace vyžaduje relace lepivost (trvalá relace) během ověřené relace. Všechny požadavky od uživatele musí být zpracovány instancí back-endové aplikace, která běží na stejném serveru v serverové farmě.
Tento scénář může být složitější, protože klient obvykle vytvoří více připojení ke službě Proxy aplikace. Požadavky přes různá připojení může dorazit na různé konektory a servery ve farmě. Vzhledem k tomu, že každý konektor používá pro tuto komunikaci vlastní IP adresu, nemůže vyvratitelé zatížení zajistit lepivost relace na základě IP adresy konektorů. Zdrojová ip afinita nemůže být použita.
Zde jsou některé možnosti pro scénář 2:

- Možnost 1: Založte trvalost relace na souborcookie relace nastavený vykladačem zatížení. Tato možnost je doporučena, protože umožňuje rovnoměrněji rozložit zatížení mezi servery back-end. Vyžaduje nástroj pro vyrovnávání zatížení vrstvy 7 s touto funkcí, který dokáže zpracovat přenosy HTTP a ukončit připojení TLS. Můžete použít Azure Application Gateway (Session Affinity) nebo vyrovnávání zatížení od jiného dodavatele.

- Možnost 2: Založte trvalost relace na poli záhlaví X-Forwarded-For. Tato možnost vyžaduje nástroj pro vyrovnávání zatížení vrstvy 7 s touto funkcí, který dokáže zpracovat přenosy HTTP a ukončit připojení TLS.  

- Možnost 3: Nakonfigurujte aplikaci back-end tak, aby nevyžadovala trvalost relace.

Informace o požadavcích back-endové aplikace na vyrovnávání zatížení naleznete v dokumentaci dodavatele softwaru.

## <a name="next-steps"></a>Další kroky

- [Povolit proxy aplikace](application-proxy-add-on-premises-application.md)
- [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
- [Povolení podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md)
- [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)
- [Zjistěte, jak architektura Azure AD podporuje vysokou dostupnost](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
