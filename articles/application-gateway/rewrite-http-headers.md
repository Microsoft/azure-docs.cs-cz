---
title: Přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje přehled funkce přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: 20c484779e7ffe74ae01e33472b4cf8761d81b66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682676"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Přepsání hlavičky protokolu HTTP pomocí služby Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hlavičky protokolu HTTP umožňují klienta a serveru předat další informace o požadavku nebo odpovědi. Přepisování tyto hlavičky protokolu HTTP vám pomůže splnit několik důležitých scénářů, jako je například přidávání pole hlavičky související se zabezpečením, jako jsou HSTS / X XSS ochranu, odebírá hlavičku odpovědi pole, které může odhalit citlivé informace, odebrat informace o portu Záhlaví X-předané-pro atd. Služba Application gateway podporuje možnost přidat, odebrat nebo aktualizovat hlaviček žádostí a odpovědí protokolu HTTP při požadavku a odpovědi pakety přesouvat mezi klientem a back-endové fondy. To poskytuje možnost přidání podmínky k zajištění, že určených hlaviček jsou zapsány pouze v případě, že jsou splněny určité podmínky. Funkce také podporuje několik [serverových proměnných](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) který pomoci ukládání dalších informací o požadavcích a odpovědích, a tím umožňuje vytvořit pravidla pro přepis výkonné.
> [!NOTE]
>
> Podpora přepsání hlavičky protokolu HTTP je dostupná jenom pro [novou skladovou Položku [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

![Přepsání hlavičky](media/rewrite-http-headers/rewrite-headers.png)

## <a name="headers-supported-for-rewrite"></a>Záhlaví podporována pro přepsání

Možnost umožňuje přepsat všechny hlavičky v požadavku a odpovědi blokování hlavičky hostitele, připojení a Upgrade. Application gateway můžete také použít k vytvoření vlastní hlavičky a přidat je do požadavku a odpovědi směrovány přes něj. 

## <a name="rewrite-conditions"></a>Přepište podmínky

Pomocí přepsání, které podmínky můžete vyhodnotit obsah HTTP (S) požadavků a odpovědí a provedení hlavičku přepsat jenom v případě, že jeden nebo více podmínek jsou splněny. Následující 3 typy proměnných se používají ve službě application gateway k vyhodnocení obsahu žádosti HTTP (S) a odpovědi:

- Hlavičky protokolu HTTP v požadavku
- Hlavičky protokolu HTTP v odpovědi
- Application gateway serverových proměnných

Podmínku můžete použít k vyhodnocení, zda určená proměnná je k dispozici, zda zadaná proměnná přesně odpovídá konkrétní hodnotu nebo zda zadané proměnné přesně odpovídá určitému vzoru. [Knihovna jazyka Perl kompatibilní regulární výrazy (PCRE)](https://www.pcre.org/) slouží k implementaci porovnávání regulárních výrazů v podmínkách. Další informace o syntaxi regulárního výrazu, najdete v článku [regulární výrazy jazyka Perl člověk stránky](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akce revize

Přepište akce se používají k určení, které chcete přepsat hlavičky požadavku a odpovědi a nové hodnoty, které musí být přepsány, aby původní záhlaví. Můžete vytvořit novou hlavičku, upravte hodnotu existující záhlaví nebo odstranit existující záhlaví. Hodnota záhlaví nové nebo existující záhlaví může být nastavena na následující typy hodnot:

- Text 
- Hlavička požadavku: Pokud chcete zadat hlavičku požadavku, je potřeba použijte syntaxi {http_req_*headerName*}
- Hlavička odpovědi: Pokud chcete zadat hlavičku odpovědi, je potřeba použijte syntaxi {http_resp_*headerName*}
- Proměnná serveru: Pokud chcete zadat proměnnou serveru, je potřeba použijte syntaxi {var_*serverVariable*}
- Kombinaci textu, hlavičku požadavku, hlavička odpovědi a proměnné serveru.

## <a name="server-variables"></a>Serverové proměnné

Služba Application gateway používá proměnné serveru k ukládání užitečné informace o serveru, připojení s klientem a aktuální požadavek na připojení, jako je například IP adresa klienta nebo typ webového prohlížeče. Tyto proměnné změnit dynamicky, například při načtení nové stránky nebo odeslání formuláře. Tyto proměnné serveru můžete použít k vyhodnocení podmínky revize a přepište záhlaví. 

Služba Application gateway podporuje následující proměnné na serveru:

| Podporované serverové proměnné | Popis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Obsahuje "X-předané-pro" klienta polem hlavičky požadavku `client_ip` (vysvětlení obsahuje tato tabulka dole) proměnné připojí k němu ve formátu (IP1, IP2, IP3,...). Pokud není k dispozici v hlavičce žádosti klienta, pole "X-předané-pro" `add_x_forwarded_for_proxy` proměnná je rovna `$client_ip` proměnné. Tato proměnná je zvláště užitečné ve scénářích, kde chcete přepsat nastavení službou Application Gateway, Hlavička X-předané – pro tak, že hlavička obsahuje jenom IP adresy bez informace o portech zákazníků. |
| ciphers_supported          | Vrátí seznam šifer podporovaných klientem          |
| ciphers_used               | Vrátí řetězec šifry použitý pro navázané připojení SSL |
| client_ip                  | IP adresa klienta, ze kterého application gateway přijal požadavek. Pokud není reverzní proxy server před application gateway a klienta, pak *client_ip* vrátí IP adresu reverzního proxy serveru. |
| client_port                | port klienta                                                  |
| client_tcp_rtt             | informace o klientovi připojení TCP. k dispozici v systémech, které podporují možnost soketu TCP_INFO |
| client_user                | Pokud používáte ověřování pomocí protokolu HTTP, uživatelské jméno zadané pro ověřování |
| hostitel                       | v tomto pořadí priorit: název hostitele z řádku požadavku nebo název hostitele z pole hlavičky požadavku "Hostitel", nebo odpovídající žádost o název serveru |
| cookie_*název*              | *název* souboru cookie                                            |
| http_method                | Metoda použitá k odeslání požadavku adresa URL. Třeba získáte POST atd. |
| http_status                | Stav relace, třeba: 200, 400, 403 atd.                       |
| http_version               | žádost o protokol, obvykle "HTTP verze 1.0", "HTTP/1.1" nebo "HTTP/2.0" |
| query_string               | seznam hodnota proměnné páry čísel, které následují "?" v požadované adrese URL. |
| received_bytes             | délka požadavku (včetně řádek požadavku, záhlaví a text žádosti) |
| request_query              | argumenty v řádku požadavku                                |
| request_scheme             | schéma požadavku, "http" nebo "https"                            |
| request_uri                | původní identifikátor URI požadavku (s argumenty)                   |
| sent_bytes                 | počet bajtů odeslaných do klienta                             |
| server_port                | port serveru, který žádost o přijetí                 |
| ssl_connection_protocol    | Vrátí protokol navázané připojení SSL        |
| ssl_enabled                | "na" if připojení funguje v režimu SSL, nebo v opačném případě prázdný řetězec |

## <a name="rewrite-configuration"></a>Přepsání konfigurace

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset:

1. Vytvořte nové objekty požadované pro přepsání hlavičky protokolu http:

   - **Přepsat akci**: používá se k určení žádosti a pole hlavičky požadavku, které chcete přepsat a nové hodnoty, které musí být přepsány, aby původní záhlaví. Můžete přidružit jeden nebo další revize podmínku akce přepsání.

   - **Přepsání stavu**: To je volitelné konfigurace. Když je přidána podmínka revize, se vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Rozhodnutí o přepsání akci spojené s podmínkou revize se budou zakládat, zda požadavek HTTP (S) nebo odpověď shoda s podmínkou revize. 

     Pokud více než jedné podmínky jsou přidružené k akci a potom akci spustí jenom v případě, že jsou splněny všechny podmínky, to znamená, logické a operace se provede.

   - **Přepsat pravidla**: pravidlo pro přepis adres obsahuje více akce přepsání - přepsat kombinace podmínku.

   - **Pravidlo pořadí**: pomáhá určit, jsou provedeny v pořadí, ve kterém různých pravidla pro přepis adres. To je užitečné, pokud existuje více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres s nižší hodnotou pořadí pravidlo se nejprve provede. Pokud poskytují stejnou sekvenci pravidlo na dvě pravidla pro přepis adres budou pořadí provádění není deterministický.

   - **Přepsat nastavení**: obsahuje více přepsání pravidla, která bude přidružen k pravidlo směrování požadavku.

2. Budete muset připojit přepsání sady (*rewriteRuleSet*) pomocí pravidel směrování. Je to proto, že konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky je přidružený zdroj naslouchací proces a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. Ano platí jen pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu http a každou sadu přepsání lze použít na víc naslouchacích procesů. Můžete však použít jen jeden přepsat nastavenou na konkrétním posluchačem.

## <a name="common-scenarios"></a>Obvyklé scénáře

Některé běžné scénáře, které vyžadují revize záhlaví jsou uvedené níže.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Odebrat informace o portech z hlavičky X-předané-pro

Služba Application gateway vloží záhlaví X-předané-pro všechny požadavky, předtím, než ji předá požadavek back-endu. Formát pro toto záhlaví je čárkou oddělený seznam IP: port. Mohou však existovat scénáře, ve kterém back-end serverů vyžadují záhlaví tak, aby obsahovala pouze IP adresy. Pro provádění takových scénářích, je možné odebrat informace o portech z hlavičky X-předané – pro záhlaví revize. Jedním ze způsobů k tomu je nastavit hlavičku add_x_forwarded_for_proxy proměnná serveru. 

![Odebrání portu](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Upravte adresu URL pro přesměrování

Když back-end aplikace odešle odpověď přesměrování, můžete k přesměrování klienta na jinou adresu URL, než je určeno back-end aplikace. Jeden takový scénář je při služby app service je hostovaných za službou application gateway a vyžaduje klienta provedete přesměrování na relativní cestu (přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2). 

Služby app service je víceklientská služba, používá ke směrování na správný koncový bod hlavičku hostitele v žádosti. Služby App services mají výchozí název domény *. azurewebsites.net (třeba contoso.azurewebsites.net), která se liší od application gateway název domény (třeba contoso.com). Vzhledem k tomu, že původní žádost od klienta má služba application gateway název domény contoso.com jako název hostitele, službu application gateway změní název hostitele contoso.azurewebsites.net, tak, aby app service může směrovat na správný koncový bod. Když služby app service odešle odpověď přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, které obdrží z aplikační brány. Klient proto způsobí, že žádost přímo contoso.azurewebsites.net/path2, místo používání přes application gateway (contoso.com/path2). Obcházení application gateway není žádoucí. 

Tento problém lze vyřešit nastavením název hostitele v záhlaví umístění pro službu application gateway název domény. K tomuto účelu můžete vytvořit pravidlo pro přepis adres s podmínkou, která vyhodnotí hlavičky location v odpovědi obsahuje azurewebsites.net tak, že zadáte-li `(https?):\/\/.*azurewebsites\.net(.*)$` jako vzor a provádějí akci, kterou chcete přepsat hlavičku location, aby služba application gateway název hostitele tak, že zadáte `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví.

![Upravit hlavičku umístění](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementace hlavičky zabezpečení HTTP, aby se zabránilo ohrožení zabezpečení

Implementací potřebné hlavičky v odezvě aplikace lze napravit několik ohrožení zabezpečení. Některé z těchto záhlaví zabezpečení jsou X XSS ochranu, striktní zabezpečení přenosu, obsah-Security-Policy, atd. Služba application gateway můžete použít k nastavení těchto hlaviček pro všechny odpovědi.

![Záhlaví zabezpečení](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Odstranit nechtěné záhlaví

Můžete chtít odebrat tyto hlavičky budou odebrány z odpovědi HTTP, který odhalí citlivé informace, jako je název serveru back-endu, operační systém, podrobnosti ke knihovně, atd. Application gateway můžete použít k jejich odebrání.

![Odstraňuje se záhlaví](media/rewrite-http-headers/remove-headers.png)

### <a name="check-presence-of-a-header"></a>Kontrola přítomnosti hlavičky.

Můžete si vyzkoušet hlavičky protokolu HTTP požadavku nebo odpovědi na přítomnost proměnnou záhlaví nebo serveru. To je užitečné, když máte v úmyslu provést přepis záhlaví jenom v případě, že některé hlavičky je k dispozici.

![Kontrola přítomnosti hlavičky.](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Omezení

- Přepsání hlavičky připojení, Upgrade a hostitel ještě nepodporuje.

- Názvy záhlaví může obsahovat jakékoli alfanumerickým znakem a konkrétní symboly definované v [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Však aktuálně nepodporujeme "podtržítka" (\_) speciálního znaku v názvu záhlaví. 

## <a name="need-help"></a>Potřebujete pomoc?

Kontaktujte nás na adrese [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) v případě, že potřebujete pomoc s tuto funkci.

## <a name="next-steps"></a>Další postup

Zjistěte, jak přepsat hlavičky protokolu HTTP, najdete v tématech:

-  [Přepsání hlavičky protokolu HTTP pomocí webu Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Přepsání hlavičky protokolu HTTP pomocí Azure Powershellu](add-http-header-rewrite-rule-powershell.md)
