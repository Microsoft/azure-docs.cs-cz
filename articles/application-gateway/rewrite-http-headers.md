---
title: Přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje přehled funkce přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 2babb6ff7b93ad9cf7c93565cadce9453a3b96ca
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103424"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Přepsání hlavičky protokolu HTTP pomocí služby Application Gateway (public preview)

Hlavičky protokolu HTTP umožňují klienta a serveru předat další informace o požadavku nebo odpovědi. Přepsání těchto HTTP záhlaví vám pomáhá s několik důležitých scénářů, jako je například přidávání bezpečnostních záhlaví pole jako HSTS / X-XSS ochrany nebo odebrání pole hlavičky odpovědi, což může vést k odhalení citlivých informací, jako je název serveru back-endu.

Služba Application Gateway nyní podporuje schopnosti přepsat záhlaví příchozí HTTP požadavky i odchozí odpovědi protokolu HTTP. Budete moct přidat, odebrat nebo aktualizovat hlavičky požadavku a odpovědi protokolu HTTP požadavku nebo odpovědi pakety přesunout mezi klientem a back-endové fondy. Je možné přepsat obě pole standardní, jakož i nestandardní hlavičky.

> [!NOTE] 
>
> Podpora přepsání hlavičky protokolu HTTP je dostupná jenom pro [novou skladovou Položku [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Podpora služby Application Gateway záhlaví revize nabízí:

- **Globální záhlaví revize**: Je možné přepsat konkrétní hlavičky pro všechny požadavky a odpovědi týkající se webu.
- **Na základě cest záhlaví revize**: Tento typ přepsání umožňuje přepsání hlavičky pro pouze tyto požadavky a odpovědi, které se vztahují pouze na určitém webovém serveru oblast, třeba nákupní košík oblasti udávají/košík / *.

Díky této změně budete muset:

1. Vytvořte nové objekty požadované pro přepsání hlavičky protokolu http: 
   - **RequestHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky požadavku a nové hodnoty, které musí být přepsány, aby původní záhlaví.
   - **ResponseHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky odpovědi a nové hodnoty, které musí být přepsány, aby původní záhlaví.
   - **ActionSet**: Tento objekt obsahuje konfigurace z výše uvedených hlaviček žádostí a odpovědí. 
   - **RewriteRule**: Tento objekt obsahuje všechny *actionSets* uvedeno výše. 
   - **RewriteRuleSet**– tento objekt obsahuje všechny *rewriteRules* a musí být připojené k pravidlo směrování požadavku – basic nebo na základě cest.
2. Pak budete muset připojit sadu pravidel přepisování pomocí pravidel směrování. Po vytvoření této revize konfigurace je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky je přidružený zdroj naslouchací proces a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. Ano platí jen pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad pravidel přepisování hlavičky protokolu http a každou sadu pravidel přepsání lze použít na víc naslouchacích procesů. Můžete však použít jediné pravidlo pro přepis adres http nastavena na konkrétním posluchačem.

Je možné přepsat hodnotou v hlavičkách na:

- Textová hodnota. 

  *Příklad:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Hodnota z jiné záhlaví. 

  *Příklad 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Pokud chcete zadat hlavičku požadavku, budete muset použít syntaxe: {http_req_headerName}

  *Příklad 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Pokud chcete zadat hlavičku odpovědi, budete muset použít syntaxe: {http_resp_headerName}

- Hodnota z podporovaných serverových proměnných.

  *Příklad:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Pokud chcete zadat proměnnou serveru, budete muset použít syntaxe: {var_serverVariable}

- Kombinace výše.

## <a name="server-variables"></a>Serverové proměnné

Serverové proměnné ukládat užitečné informace na webovém serveru. Tyto proměnné poskytují informace o serveru, připojení s klientem a aktuální požadavek na připojení, jako je například IP adresa klienta nebo typ webového prohlížeče. Tyto změny dynamicky, například při načtení nové stránky nebo odeslání formuláře.  Pomocí těchto uživatelů proměnným lze nastavit hlavičky požadavku, jakož i hlavičky odpovědi. 

Tato funkce podporuje přepis adres hlavičky pro následující proměnné na serveru:

| Podporované serverové proměnné | Popis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | Vrátí seznam šifer podporovaných klientem          |
| ciphers_used               | Vrátí řetězec šifry použitý pro navázané připojení SSL |
| client_port                | port klienta                                                  |
| client_tcp_rtt             | informace o klientovi připojení TCP. k dispozici v systémech, které podporují možnost soketu TCP_INFO |
| client_user                | Pokud používáte ověřování pomocí protokolu HTTP, uživatelské jméno zadané pro ověřování |
| hostitel                       | v tomto pořadí priorit: název hostitele z řádku požadavku nebo název hostitele z pole hlavičky požadavku "Hostitel", nebo odpovídající žádost o název serveru |
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

## <a name="limitations"></a>Omezení

- Tuto možnost přepsání hlavičky protokolu HTTP je momentálně dostupná jenom prostřednictvím Azure Powershellu, rozhraní API služby Azure a Azure SDK. Brzy bude k dispozici podporu prostřednictvím portálu a Azure CLI.

- Podpora přepsání hlavičky protokolu HTTP je podporována pouze na nové SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). Možnost nebude podporovat staré skladové položky.

- Přepsání hlavičky připojení, Upgrade a hostitel ještě nepodporuje.

- Dvě důležité serverových proměnných, client_ip (IP adresu klientovi provádějícímu žádost) a cookie_*název* ( *název* souborů cookie), se zatím nepodporují. Proměnná serveru client_ip je zvláště užitečné v situacích, kde chcete přepsat nastavení službou Application Gateway, Hlavička x předané – pro tak, aby záhlaví obsahuje IP adresu klienta a ne informace o portu zákazníkům.

  Obě tyto proměnné serveru budou již brzy bude podporován.

- Brzy bude k dispozici možnost podmíněně přepsání hlavičky protokolu http.

- Názvy záhlaví může obsahovat jakékoli alfanumerickým znakem a konkrétní symboly definované v [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Však aktuálně nepodporujeme "podtržítka" (\_) speciálního znaku v názvu záhlaví. 

## <a name="need-help"></a>Potřebujete pomoc?

Kontaktujte nás na adrese [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) v případě, že potřebujete pomoc s tuto funkci.

## <a name="next-steps"></a>Další postup

Po získání informací o možnost přepsání hlavičky protokolu HTTP, přejděte na [vytvoření automatického škálování a zónově redundantní aplikační bránu, která přepíše hlavičky protokolu HTTP](tutorial-http-header-rewrite-powershell.md) nebo [hlavičky HTTP přepsat v existující automatické škálování a zónově redundantní služba application gateway](add-http-header-rewrite-rule-powershell.md)
