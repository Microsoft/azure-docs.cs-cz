---
title: Přepsat hlavičky HTTP pomocí Azure Application Gateway | Microsoft Docs
description: Tento článek poskytuje přehled o přepisu hlaviček HTTP v Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: fb5196f9612cb4ce1f0a49be8b5a76f6703fdab6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85248669"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Přepsat hlavičky HTTP pomocí Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hlavičky protokolu HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsáním těchto hlaviček můžete provádět důležité úkoly, jako je například přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS/X-XSS-Protection, odebrání polí hlaviček odpovědi, které mohou odhalit citlivé informace, a odebrání informací o portech z předávaných hlaviček X-pro.

Když se pakety požadavků a odpovědí pohybují mezi klientem a back-endovými fondy, služba Application Gateway umožňuje přidat, odebrat nebo aktualizovat hlavičky požadavků a odpovědí HTTP. Umožňuje také přidat určité podmínky, které zajistí, že se zadané hlavičky přepíší pouze při splnění těchto podmínek.

Application Gateway také podporuje několik [proměnných serveru](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) , které vám pomůžou ukládat Další informace o žádostech a odpovědích. To usnadňuje vytváření výkonných pravidel pro přepis.

> [!NOTE]
>
> Podpora přepisování hlaviček protokolu HTTP je k dispozici pouze pro [SKU Standard_V2 a WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Přepis hlaviček](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Podporované hlavičky

V žádostech a odpovědích můžete přepsat všechna záhlaví s výjimkou hlaviček hostitele, připojení a upgradu. Aplikační bránu můžete použít také k vytvoření vlastních hlaviček a jejich přidání do požadavků a odpovědí, které jsou prostřednictvím ní směrovány.

## <a name="rewrite-conditions"></a>Podmínky přepisu

Podmínky přepsání můžete použít k vyhodnocení obsahu požadavků HTTP (S) a k vyhodnocování odpovědí a přepisování hlaviček, pokud je splněna jedna nebo více podmínek. Aplikační brána používá tyto typy proměnných k vyhodnocení obsahu požadavků HTTP (S) a odpovědí:

- Hlavičky HTTP v žádosti.
- Hlavičky HTTP v odpovědi.
- Application Gateway proměnných serveru.

Podmínku můžete použít k vyhodnocení, zda je zadaná proměnná přítomna, zda zadaná proměnná odpovídá konkrétní hodnotě nebo zda zadaná proměnná odpovídá konkrétnímu vzoru. K nastavení porovnávání vzorů regulárních výrazů v podmínkách použijete [knihovnu PCRE (s kompatibilitou regulárních výrazů) jazyka Perl](https://www.pcre.org/) . Další informace o syntaxi regulárních výrazů naleznete v [hlavní stránce regulární výrazy jazyka Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akce přepisu

Akce přepisu slouží k zadání hlaviček žádosti a odpovědi, které chcete přepsat, a nové hodnoty hlaviček. Můžete buď vytvořit novou hlavičku, upravit hodnotu existující hlavičky nebo odstranit existující hlavičku. Hodnota nového záhlaví nebo existující záhlaví lze nastavit na tyto typy hodnot:

- Textové.
- Hlavička žádosti Chcete-li zadat hlavičku požadavku, je nutné použít syntaxi {http_req_*header*}.
- Hlavička odpovědi. Chcete-li zadat hlavičku odpovědi, je nutné použít syntaxi {http_resp_*header*}.
- Proměnná serveru. Pokud chcete zadat proměnnou serveru, musíte použít syntaxi {var_*serverVariable*}.
- Kombinace textu, hlavičky požadavku, hlavičky odpovědi a serverové proměnné.

## <a name="server-variables"></a>Serverové proměnné

Application Gateway používá serverové proměnné k ukládání užitečných informací o serveru, připojení ke klientovi a aktuální žádosti o připojení. Příklady uložených informací zahrnují IP adresu klienta a typ webového prohlížeče. Proměnné serveru se dynamicky mění, například když se načte nová stránka nebo když se publikuje formulář. Tyto proměnné můžete použít k vyhodnocení podmínek přepisu a přepisu hlaviček. Aby bylo možné použít hodnotu proměnných serveru k přepsání hlaviček, bude nutné zadat tyto proměnné v syntaxi {var_*serverVariable*}.

Application Gateway podporuje tyto proměnné serveru:

| Název proměnné | Popis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Pole hlavičky žádosti klienta s přesměrováním do `client_ip` proměnné (viz vysvětlení později v této tabulce), ke kterému se připojuje ve formátu IP1, IP2, IP3 a tak dále. Pokud pole s přesměrováním X není v hlavičce žádosti klienta, `add_x_forwarded_for_proxy` proměnná je rovna `$client_ip` proměnné. Tato proměnná je užitečná hlavně v případě, že chcete přepsat hlavičku, kterou předáváte X-pro nastavenou Application Gateway tak, aby hlavička obsahovala jenom IP adresu bez informací o portu. |
| ciphers_supported          | Seznam šifr podporovaných klientem.          |
| ciphers_used               | Řetězec šifr používaných pro navázáné připojení TLS. |
| client_ip                  | IP adresa klienta, ze kterého služba Application Gateway přijala požadavek. Pokud před aplikační bránou a původním klientem dojde k reverznímu proxy serveru, *client_ip* vrátí IP adresu reverzního proxy serveru. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informace o připojení TCP klienta. K dispozici v systémech, které podporují možnost soketu TCP_INFO. |
| client_user                | Když se použije ověřování protokolem HTTP, uživatelské jméno zadané pro ověřování. |
| Hostitel                       | V tomto pořadí priorit: název hostitele z řádku žádosti, název hostitele z pole Hlavička požadavku hostitele nebo název serveru, který odpovídá požadavku. Příklad: *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* hodnota host bude v požadavku *contoso.com* |
| *název* cookie_              | *Název* souboru cookie.                                            |
| http_method                | Metoda použitá pro vytvoření žádosti adresy URL Například GET nebo POST. |
| http_status                | Stav relace. Například 200, 400 nebo 403.                       |
| http_version               | Protokol žádosti. Obvykle HTTP/1.0, HTTP/1.1 nebo HTTP/2.0. |
| query_string               | Seznam párů proměnných/hodnot, které následují po "?" v požadované adrese URL. Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING hodnota *ID = 123&title = Fabrikam* |
| received_bytes             | Délka požadavku (včetně řádku žádosti, hlavičky a textu žádosti) |
| request_query              | Argumenty na řádku požadavku.                                |
| request_scheme             | Schéma žádosti: http nebo HTTPS.                            |
| request_uri                | Úplný identifikátor URI původní žádosti (s argumenty). Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI hodnota */article.aspx? ID = 123&title = Fabrikam*   |
| sent_bytes                 | Počet bajtů odeslaných klientovi.                             |
| server_port                | Port serveru, který přijal požadavek.                 |
| ssl_connection_protocol    | Protokol vytvořeného připojení TLS.        |
| ssl_enabled                | "On", pokud připojení funguje v režimu TLS. V opačném případě prázdný řetězec. |
| uri_path                   | Identifikuje konkrétní prostředek v hostiteli, ke kterému chce webový klient získat přístup. Toto je část identifikátoru URI požadavku bez argumentů. Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* hodnota uri_path */article.aspx*  |

## <a name="rewrite-configuration"></a>Přepsat konfiguraci

Chcete-li nakonfigurovat přepsání hlaviček protokolu HTTP, je nutné provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlaviček protokolu HTTP:

   - **Akce přepisu**: slouží k zadání polí hlavičky Request a Request, která chcete přepsat, a nové hodnoty hlaviček. K akci přepsání můžete přidružit jednu nebo více podmínek přepsání.

   - **Podmínka přepisu**: volitelná konfigurace. Podmínky přepisu vyhodnocují obsah požadavků a odpovědí HTTP (S). Akce přepisu nastane, pokud požadavek nebo odpověď HTTP (S) odpovídá podmínce přepsání.

     Pokud k akci přiřadíte více než jednu podmínku, bude akce provedena pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická a operace.

   - **Pravidlo přepisu**: obsahuje více kombinací podmínka pro akci přepisu nebo přepisu.

   - **Pořadí pravidel**: pomáhá určit pořadí, ve kterém se pravidla přepsání spouštějí. Tato konfigurace je užitečná, když máte více pravidel pro přepsání v sadě přepsání. Jako první se spustí pravidlo pro přepsání s nižší hodnotou pořadí pravidel. Pokud přiřadíte stejné pořadí pravidel ke dvěma pravidlům přepisu, pořadí spuštění je nedeterministické.

   - **Sada přepsání**: obsahuje více pravidel pro přepis, která budou přidružena k pravidlu směrování požadavků.

2. Připojte sadu přepište (*rewriteRuleSet*) do pravidla směrování. Konfigurace přepsání je připojena ke zdroji naslouchání prostřednictvím pravidla směrování. Použijete-li pravidlo základního směrování, je konfigurace přepsání hlaviček přidružena ke zdrojovému naslouchacího procesu a je přepsána globální hlavičkou. Když použijete pravidlo směrování na základě cesty, konfigurace opětovného zápisu hlaviček se definuje na mapě cesty URL. V takovém případě platí pouze pro konkrétní oblast cesty lokality.
   > [!NOTE]
   > Přepsání adresy URL – změna hlaviček; nemění adresu URL pro cestu.

Můžete vytvořit více sad pro přepsání hlaviček protokolu HTTP a použít každou sadu přepsání na více posluchačů. Můžete ale použít jenom jednu sadu přepsaného zápisu na konkrétní naslouchací proces.

## <a name="common-scenarios"></a>Typické scénáře

Tady je několik běžných scénářů použití přepisu hlaviček.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Odebrat informace o portu ze záhlaví X-Transported – pro

Application Gateway vloží do všech požadavků hlavičku X s přesměrováním na všechny požadavky předtím, než přepošle požadavky do back-endu. Tato hlavička je čárkami oddělený seznam portů IP. Můžou nastat situace, kdy back-end servery potřebují jenom hlavičky obsahující IP adresy. Chcete-li odebrat informace o portu ze záhlaví X-Transported, můžete použít možnost přepisování hlaviček. To lze provést tak, že nastavíte hlavičku na proměnnou serveru add_x_forwarded_for_proxy:

![Odebrat port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Úprava adresy URL pro přesměrování

Když aplikace back-end pošle odezvu přesměrování, může být vhodné přesměrovat klienta na jinou adresu URL, než která je určena v back-endové aplikaci. Můžete to třeba udělat, když je služba App Service hostována za aplikační bránou a vyžaduje, aby klient provedl přesměrování na jeho relativní cestu. (Například přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2.)

Vzhledem k tomu, že App Service je víceklientské služba, používá v žádosti hlavičku hostitele ke směrování požadavku na správný koncový bod. App Services mají výchozí název domény *. azurewebsites.net (řekněme contoso.azurewebsites.net), který se liší od názvu domény služby Application Gateway (řekněme contoso.com). Vzhledem k tomu, že původní požadavek od klienta má název domény služby Application Gateway (contoso.com) jako název hostitele, služba Application Gateway změní název hostitele na contoso.azurewebsites.net. Tato změna tuto změnu provede, aby služba App Service mohla požadavek směrovat do správného koncového bodu.

Když služba App Service pošle odezvu přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, kterou přijímá z aplikační brány. Proto klient provede požadavek přímo na contoso.azurewebsites.net/path2 namísto průchodu přes Aplikační bránu (contoso.com/path2). Obcházení aplikační brány není žádoucí.

Tento problém můžete vyřešit tak, že nastavíte název hostitele v hlavičce umístění na název domény služby Application Gateway.

Tady je postup nahrazení názvu hostitele:

1. Vytvoří pravidlo přepsání s podmínkou, která vyhodnotí, jestli hlavička umístění v odpovědi obsahuje azurewebsites.net. Zadejte vzor `(https?):\/\/.*azurewebsites\.net(.*)$` .
1. Provedením akce přepište hlavičku umístění tak, aby měla název hostitele služby Application Gateway. To uděláte tak, že zadáte `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` hodnotu hlavičky.

![Změnit hlavičku umístění](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementace hlaviček protokolu HTTP zabezpečení, aby se předešlo ohrožení zabezpečení

Pomocí nezbytných hlaviček v odpovědi aplikace můžete opravit několik chyb zabezpečení. Tyto hlavičky zabezpečení zahrnují zásady X-XSS-Protection, striktního přenosu a zabezpečení obsahu. K nastavení těchto hlaviček pro všechny odpovědi můžete použít Application Gateway.

![Záhlaví zabezpečení](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Odstranit nechtěné hlavičky

Možná budete chtít odebrat hlavičky, které odhalí citlivé informace z odpovědi HTTP. Například můžete chtít odebrat informace, jako je název back-end serveru, operační systém nebo podrobnosti knihovny. K odebrání těchto hlaviček můžete použít Aplikační bránu:

![Odstraňuje se záhlaví](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Zkontroluje přítomnost záhlaví.

Pro přítomnost záhlaví nebo serverové proměnné můžete vyhodnotit hlavičku požadavku nebo odpovědi HTTP. Toto vyhodnocení je užitečné v případě, že chcete provést přepsání hlavičky pouze v případě, že je k dispozici určitá hlavička.

![Kontroluje se přítomnost hlavičky.](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Omezení

- Pokud má odpověď více než jednu hlavičku se stejným názvem, pak přepsání hodnoty jednoho z těchto hlaviček způsobí vyřazení ostatních hlaviček v odpovědi. K tomu obvykle dochází v Set-Cookie hlavičce, protože odpověď může obsahovat více než jednu Set-Cookie hlavičku. Takový scénář se používá v případě, že používáte službu App Service s aplikační bránou a máte nakonfigurovanou spřažení relací na základě souborů cookie na aplikační bránu. V takovém případě bude odpověď obsahovat dvě hlavičky Set-Cookie: jednu, kterou používá služba App Service, například: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` a další pro spřažení Application Gateway, například `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Přepsání jedné z hlaviček Set-Cookie v tomto scénáři může mít za následek odebrání druhé hlavičky Set-Cookie z odpovědi.

- Přepsání nejsou podporována, pokud je brána Application Gateway nakonfigurována pro přesměrování požadavků nebo zobrazení vlastní chybové stránky.

- Přepsání připojení, upgradu a hlaviček hostitele není v současné době podporováno.

- Názvy hlaviček můžou obsahovat libovolné alfanumerické znaky a specifické symboly, jak jsou definované v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). V názvech hlaviček Momentálně nepodporujeme \_ speciální znak podtržítka ().

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přepsat hlavičky HTTP, najdete tady:

- [Přepsání hlaviček HTTP s využitím webu Azure Portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Přepsat hlavičky HTTP pomocí Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
