---
title: Přepis záhlaví HTTP pomocí brány aplikace Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled přepisování hlaviček HTTP v Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132996"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Přepsání záhlaví PROTOKOLU HTTP pomocí brány aplikace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hlavičky protokolu HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsáním těchto záhlaví můžete provádět důležité úkoly, jako je například přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS/ X-XSS-Protection, odebrání polí záhlaví odpovědi, které by mohlo odhalit citlivé informace, a odebrání informací o portu ze záhlaví X-Forwarded-For.

Když se pakety požadavků a odpovědí pohybují mezi klientem a back-endovými fondy, služba Application Gateway umožňuje přidat, odebrat nebo aktualizovat hlavičky požadavků a odpovědí HTTP. Umožňuje také přidat určité podmínky, které zajistí, že se zadané hlavičky přepíší pouze při splnění těchto podmínek.

Aplikační brána také podporuje několik [proměnných serveru,](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) které vám pomohou ukládat další informace o požadavcích a odpovědích. To usnadňuje vytvoření výkonných pravidel pro přepis.

> [!NOTE]
>
> Podpora přepisu záhlaví PROTOKOLU HTTP je k dispozici pouze pro [Standard_V2 a WAF_v2 skladovou položku](application-gateway-autoscaling-zone-redundant.md).

![Přepisování záhlaví](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Podporovaná záhlaví

Můžete přepsat všechna záhlaví v požadavcích a odpovědích, s výjimkou záhlaví Hostitele, Připojení a Upgrade. Pomocí brány aplikace můžete také vytvořit vlastní záhlaví a přidat je k požadavkům a odpovědím, které jsou přes ni směrovány.

## <a name="rewrite-conditions"></a>Podmínky přepsání

Podmínky přepsání můžete použít k vyhodnocení obsahu požadavků a odpovědí HTTP(S) a provést přepsání záhlaví pouze v případě, že je splněna jedna nebo více podmínek. Aplikační brána používá tyto typy proměnných k vyhodnocení obsahu požadavků a odpovědí HTTP(S):

- Hlavičky PROTOKOLU HTTP v požadavku.
- Hlavičky HTTP v odpovědi.
- Proměnné serveru aplikační brány.

Podmínku můžete použít k vyhodnocení, zda je k dispozici zadaná proměnná, zda zadaná proměnná odpovídá určité hodnotě nebo zda zadaná proměnná odpovídá určitému vzoru. Knihovna [regulárních výrazů kompatibilních s perlem (PCRE)](https://www.pcre.org/) slouží k nastavení porovnávání vzorů regulárních výrazů v podmínkách. Informace o syntaxi regulárních výrazů naleznete na [hlavní stránce regulárních výrazů Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Přepsat akce

Akce přepsání slouží k určení hlaviček požadavků a odpovědí, které chcete přepsat, a nové hodnoty pro záhlaví. Můžete buď vytvořit novou hlavičku, upravit hodnotu existujícího záhlaví, nebo odstranit existující záhlaví. Hodnotu nové hlavičky nebo existující hlavičky lze nastavit na tyto typy hodnot:

- Text.
- Hlavička požadavku. Chcete-li zadat hlavičku požadavku, musíte použít syntaxi {http_req_*headerName*}.
- Hlavička odpovědi. Chcete-li zadat hlavičku odpovědi, musíte použít syntaxi {http_resp_*headerName*}.
- Proměnná serveru. Chcete-li určit proměnnou serveru, je třeba použít syntaxi {var_*serverVariable*}.
- Kombinace textu, hlavičky požadavku, hlavičky odpovědi a proměnné serveru.

## <a name="server-variables"></a>Proměnné serveru

Aplikační brána používá proměnné serveru k ukládání užitečných informací o serveru, připojení ke klientovi a aktuálním požadavku na připojení. Příklady uložených informací zahrnují IP adresu klienta a typ webového prohlížeče. Proměnné serveru se dynamicky mění, například při načtení nové stránky nebo při zaúčtování formuláře. Tyto proměnné můžete použít k vyhodnocení podmínek přepsání a přepsání záhlaví.

Aplikační brána podporuje tyto proměnné serveru:

| Název proměnné | Popis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Pole hlavičky požadavku klienta X-Forwarded-For s `client_ip` proměnnou (viz vysvětlení dále v této tabulce) k němu připojené ve formátu IP1, IP2, IP3 a tak dále. Pokud pole X-Forwarded-For není v hlavičce `add_x_forwarded_for_proxy` požadavku klienta, `$client_ip` proměnná se rovná proměnné. Tato proměnná je užitečná zejména v případě, že chcete přepsat hlavičku X-Forwarded-For nastavenou aplikací Brány tak, aby záhlaví obsahovalo pouze adresu IP bez informací o portu. |
| ciphers_supported          | Seznam šifer podporovaných klientem.          |
| ciphers_used               | Řetězec šifer používaných pro zavedené připojení TLS. |
| client_ip                  | IP adresa klienta, ze kterého aplikační brána obdržela požadavek. Pokud před bránou aplikace a původním klientem existuje reverzní proxy server, *vrátí client_ip* IP adresu reverzního proxy serveru. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informace o připojení TCP klienta. K dispozici v systémech, které podporují možnost TCP_INFO soketu. |
| client_user                | Při použití ověřování HTTP je k ověření zadáno uživatelské jméno. |
| host                       | V tomto pořadí priorit: název hostitele z řádku požadavku, název hostitele z pole hlavičky požadavku hostitele nebo název serveru odpovídající požadavku. |
| *cookie_ jméno*              | *Název* cookie.                                            |
| http_method                | Metoda použitá k požadavku na adresu URL. Například GET nebo POST. |
| http_status                | Stav relace. Například 200, 400 nebo 403.                       |
| http_version               | Protokol požadavku. Obvykle HTTP/1.0, HTTP/1.1 nebo HTTP/2.0. |
| query_string               | Seznam párů proměnných a hodnot, které následují za "?" v požadované adrese URL. |
| received_bytes             | Délka požadavku (včetně řádku požadavku, záhlaví a těla požadavku). |
| request_query              | Argumenty v řádku požadavku.                                |
| request_scheme             | Schéma požadavků: http nebo https.                            |
| request_uri                | Úplný původní požadavek URI (s argumenty).                   |
| sent_bytes                 | Počet bajtů odeslaných klientovi.                             |
| server_port                | Port serveru, který přijal požadavek.                 |
| ssl_connection_protocol    | Protokol nařízeného připojení TLS.        |
| ssl_enabled                | "On", pokud připojení pracuje v režimu TLS. V opačném případě prázdný řetězec. |

## <a name="rewrite-configuration"></a>Přepsat konfiguraci

Chcete-li nakonfigurovat přepsání hlavičky HTTP, je třeba provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky HTTP:

   - **Akce přepsání**: Používá se k určení polí hlavičky požadavku a požadavku, která chcete přepsat, a nové hodnoty pro záhlaví. K akci přepsání můžete přidružit jednu nebo více podmínek přepsání.

   - **Podmínka přepsání**: Volitelná konfigurace. Podmínky přepsání vyhodnocují obsah požadavků a odpovědí HTTP(S). Akce přepsání dojde, pokud požadavek HTTP(S) nebo odpověď odpovídá podmínce přepsání.

     Pokud přidružíte více než jednu podmínku k akci, akce nastane pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická operace AND.

   - **Pravidlo přepisu**: Obsahuje více kombinací akcí přepsání / přepsání.

   - **Pořadí pravidel**: Pomáhá určit pořadí, ve kterém se pravidla přepisu spouští. Tato konfigurace je užitečná, pokud máte více pravidel pro přepsání v sadě přepsání. Pravidlo přepsání, které má nižší hodnotu pořadí pravidel, je spuštěno jako první. Pokud přiřadíte stejnou sekvenci pravidel dvěma pravidlům pro zápis, pořadí provádění je nedeterministické.

   - **Přepsat sadu**: Obsahuje více pravidel přepsání, která budou přidružena k pravidlu směrování požadavků.

2. Připojte sadu přepisů (*rewriteRuleSet*) k pravidlu směrování. Konfigurace přepsání je připojena ke zdrojovému naslouchací procesu prostřednictvím pravidla směrování. Při použití základního pravidla směrování je konfigurace přepsání záhlaví přidružena ke zdrojovému naslouchací procesu a je přepsáním globální hlavičky. Při použití pravidla směrování založeného na cestě je konfigurace přepsání záhlaví definována na mapě cesty URL. V takovém případě se vztahuje pouze na konkrétní oblast cesty lokality.
   > [!NOTE]
   > URL Přepis změnit záhlaví; nezmění adresu URL cesty.

Můžete vytvořit více sad přepisů hlaviček PROTOKOLU HTTP a použít každou sadu přepsání na více naslouchacích procesech. Ale můžete použít pouze jeden přepsání nastavit na konkrétní naslouchací proces.

## <a name="common-scenarios"></a>Obvyklé scénáře

Zde jsou některé běžné scénáře pro použití přepsání záhlaví.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Odebrání informací o portu z hlavičky X-Forwarded-For

Brána aplikace vloží hlavičku X-Forwarded-For do všech požadavků před předáním požadavků back-endu. Toto záhlaví je seznam portů IP oddělených čárkami. Mohou existovat scénáře, ve kterých servery back-end potřebují pouze záhlaví obsahovat IP adresy. Přepsání záhlaví můžete použít k odebrání informací o portu z hlavičky X-Forwarded-For. Jedním ze způsobů, jak to provést, je nastavit záhlaví na proměnnou serveru add_x_forwarded_for_proxy:

![Odebrat port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Změna adresy URL přesměrování

Když back-endová aplikace odešle odpověď na přesměrování, můžete klienta přesměrovat na jinou adresu URL, než kterou zadala aplikace back-end. Například můžete chtít provést, když je služba aplikace hostovaná za aplikační bránou a vyžaduje, aby klient přesměroval na relativní cestu. (Například přesměrování z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2.)

Vzhledem k tomu, že služba App Service je víceklientská služba, používá hlavičku hostitele v požadavku ke směrování požadavku do správného koncového bodu. Služby aplikace mají výchozí název domény *.azurewebsites.net (řekněme contoso.azurewebsites.net), který se liší od názvu domény aplikační brány (řekněme contoso.com). Vzhledem k tomu, že původní požadavek klienta má název domény aplikační brány (contoso.com) jako název hostitele, změní aplikační brána název hostitele na contoso.azurewebsites.net. Provede tuto změnu tak, aby služba aplikace můžete směrovat požadavek na správný koncový bod.

Když služba aplikace odešle odpověď na přesměrování, použije stejný název hostitele v hlavičce umístění své odpovědi jako v požadavku, který obdrží z aplikační brány. Takže klient bude žádost přímo contoso.azurewebsites.net/path2 namísto prochází aplikační brány (contoso.com/path2). Vynechání brány aplikace není žádoucí.

Tento problém můžete vyřešit nastavením názvu hostitele v hlavičce umístění na název domény aplikační brány.

Zde jsou kroky pro nahrazení názvu hostitele:

1. Vytvořte pravidlo přepsání s podmínkou, která vyhodnotí, zda hlavička umístění v odpovědi obsahuje azurewebsites.net. Zadejte `(https?):\/\/.*azurewebsites\.net(.*)$`vzorek .
1. Proveďte akci k přepsání hlavičky umístění tak, aby měl název hostitele aplikační brány. Proveďte to `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` zadáním hodnoty záhlaví.

![Změnit záhlaví umístění](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementace hlavičky HTTP zabezpečení, aby se zabránilo chybám zabezpečení

Můžete opravit několik chyb zabezpečení implementací nezbytných záhlaví v odpovědi aplikace. Tyto hlavičky zabezpečení zahrnují X-XSS-Protection, Strict-Transport-Security a Content-Security-Policy. Pomocí brány aplikace můžete nastavit tyto hlavičky pro všechny odpovědi.

![Hlavička zabezpečení](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Odstranění nežádoucích záhlaví

Můžete chtít odebrat záhlaví, které odhalují citlivé informace z odpovědi HTTP. Můžete například odebrat informace, jako je název serveru back-end, operační systém nebo podrobnosti knihovny. Pomocí brány aplikace můžete odebrat tato záhlaví:

![Odstranění záhlaví](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Kontrola přítomnosti záhlaví

Můžete vyhodnotit hlavičku požadavku HTTP nebo odpovědi na přítomnost hlavičky nebo proměnné serveru. Toto vyhodnocení je užitečné, pokud chcete provést přepsání záhlaví pouze v případě, že je k dispozici určité záhlaví.

![Kontrola přítomnosti záhlaví](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Omezení

- Pokud odpověď obsahuje více než jedno záhlaví se stejným názvem, pak přepsání hodnoty jednoho z těchto záhlaví bude mít za následek uvolnění mařit ostatní záhlaví v odpovědi. To se obvykle může stát s hlavičkou Set-Cookie, protože v odpovědi můžete mít více než jednu hlavičku Set-Cookie. Jeden takový scénář je, když používáte službu aplikace s aplikační bránou a nakonfigurovali spřažení relací založených na souborech cookie v bráně aplikace. V tomto případě bude odpověď obsahovat dvě hlavičky Set-Cookie: jednu používanou službou aplikace, `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` například: `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`a druhou pro spřažení aplikační brány, například . Přepsání jedné z hlaviček set-cookie v tomto scénáři může mít za následek odebrání jiné hlavičky set-cookie z odpovědi.

- Přepisování záhlaví Připojení, Upgrade a Hostitele není aktuálně podporováno.

- Názvy záhlaví mohou obsahovat libovolné alfanumerické znaky a specifické symboly definované v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). V současné době nepodporujeme\_speciální znak podtržítka ( ) v názvech záhlaví.

## <a name="next-steps"></a>Další kroky

Informace o přepisu záhlaví HTTP najdete v tématu:

- [Přepis záhlaví HTTP pomocí portálu Azure Portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Přepis ování záhlaví HTTP pomocí Azure PowerShellu](add-http-header-rewrite-rule-powershell.md)
