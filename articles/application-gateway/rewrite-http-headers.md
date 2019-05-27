---
title: Přepsání hlavičky protokolu HTTP s Azure Application Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje základní informace o přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: ebb14d97273851585e491e3bcd36f776ec9b61b4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000977"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Přepsání hlavičky protokolu HTTP pomocí služby Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hlavičky protokolu HTTP umožňují klientem a serverem pro předávání dalších informací s požadavku nebo odpovědi. Pomocí přepsání tyto hlavičky, lze provádět důležité úkoly, jako je například pole hlavičky související se zabezpečením, jako HSTS / X-XSS ochrany přidání, odebrání pole hlavičky odpovědi, které může odhalit citlivé informace a odebírá se informace o portu X-předané – pro hlavičky.

Služba Application Gateway umožňuje přidat, odebrat nebo aktualizovat hlaviček žádostí a odpovědí protokolu HTTP při požadavku a odpovědi pakety přesunout mezi klientem a back endové fondy. A umožňuje přidání podmínky k zajištění, že určených hlaviček jsou zapsány pouze v případě, že jsou splněny určité podmínky.

Služba Application Gateway také podporuje několik [serverových proměnných](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) umožňující ukládání dalších informací o požadavcích a odpovědích. To usnadňuje vytváření pravidla pro přepis výkonné.

> [!NOTE]
>
> Podpora přepsání hlavičky protokolu HTTP je dostupná jenom pro [Standard_V2 a WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Přepsání hlavičky](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Podporované záhlaví

Je možné přepsat všechny hlavičky požadavků a odpovědí, s výjimkou hlavičky hostitele, připojení a Upgrade. Application gateway můžete také použít k vytvoření vlastní hlavičky a přidat je do požadavky a odpovědi směrovány přes něj.

## <a name="rewrite-conditions"></a>Přepište podmínky

Přepište podmínky můžete použít k vyhodnocení obsah HTTP (S) požadavků a odpovědí a provedení revize záhlaví pouze v případě, že je jeden nebo více podmínek. Application gateway používá k vyhodnocení obsah HTTP (S) žádosti a odpovědi na tyto typy proměnných:

- Hlavičky protokolu HTTP v požadavku.
- Hlavičky protokolu HTTP v odpovědi.
- Application Gateway serverových proměnných.

Podmínku můžete použít k vyhodnocení, zda určená proměnná je k dispozici, zda zadaná proměnná se shoduje s konkrétní hodnotou nebo zda zadanou proměnnou odpovídá určitému vzoru. Můžete použít [Perl kompatibilní regulární výrazy (PCRE) knihovny](https://www.pcre.org/) nastavení porovnávání regulárních výrazů v podmínkách. Další informace o syntaxi regulárního výrazu, najdete v článku [hlavní stránka regulární výrazy jazyka Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akce revize

Pomocí přepsání akcí k určení hlaviček žádostí a odpovědí, které chcete přepsat a novou hodnotu hlavičky. Můžete buď vytvořit novou hlavičku, upravte hodnotu existující záhlaví nebo odstranit existující záhlaví. Hodnota záhlaví nové nebo existující záhlaví můžete nastavit na těchto typů hodnot:

- Text.
- Hlavička požadavku. Pokud chcete zadat hlavičku požadavku, budete muset použít syntaxe {http_req_*headerName*}.
- Hlavička odpovědi. Pokud chcete zadat hlavičku odpovědi, budete muset použít syntaxe {http_resp_*headerName*}.
- Proměnná serveru. Pokud chcete zadat proměnnou serveru, budete muset použít syntaxe {var_*serverVariable*}.
- Kombinace textu, hlavičku požadavku, hlavičky odpovědi a proměnné serveru.

## <a name="server-variables"></a>Serverové proměnné

Application Gateway používá proměnné serveru k ukládání užitečné informace o serveru, připojení s klientem a aktuální požadavek na připojení. Příklady informací uložených: adresa IP klienta a typ webového prohlížeče. Serverové proměnné dynamicky měnit, například při načtení nové stránky, nebo při odeslání formuláře. Tyto proměnné můžete použít k vyhodnocení podmínky revize a přepište záhlaví.

Služba Application gateway podporuje tyto proměnné serveru:

| Název proměnné | Popis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Pole hlavičky požadavku X-předané-pro klienta s `client_ip` proměnné (viz vysvětlení později v této tabulce) připojeným k němu ve formátu IP1, IP2, IP3 a tak dále. Není-li X-předané-pro pole v hlavičce žádosti klienta `add_x_forwarded_for_proxy` proměnná je rovna `$client_ip` proměnné. Tato proměnná je zvlášť užitečné, pokud chcete přepsat hlavičku X-předané – pro nastavit službou Application Gateway tak, aby záhlaví obsahuje jenom IP adresy bez informace o portech. |
| ciphers_supported          | Seznam šifer podporovaných klientem.          |
| ciphers_used               | Řetězec šifry použitý pro navázané připojení SSL. |
| client_ip                  | IP adresa klienta, ze kterého application gateway přijal požadavek. Pokud není reverzní proxy server před application gateway a klienta, *client_ip* vrátí IP adresu reverzního proxy serveru. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informace o klientovi připojení TCP. K dispozici v systémech, které podporují parametr TCP_INFO soketu. |
| client_user                | Při použití ověřování pomocí protokolu HTTP pro ověřování zadat uživatelské jméno. |
| host                       | V tomto pořadí priorit: název hostitele z řádku požadavek, název hostitele z pole hlavičky požadavku hostitele nebo odpovídající žádost o název serveru. |
| cookie_*název*              | *Název* souboru cookie.                                            |
| http_method                | Metoda použitá k odeslání požadavku adresa URL. Třeba získat nebo odeslat. |
| http_status                | Stav relace. Například 200, 400 nebo 403.                       |
| http_version               | Žádost o protokol. Obvykle HTTP verze 1.0, HTTP/1.1 nebo HTTP/2.0. |
| query_string               | Následující seznam dvojic proměnné/hodnota "?" v požadované adrese URL. |
| received_bytes             | Délka požadavku (včetně řádek požadavku, záhlaví a text požadavku). |
| request_query              | Argumenty v řádku požadavku.                                |
| request_scheme             | Schéma požadavku: http nebo https.                            |
| request_uri                | Původní identifikátor URI požadavku (s argumenty).                   |
| sent_bytes                 | Počet bajtů odeslaných do klienta.                             |
| server_port                | Port serveru, který je přijat požadavek.                 |
| ssl_connection_protocol    | Protokol navázané připojení SSL.        |
| ssl_enabled                | "Na" if připojení funguje v režimu SSL. V opačném případě prázdný řetězec. |

## <a name="rewrite-configuration"></a>Přepsání konfigurace

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset dokončit tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky protokolu HTTP:

   - **Přepsat akci**: Slouží k zadání požadavku a pole hlavičky požadavku, které chcete přepsat a novou hodnotu hlavičky. Můžete přidružit jeden nebo více přepsání pomocí akce přepsání.

   - **Přepsání stavu**: Volitelná konfigurace. Přepište podmínky vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Akce revize se vrátí požadavku HTTP (S) nebo odpovědi odpovídá podmínce revize.

     Pokud přiřadíte více než jednu podmínku akci, dojde k akci pouze v případě, že jsou splněné všechny podmínky. Operace je jinými slovy, logické a operace.

   - **Přepsat pravidla**: Obsahuje více akce přepsání / přepisování kombinace podmínku.

   - **Pravidlo pořadí**: Pomáhá určit pořadí, ve kterém spuštění pravidla pro přepis adres. Tato konfigurace je užitečný, pokud máte více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres, který má menší hodnotu pořadí pravidlo spustí první. Pokud přiřadíte stejnou sekvenci pravidlo na dvě pravidla pro přepis, pořadí spouštění je Nedeterministický.

   - **Přepsat nastavení**: Obsahuje více pravidla pro přepis adres, které mají být asociována s pravidlo směrování požadavku.

2. Připojení sady revize (*rewriteRuleSet*) do pravidel směrování. Konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky souvisí s naslouchacím procesem zdroj a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. V takovém případě platí jenom pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu HTTP a použije každé revize nastavena na víc naslouchacích procesů. Můžete ale použít pouze jeden přepsat nastavenou na konkrétní naslouchací proces.

## <a name="common-scenarios"></a>Obvyklé scénáře

Zde jsou uvedeny některé obvyklé scénáře pro použití přepsání hlavičky.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Odebrat informace o portech z hlavičky X-předané-pro

Application Gateway vloží hlavičku X-předané – pro všechny požadavky předtím, než ji předá požadavek back-endu. Tato hlavička je čárkou oddělený seznam IP porty. Může existovat scénáře, ve kterých back-end serverů, stačí záhlaví tak, aby obsahovala IP adresy. Záhlaví revize můžete odebrat informace o portech z hlavičky X-předané-pro. Jedním ze způsobů k tomu je nastavit hlavičku add_x_forwarded_for_proxy proměnná serveru:

![Odebrání portu](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Upravte adresu URL přesměrování

Když back endové aplikace odešle odpověď přesměrování, můžete chtít přesměrování klienta na jinou adresu URL, než je určeno back endové aplikace. Například můžete chtít provést při služby app service je hostovaných za službou application gateway a vyžaduje klienta provedete přesměrování na relativní cestu. (Například přesměrování z contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2.)

Protože App Service je víceklientská. to znamená, používá pro směrování požadavku na správný koncový bod hlavičku hostitele v žádosti. Služby App services mají výchozí název domény *. azurewebsites.net (třeba contoso.azurewebsites.net), které se liší od názvu domény application gateway (třeba contoso.com). Vzhledem k tomu, že původní žádost od klienta má application gateway název domény (contoso.com) jako název hostitele, změny aplikační brány contoso.azurewebsites.net název hostitele. Díky této změně tak, aby služby app service může směrovat žádosti na správný koncový bod.

Když služby app service odešle odpověď přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, které obdrží z aplikační brány. Klient proto způsobí, že žádost přímo contoso.azurewebsites.net/path2 místo používání přes application gateway (contoso.com/path2). Obcházení application gateway není žádoucí.

Tento problém můžete vyřešit tak, že nastavíte název hostitele v záhlaví umístění pro službu application gateway název domény.

Tady jsou kroky pro nahrazení název hostitele:

1. Vytvořte pravidlo pro přepis adres s podmínku, která vyhodnotí, jestli hlavičky location v odpovědi obsahuje azurewebsites.net. Zadejte vzorek `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Proveďte akci, kterou chcete přepsat hlavičku umístění tak, aby měl název hostitele pro službu application gateway. To udělat tak, že zadáte `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví.

![Upravit hlavičku umístění](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementace hlavičky zabezpečení HTTP, aby se zabránilo ohrožení zabezpečení

Několik ohrožení zabezpečení můžete vyřešit implementací potřebné hlavičky v odpovědi aplikace. Tyto hlavičky zabezpečení zahrnují X XSS ochranu, striktní zabezpečení přenosu a zásady zabezpečení obsahu. Služba Application Gateway můžete použít k nastavení těchto hlaviček pro všechny odpovědi.

![Záhlaví zabezpečení](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Odstranit nechtěné záhlaví

Můžete chtít odebrat hlavičky, které odhalit citlivé informace z odpovědi HTTP. Například můžete chtít odebrat informace, jako název back endového serveru, operační systém nebo podrobnosti ke knihovně. Application gateway můžete použít k odebrání těchto hlaviček:

![Odstraňuje se záhlaví](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Kontrola přítomnosti hlavičky.

Můžete si vyzkoušet požadavku nebo odpovědi hlavičku HTTP přítomnost proměnnou záhlaví nebo serveru. Toto testování je užitečné, když chcete provést přepis záhlaví jenom v případě, že některé hlavičky je k dispozici.

![Kontrola přítomnosti hlavičky.](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Omezení

- Přepsání hlavičky připojení, Upgrade a hostitel se momentálně nepodporuje.

- Názvy záhlaví může obsahovat libovolný alfanumerické znaky a symboly konkrétní jak jsou definovány v [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Aktuálně nepodporujeme podtržítko (\_) speciálního znaku v hlavičce názvy.

- Pokud odpověď má více záhlaví s názvem, potom přepsáním hodnotu jedné z těchto záhlaví způsobí vyřazením dalších hlaviček odpovědi.

## <a name="next-steps"></a>Další postup

Zjistěte, jak přepsat hlavičky protokolu HTTP, najdete v tématech:

- [Přepsání hlavičky protokolu HTTP pomocí webu Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Přepsání hlavičky protokolu HTTP pomocí Azure Powershellu](add-http-header-rewrite-rule-powershell.md)
