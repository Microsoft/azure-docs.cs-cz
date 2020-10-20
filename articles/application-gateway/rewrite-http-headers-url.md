---
title: Přepsání hlaviček a adres URL protokolu HTTP pomocí Azure Application Gateway | Microsoft Docs
description: Tento článek poskytuje přehled přepisu hlaviček a adres URL protokolu HTTP v Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 93af3183ae9e969d14a35ce4e365d48895ef4e79
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216670"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Přepsat hlavičky a adresu URL protokolu HTTP pomocí Application Gateway

 Application Gateway umožňuje přepsat vybraný obsah požadavků a odpovědí. Pomocí této funkce můžete překládat adresy URL, parametry řetězce dotazu a také upravovat hlavičky požadavků a odpovědí. Umožňuje taky přidat podmínky, abyste zajistili, že se adresa URL nebo zadaná záhlaví přepíší jenom v případě, že jsou splněné určité podmínky. Tyto podmínky vycházejí z informací o požadavku a odpovědi.

>[!NOTE]
>Funkce hlaviček protokolu HTTP a přepis adres URL jsou k dispozici pouze pro [SKU Application Gateway v2](application-gateway-autoscaling-zone-redundant.md) .

## <a name="rewrite-types-supported"></a>Podporované typy přepsání

### <a name="request-and-response-headers"></a>Hlavičky žádostí a odpovědí

Hlavičky protokolu HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsáním těchto hlaviček můžete provádět důležité úkoly, jako je například přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS/X-XSS-Protection, odebrání polí hlaviček odpovědi, které mohou odhalit citlivé informace, a odebrání informací o portech z předávaných hlaviček X-pro.

Když se pakety požadavků a odpovědí pohybují mezi klientem a back-endovými fondy, služba Application Gateway umožňuje přidat, odebrat nebo aktualizovat hlavičky požadavků a odpovědí HTTP.

Informace o tom, jak přepsat hlavičky žádosti a odpovědi pomocí Application Gateway pomocí Azure Portal najdete [tady](rewrite-url-portal.md).

![obrázek](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Podporované hlavičky**

V žádostech a odpovědích můžete přepsat všechna záhlaví s výjimkou připojení a hlaviček upgradu. Aplikační bránu můžete použít také k vytvoření vlastních hlaviček a jejich přidání do požadavků a odpovědí, které jsou prostřednictvím ní směrovány.

### <a name="url-path-and-query-string-preview"></a>Cesta URL a řetězec dotazu (Preview)

Pomocí funkce pro přepsání adresy URL v Application Gateway můžete:

* Přepište název hostitele, cestu a řetězec dotazu adresy URL požadavku. 

* Vyberte, že se má přepsat adresa URL všech žádostí na naslouchací proces, nebo jenom ty, které splňují jednu nebo více podmínek, které jste nastavili. Tyto podmínky jsou založené na vlastnostech žádosti a odpovědi (požadavek, hlavička, hlavička odpovědi a proměnné serveru).

* Zvolte, že se má žádost směrovat (vyberte fond back-end) na základě původní adresy URL nebo přepsané adresy URL.

Informace o tom, jak přepsat adresu URL pomocí Application Gateway pomocí Azure Portal, najdete [tady](rewrite-url-portal.md).

![Diagram, který popisuje proces přepisu adresy URL pomocí Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> Funkce přepisu adresy URL je ve verzi Preview a je k dispozici pouze pro Standard_v2 a WAF_v2 SKU Application Gateway. Nedoporučuje se používat v produkčním prostředí. Další informace o verzi Preview najdete v tématu věnovaném [podmínkám použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rewrite-actions"></a>Akce přepisu

Akce přepisu slouží k zadání adresy URL, hlaviček požadavků nebo hlaviček odpovědí, které chcete přepsat, a nové hodnoty, do které chcete přepsat. Hodnota adresy URL nebo nová nebo existující hlavička může být nastavena na tyto typy hodnot:

* Text
* Hlavička žádosti Chcete-li zadat hlavičku požadavku, je nutné použít syntaxi {http_req_*header*}.
* Hlavička odpovědi. Chcete-li zadat hlavičku odpovědi, je nutné použít syntaxi {http_resp_*header*}.
* Proměnná serveru. Pokud chcete zadat proměnnou serveru, musíte použít syntaxi {var_*serverVariable*}. Zobrazit seznam podporovaných proměnných serveru
* Kombinace textu, hlavičky požadavku, hlavičky odpovědi a serverové proměnné. 

## <a name="rewrite-conditions"></a>Podmínky přepisu

Můžete použít podmínky přepisu, volitelná konfigurace, vyhodnotit obsah požadavků a odpovědí HTTP a provést přepsání pouze v případě, že je splněna jedna nebo více podmínek. Aplikační brána používá tyto typy proměnných k vyhodnocení obsahu požadavků a odpovědí:

* Hlavičky HTTP v žádosti
* Hlavičky HTTP v odpovědi
* Proměnné serveru Application Gateway

Podmínku můžete použít k vyhodnocení, zda je zadaná proměnná přítomna, zda zadaná proměnná odpovídá konkrétní hodnotě nebo zda zadaná proměnná odpovídá konkrétnímu vzoru. 


### <a name="pattern-matching"></a>Porovnávání vzorů 

Application Gateway používá regulární výrazy pro porovnávání vzorů v podmínce. K nastavení porovnávání vzorů regulárních výrazů v podmínkách lze použít [knihovnu PCRE (](https://www.pcre.org/) standarded Expressions Compatible). Další informace o syntaxi regulárních výrazů naleznete v [hlavní stránce regulární výrazy jazyka Perl](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Zachytávání

Pro zachycení dílčího řetězce pro pozdější použití vložte závorky kolem dílčího vzoru, který odpovídá v definici regulárního výrazu podmínky. První dvojice závorek ukládá svůj podřetězec v 1, druhé dvojici v 2 atd. Můžete použít tolik kulatých závorek, kolik jich budete chtít. Jazyk Perl pouze udržuje pro tyto zachycené řetězce definovat více očíslovaných proměnných. Příklady z [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# porovnává dvě číslice a zachytí je do skupin 1 a 2. 

* /(\d +)/# se shoduje s jednou nebo více číslicemi, které jsou zachytávání do skupiny 1. 

* /(\d) +/# odpovídá číslici jednou nebo vícekrát a zachytí poslední do skupiny 1.

Po zachycení můžete na ně odkazovat v sadě akcí pomocí následujícího formátu:

* Pro zachytávání hlaviček požadavku je nutné použít {http_req_headerName_groupNumber}. Například {http_req_User-Agent_1} nebo {http_req_User-Agent_2}
* Pro zachytávání hlaviček odpovědí je nutné použít {http_resp_headerName_groupNumber}. Například {http_resp_Location_1} nebo {http_resp_Location_2}
* Pro proměnnou serveru musíte použít {var_serverVariableName_groupNumber}. Například {var_uri_path_1} nebo {var_uri_path_2}

Pokud chcete použít celou hodnotu, neměli byste toto číslo uvádět. Stačí použít formát {http_req_headerName} atd. bez groupNumber.

## <a name="server-variables"></a>Serverové proměnné

Application Gateway používá serverové proměnné k ukládání užitečných informací o serveru, připojení ke klientovi a aktuální žádosti o připojení. Příklady uložených informací zahrnují IP adresu klienta a typ webového prohlížeče. Proměnné serveru se dynamicky mění, například když se načte nová stránka nebo když se publikuje formulář. Tyto proměnné můžete použít k vyhodnocení podmínek přepisu a přepisu hlaviček. Aby bylo možné použít hodnotu proměnných serveru k přepsání hlaviček, bude nutné zadat tyto proměnné v syntaxi {var_*serverVariableName*}.

Application Gateway podporuje následující proměnné serveru:

|   Název proměnné    |                   Popis                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Pole hlavičky žádosti klienta s přesměrováním do `client_ip` proměnné (viz vysvětlení později v této tabulce), ke kterému se připojuje ve formátu IP1, IP2, IP3 a tak dále. Pokud pole s přesměrováním X není v hlavičce žádosti klienta, `add_x_forwarded_for_proxy` proměnná je rovna `$client_ip` proměnné.   Tato proměnná je užitečná hlavně v případě, že chcete přepsat hlavičku, kterou předáváte X-pro nastavenou Application Gateway tak, aby hlavička obsahovala jenom IP adresu bez informací o portu. |
| ciphers_supported         | Seznam šifr podporovaných klientem.               |
| ciphers_used              | Řetězec šifr používaných pro navázáné připojení TLS. |
| client_ip                 | IP adresa klienta, ze kterého služba Application Gateway přijala požadavek. Pokud před aplikační bránou a původním klientem dojde k reverznímu proxy serveru, `client_ip` vrátí IP adresu reverzního proxy serveru. |
| client_port               | Port klienta.                                             |
| client_tcp_rtt            | Informace o připojení TCP klienta. K dispozici v systémech, které podporují možnost soketu TCP_INFO. |
| client_user               | Když se použije ověřování protokolem HTTP, uživatelské jméno zadané pro ověřování. |
| Hostitel                      | V tomto pořadí priorit: název hostitele z řádku žádosti, název hostitele z pole Hlavička požadavku hostitele nebo název serveru, který odpovídá požadavku. Příklad: v požadavku `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` bude hodnota hostitele `contoso.com` |
| *název* cookie_             | *Název* souboru cookie.                                           |
| http_method               | Metoda použitá pro vytvoření žádosti adresy URL Například GET nebo POST. |
| http_status               | Stav relace. Například 200, 400 nebo 403.           |
| http_version              | Protokol žádosti. Obvykle HTTP/1.0, HTTP/1.1 nebo HTTP/2.0. |
| query_string              | Seznam párů proměnných/hodnot, které následují po "?" v požadované adrese URL. Příklad: v požadavku se `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING hodnota `id=123&title=fabrikam` |
| received_bytes            | Délka požadavku (včetně řádku žádosti, hlavičky a textu žádosti) |
| request_query             | Argumenty na řádku požadavku.                           |
| request_scheme            | Schéma žádosti: http nebo HTTPS.                           |
| request_uri               | Úplný identifikátor URI původní žádosti (s argumenty). Příklad: v požadavku se `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` REQUEST_URI hodnota `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Počet bajtů odeslaných klientovi.                        |
| server_port               | Port serveru, který přijal požadavek.              |
| ssl_connection_protocol   | Protokol vytvořeného připojení TLS.               |
| ssl_enabled               | "On", pokud připojení funguje v režimu TLS. V opačném případě prázdný řetězec. |
| uri_path                  | Identifikuje konkrétní prostředek v hostiteli, ke kterému chce webový klient získat přístup. Toto je část identifikátoru URI požadavku bez argumentů. Příklad: v požadavku se `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path hodnota `/article.aspx` |

 

## <a name="rewrite-configuration"></a>Přepsat konfiguraci

Chcete-li nakonfigurovat pravidlo přepsání, je nutné vytvořit sadu pravidel přepsání a přidat do ní konfiguraci pravidla přepsání.

Sada pravidel přepsání obsahuje:

* **Přidružení pravidla směrování žádostí:** Konfigurace přepsání je přidružená ke zdrojovému naslouchacího procesu prostřednictvím pravidla směrování. Použijete-li pravidlo základního směrování, je konfigurace přepsání přidružena ke zdrojovému naslouchacího procesu a je přepsána globální hlavičkou. Když použijete pravidlo směrování na základě cesty, konfigurace přepsání se definuje na mapě cesty URL. V takovém případě platí pouze pro konkrétní oblast cesty lokality. Můžete vytvořit vícenásobné sady pro přepsání a použít všechny přepsané sady na více posluchačů. Můžete ale použít jenom jednu sadu přepsaného zápisu na konkrétní naslouchací proces.

* **Podmínka přepisu**: Jedná se o volitelnou konfiguraci. Podmínky přepisu vyhodnocují obsah požadavků a odpovědí HTTP (S). Akce přepisu nastane, pokud požadavek nebo odpověď HTTP (S) odpovídá podmínce přepsání. Pokud k akci přiřadíte více než jednu podmínku, bude akce provedena pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická a operace.

* **Typ přepsání**: k dispozici jsou tři typy přepsání:
   * Přepis hlaviček požadavků 
   * Přepisují se hlavičky odpovědí.
   * Přepisování adresy URL: typ přepisu adresy URL má 3 součásti
      * **Cesta URL**: hodnota, na kterou má být cesta přepsána. 
      * **Řetězec dotazu adresy URL**: hodnota, na kterou se má řetězec dotazu přepsat. 
      * **Znovu vyhodnotit mapu cest**: slouží k určení, zda má být mapování cesty URL znovu vyhodnoceno. Pokud je tato akce ponechána nezaškrtnutá, použije se původní cesta URL, která bude odpovídat vzoru cesty v mapě cesty URL. Pokud je nastavená hodnota true, mapování cesty URL se znovu vyhodnotí, aby se zkontrolovala shoda s přepsanou cestou. Povolení tohoto přepínače pomáhá při směrování požadavku do jiného back-endu po opětovném zápisu.

### <a name="common-scenarios-for-header-rewrite"></a>Běžné scénáře přepsání hlaviček

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Odebrat informace o portu ze záhlaví X-Transported – pro

Application Gateway vloží do všech požadavků hlavičku X s přesměrováním na všechny požadavky předtím, než přepošle požadavky do back-endu. Tato hlavička je čárkami oddělený seznam portů IP. Můžou nastat situace, kdy back-end servery potřebují jenom hlavičky obsahující IP adresy. Chcete-li odebrat informace o portu ze záhlaví X-Transported, můžete použít možnost přepisování hlaviček. To lze provést pouze v případě, že nastavíte hlavičku na serverovou proměnnou add_x_forwarded_for_proxy. Alternativně můžete také použít client_ip proměnných:

![Odebrat port](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Úprava adresy URL pro přesměrování

Když aplikace back-end pošle odezvu přesměrování, může být vhodné přesměrovat klienta na jinou adresu URL, než která je určena v back-endové aplikaci. Můžete to třeba udělat, když je služba App Service hostována za aplikační bránou a vyžaduje, aby klient provedl přesměrování na jeho relativní cestu. (Například přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2.)

Vzhledem k tomu, že App Service je víceklientské služba, používá v žádosti hlavičku hostitele ke směrování požadavku na správný koncový bod. App Services mají výchozí název domény *. azurewebsites.net (řekněme contoso.azurewebsites.net), který se liší od názvu domény služby Application Gateway (řekněme contoso.com). Vzhledem k tomu, že původní požadavek od klienta má název domény služby Application Gateway (contoso.com) jako název hostitele, služba Application Gateway změní název hostitele na contoso.azurewebsites.net. Tato změna tuto změnu provede, aby služba App Service mohla požadavek směrovat do správného koncového bodu.

Když služba App Service pošle odezvu přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, kterou přijímá z aplikační brány. Proto klient provede požadavek přímo na contoso.azurewebsites.net/path2 namísto průchodu přes Aplikační bránu (contoso.com/path2). Obcházení aplikační brány není žádoucí.

Tento problém můžete vyřešit tak, že nastavíte název hostitele v hlavičce umístění na název domény služby Application Gateway.

Tady je postup nahrazení názvu hostitele:

1. Vytvoří pravidlo přepsání s podmínkou, která vyhodnotí, jestli hlavička umístění v odpovědi obsahuje azurewebsites.net. Zadejte vzor `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Provedením akce přepište hlavičku umístění tak, aby měla název hostitele služby Application Gateway. To uděláte tak, že zadáte `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` hodnotu hlavičky. Alternativně můžete také použít proměnnou serveru `host` k nastavení názvu hostitele tak, aby odpovídal původní žádosti.

![Změnit hlavičku umístění](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementace hlaviček protokolu HTTP zabezpečení, aby se předešlo ohrožení zabezpečení

Pomocí nezbytných hlaviček v odpovědi aplikace můžete opravit několik chyb zabezpečení. Tyto hlavičky zabezpečení zahrnují zásady X-XSS-Protection, striktního přenosu a zabezpečení obsahu. K nastavení těchto hlaviček pro všechny odpovědi můžete použít Application Gateway.

![Záhlaví zabezpečení](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Odstranit nechtěné hlavičky

Možná budete chtít odebrat hlavičky, které odhalí citlivé informace z odpovědi HTTP. Například můžete chtít odebrat informace, jako je název back-end serveru, operační systém nebo podrobnosti knihovny. K odebrání těchto hlaviček můžete použít Aplikační bránu:

![Odstraňuje se záhlaví](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Zkontroluje přítomnost záhlaví.

Pro přítomnost záhlaví nebo serverové proměnné můžete vyhodnotit hlavičku požadavku nebo odpovědi HTTP. Toto vyhodnocení je užitečné v případě, že chcete provést přepsání hlavičky pouze v případě, že je k dispozici určitá hlavička.

![Kontroluje se přítomnost hlavičky.](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Běžné scénáře pro přepsání adresy URL

#### <a name="parameter-based-path-selection"></a>Výběr cesty na základě parametrů

Chcete-li dosáhnout scénářů, ve kterých chcete vybrat back-end fond na základě hodnoty záhlaví, části adresy URL nebo řetězce dotazu v žádosti, můžete použít kombinaci možností přepsání adresy URL a směrování na základě cesty.  Například pokud máte nákupní web a kategorie produktu se předává jako řetězec dotazu v adrese URL a chcete směrovat požadavek do back-endu na základě řetězce dotazu, pak:

**Krok 1:**  Vytvořte mapu cest, jak je znázorněno na obrázku níže.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scénář přepsání adresy URL 1-1.":::

**Krok 2 (a):** Vytvořte sadu přepsání, která má 3 pravidla přepisu: 

* První pravidlo má podmínku, která kontroluje *QUERY_STRING*  proměnnou pro *kategorii = obuv* a má akci, která přepíše cestu URL k/*listing1* a má **znovu vyhodnotit mapu cest** povoleno.

* Druhé pravidlo má podmínku, která kontroluje *QUERY_STRING*  proměnnou pro *Category = pytle* a má akci, která přepíše cestu URL k/*listing2*  a má **znovu vyhodnotit mapu cest** .

* Třetí pravidlo má podmínku, která kontroluje *QUERY_STRING*  proměnnou pro *kategorii = příslušenství* a má akci, která přepíše cestu URL k/*listing3* a má **znovu vyhodnotit mapu cest** zapnuto.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scénář přepsání adresy URL 1-1.":::

 

**Krok 2 (b):** Přidružit tuto sadu přepsání s výchozí cestou k výše uvedenému pravidlu na základě cesty

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scénář přepsání adresy URL 1-1.":::

Když teď uživatel požaduje *contoso.com/listing?Category=any*, pak se porovná s výchozí cestou, protože žádný ze vzorů cest v mapě cest (/listing1,/listing2,/listing3) se neshoduje. Vzhledem k tomu, že jste přidružili výše uvedenou sadu přepsání s touto cestou, vyhodnotí se tato sada přepisování. Vzhledem k tomu, že řetězec dotazu se neshoduje s podmínkou v žádném z 3 pravidel přepsání v této sadě přepsání, nebude provedena žádná akce přepisu, takže požadavek bude směrován beze změny do back-endu přidruženého k výchozí cestě (což je *GenericList*).

 Pokud uživatel požádá o *contoso.com/listing?Category=Shoes,* bude se shodovat výchozí cesta. V tomto případě se ale podmínka prvního pravidla bude shodovat, a proto se spustí akce přidružená k této podmínce, která přepíše cestu URL k/*listing1*  a znovu vyhodnotí mapu cest. Když se znovu vyhodnotí mapování cest, požadavek se teď bude shodovat s cestou přidruženou ke vzorové */listing1* a požadavek se směruje do back-endu přidruženého k tomuto vzoru, což je ShoesListBackendPool.

>[!NOTE]
>Tento scénář je možné rozšířit na jakékoli hodnoty hlaviček nebo souborů cookie, cestu URL, řetězec dotazu nebo proměnné serveru na základě definované podmínky a v podstatě vám umožní směrovat požadavky na základě těchto podmínek.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Přepsat parametry řetězce dotazu na základě adresy URL

Vezměte v úvahu scénář nákupního webu, ve kterém by měl být viditelný odkaz uživatele jednoduchý a čitelný, ale back-end server potřebuje k zobrazení správného obsahu parametry řetězce dotazu.

V takovém případě Application Gateway může zachytit parametry z adresy URL a přidat páry klíč-hodnota řetězce dotazu z adresy URL. Řekněme například, že uživatel chce přezapisovat `https://www.contoso.com/fashion/shirts` do `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` , dá se dosáhnout pomocí následující konfigurace přepsání adresy URL.

**Podmínka** – Pokud se proměnná serveru `uri_path` rovná vzoru `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scénář přepsání adresy URL 1-1.":::

**Action** – nastaví cestu URL k `buy.aspx` řetězci dotazu na `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scénář přepsání adresy URL 1-1.":::

Podrobný průvodce pro dosažení výše popsaného scénáře najdete v tématu [přepisování adresy URL s Application Gateway pomocí Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Přesměrování adresy URL přepsání adresy URL vs

V případě přepisu adresy URL Application Gateway přepíše adresu URL před odesláním požadavku do back-endu. Tato změna neovlivní to, co uživatelé uvidí v prohlížeči, protože změny se od uživatele skryjí.

V případě přesměrování adresy URL Application Gateway odešle klientovi odpověď přesměrování s novou adresou URL. To zase vyžaduje, aby klient znovu odeslal svůj požadavek na novou adresu URL, která je k dispozici v přesměrování. Adresa URL, kterou uživatel vidí v prohlížeči, se aktualizuje na novou adresu URL.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Scénář přepsání adresy URL 1-1.":::

## <a name="limitations"></a>Omezení

- Pokud má odpověď více než jednu hlavičku se stejným názvem, pak přepsání hodnoty jednoho z těchto hlaviček způsobí vyřazení ostatních hlaviček v odpovědi. K tomu obvykle dochází v Set-Cookie hlavičce, protože odpověď může obsahovat více než jednu Set-Cookie hlavičku. Takový scénář se používá v případě, že používáte službu App Service s aplikační bránou a máte nakonfigurovanou spřažení relací na základě souborů cookie na aplikační bránu. V takovém případě bude odpověď obsahovat dvě hlavičky Set-Cookie: jednu, kterou používá služba App Service, například: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` a další pro spřažení Application Gateway, například `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Přepsání jedné z hlaviček Set-Cookie v tomto scénáři může mít za následek odebrání druhé hlavičky Set-Cookie z odpovědi.
- Přepsání nejsou podporována, pokud je brána Application Gateway nakonfigurována pro přesměrování požadavků nebo zobrazení vlastní chybové stránky.
- Názvy hlaviček můžou obsahovat libovolné alfanumerické znaky a specifické symboly, jak jsou definované v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). V názvech hlaviček Momentálně nepodporujeme speciální znak podtržítka (_).
- Nelze přepsat hlavičky připojení a upgradu

## <a name="next-steps"></a>Další kroky

- [Informace o tom, jak přepsat hlavičky HTTP pomocí Application Gateway pomocí Azure Portal](rewrite-http-headers-portal.md)
- [Informace o tom, jak přepsat adresu URL s Application Gateway pomocí Azure Portal](rewrite-url-portal.md)
