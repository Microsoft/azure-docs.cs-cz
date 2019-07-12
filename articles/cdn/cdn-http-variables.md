---
title: Proměnné protokolu HTTP pro stroj pravidel Azure CDN | Dokumentace Microsoftu
description: Proměnné protokolu HTTP umožňují načíst metadata žádostí a odpovědí HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593817"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Proměnné protokolu HTTP pro Azure CDN stroje pravidel
HTTP proměnné poskytují prostředky, pomocí kterého můžete získat metadata žádostí a odpovědí HTTP. Tato metadata pak lze dynamicky měnit požadavek nebo odpověď. Použití proměnných HTTP je omezen na následující funkce stroje pravidel:

- [Přepište klíč mezipaměti](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Upravit hlavičku požadavku klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Upravit klienta hlavička odpovědi](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Adresa URL pro přesměrování](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Přepsání adresy URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definice
Následující tabulka popisuje podporované proměnné protokolu HTTP. Při Geograficky metadata (například PSČ) není k dispozici pro konkrétní žádost byla vrácena prázdná hodnota.


| Name | Proměnná | Popis | Ukázková hodnota |
| ---- | -------- | ----------- | ------------ |
| Číslo ASN (žadateli) | %{geo_asnum} | Označuje žadatele jako číslo. <br /><br />**Zastaralé:** % {virt_dst_asnum}. <br />Tato proměnná je zastaralá a místo toho použití % {geo_asnum}. I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné. | AS15133 |
| Město (žadateli) | %{geo_city} | Označuje žadatele město. | Los Angeles |
| Kontinent (žadateli) | %{geo_continent} | Označuje žadatele kontinent prostřednictvím jeho zkratkou. <br />Platné hodnoty jsou: <br />AF: Afrika<br />AS: Asie<br />EU: Evropa<br />NA: Severní Amerika<br />OC: Oceánie<br />PŘIDRUŽENÍ ZABEZPEČENÍ: Jižní Amerika<br /><br />**Deprecated:** %{virt_dst_continent}. <br />Tato proměnná je zastaralá a místo toho použití % {geo_continent}. <br />I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné.| Není k dispozici |
| Hodnota souboru cookie | %{cookie_Cookie} | Vrací hodnotu odpovídající klíč souboru cookie, který je identifikován jako souboru Cookie. | Využití vzorků: <br />%{cookie__utma}<br /><br />Hodnota vzorku:<br />111662281.2.10.1222100123 |
| Země (žadateli) | %{geo_country} | Označuje žadatele země původu prostřednictvím jeho směrové číslo země. <br />**Zastaralé:** % {virt_dst_country}. <br /><br />Tato proměnná je zastaralá a místo toho použití % {geo_country}. I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné. | USA |
| Určený trh oblasti (žadateli) | %{geo_dma_code} |Označuje žadatele média trhu podle oblasti kódu. <br /><br />Toto pole platí pouze pro požadavky, které pocházejí z USA.| 745 |
| Metoda požadavku HTTP | %{request_method} | Označuje metodu požadavku HTTP. | GET |
| Kód stavu HTTP | %{status} | Určuje kód stavu HTTP pro odpověď. | 200 |
| IP adresa (žadateli) | %{virt_dst_addr} | Označuje žadatele IP adresu. | 192.168.1.1 |
| Zeměpisná šířka (žadateli) | %{geo_latitude} | Určuje zeměpisnou šířku žadatele. | 34.0995 |
| Zeměpisná délka (žadateli) | %{geo_longitude} | Určuje zeměpisnou délku žadatele. | -118.4143 |
| Statistické metropolitní oblasti (žadateli) | %{geo_metro_code} | Označuje metropolitní oblasti žadatele. <br /><br />Toto pole platí pouze pro požadavky, které pocházejí z USA.<br />| 745 |
| Port (žadateli) | %{virt_dst_port} | Určuje dočasný port žadatele. | 55885 |
| Poštovní směrovací číslo (žadateli) | %{geo_postal_code} | Určuje poštovní směrovací číslo žadatele. | 90210 |
| Řetězec dotazu nalezen | %{is_args} | Hodnota pro tuto proměnnou, se liší podle Určuje, zda žádost obsahuje řetězec dotazu.<br /><br />-Nalezen řetězec dotazu:?<br />-Žádný řetězec dotazu: NULL | ? |
| Nebyl nalezen parametr řetězce dotazu | %{is_amp} | Hodnota pro tuto proměnnou se liší podle Určuje, zda požadavek obsahuje nejméně jeden parametr řetězce dotazu.<br /><br />-Nebyl nalezen parametr: &<br />-Žádné parametry: NULL | & |
| Hodnotu parametru řetězce dotazu | %{arg_&lt;parameter&gt;} | Vrací hodnotu odpovídající parametru řetězce dotazu, který je identifikován &lt;parametr&gt; termín. | Využití vzorků: <br />%{arg_language}<br /><br />Ukázka parametru řetězce dotazu: <br />? jazyk = en<br /><br />Ukázkové hodnoty: cs |
| Hodnota řetězce dotazu | %{query_string} | Určuje řetězcovou hodnotu celý dotaz definovaný v adrese URL požadavku. |key1 = val1 & key2 = val2 & klíč3 = val3 |
| Odkazující server domény | % {referring_domain} | Určuje doménu definovaná v hlavičce požadavku odkazující server. | <www.google.com> |
| Oblast (žadateli) | %{geo_region} | Označuje oblast žadatele (například stát nebo kraj) prostřednictvím jeho alfanumerické zkratkou. | CA |
| Hodnota hlavičky žádosti | %{http_RequestHeader} | Vrací hodnotu odpovídající identifikován jako RequestHeader hlavičky žádosti. <br /><br />Pokud název hlavičky žádosti obsahuje pomlčku (například uživatelský Agent), nahraďte ho podtržítko (například User_Agent).| Využití vzorků: % {http_Connection}<br /><br />Hodnota vzorku: Keep-Alive | 
| Hostitel požadavku | %{host} | Označuje hostitelů definovaný v adrese URL požadavku. | <www.mydomain.com> |
| Žádost o protokol | %{request_protocol} | Určuje protokol žádosti. | HTTP/1.1 |
| Schéma požadavku | % {scheme} | Určuje schéma požadavku. |http |
| Identifikátor URI (relativní) žádosti | %{request_uri} | Určuje relativní cestu, včetně řetězce dotazu, které jsou definovány v identifikátoru URI požadavku. | /marketing/foo.js?loggedin=true |
| Identifikátor URI (relativní bez řetězce dotazu) žádosti | % {identifikátor uri} | Určuje relativní cestu do požadovaného obsahu. <br /><br/>Informace o klíči:<br />– Tento relativní cesta vyloučí řetězec dotazu.<br />– Tento relativní cesta odráží přepisů adresy URL. Adresa URL bude přepsán při splnění následujících podmínek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;– Funkce přepisování adresa URL: Tato funkce přepíše relativní cestu definovanou v identifikátoru URI požadavku.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Adresa URL CNAME edge: Tento typ požadavku je přepsán na adresu URL odpovídající CDN. |/800001/corigin/rewrittendir/foo.js |
| Identifikátor URI žádosti | % {požadavek} | Popisuje požadavek. <br />Syntaxe: &lt;Metoda HTTP&gt; &lt;relativní cesta&gt; &lt;protokolu HTTP&gt; | ZÍSKAT /marketing/foo.js?loggedin=true HTTP/1.1 |
| Hodnota hlavičky odpovědi | % {resp_&lt;ResponseHeader&gt;} | Vrací hodnotu odpovídající hlavičku odpovědi identifikován &lt;ResponseHeader&gt; termín. <br /><br />Pokud název hlavičky odpovědi obsahuje pomlčku (například uživatelský Agent), nahraďte ho podtržítko (například User_Agent). | Využití vzorků: % {resp_Content_Length}<br /><br />Hodnota vzorku: 100 |

## <a name="usage"></a>Použití
Následující tabulka popisuje správná syntaxe pro určení proměnnou HTTP.


| Syntaxe | Příklad | Popis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Použijte následující syntaxi zobrazíte celou hodnotu odpovídající zadanému &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {hostitele} | Použijte následující syntaxi pro nastavení platí pro celou hodnotu odpovídající zadanému &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Použít regulární výraz pro &lt;HTTPVariableDelimiterExpression&gt; nahradit, odstranit nebo manipulaci s hodnotu proměnné protokolu HTTP. |

Názvy proměnných HTTP podporují jenom abecední znaky a podtržítka. Převeďte nepodporované znaky podtržítka.

## <a name="delimiter-reference"></a>Odkaz na oddělovač
Oddělovač je možné zadat po proměnnou HTTP k dosažení následujících efektů:

- Transformace hodnoty přidružený k proměnné.

     Příklad: Celou hodnotu převeďte na malá písmena.

- Odstraňte hodnotu přidružený k proměnné.

- Manipulace s hodnotou přidružený k proměnné.

     Příklad: Použití regulárních výrazů ke změně hodnoty přidružený k proměnné protokolu HTTP.

Oddělovače jsou popsány v následující tabulce.

| Oddělovač | Popis |
| --------- | ----------- |
| := | Označuje, že výchozí hodnota se přiřadí do proměnné, když je buď: <br />-Chybí <br />– Nastavte na hodnotu NULL. |
| :+ | Označuje, že výchozí hodnota se přiřadí proměnné při jí byla přiřazena hodnota. |
| : | Označuje, že budou rozšířeny podřetězec hodnota přiřazená k proměnné. |
| # | Označuje, že vzor zadat, až se tento oddělovač měla by být odstraněna nalezené na začátku hodnota přidružený k proměnné. |
| % | Označuje, že vzor zadat, až se tento oddělovač měla by být odstraněna, když se nachází na konci hodnotu přidruženou k proměnné. <br />Tato definice lze použít pouze při % symbol se používá jako oddělovač. |
| / | Odděluje citaci proměnnou HTTP nebo vzor. |
| // | Najít a nahradit všechny výskyty zadanému vzoru. |
| /= | Najít, kopírovat a přepište všechny výskyty zadanému vzoru. |
| , | Převeďte hodnotu přidružený k proměnné protokolu HTTP na malá písmena. |
| ^ | Převeďte hodnotu přidružený k proměnné protokolu HTTP na velká písmena. |
| ,, | Převeďte všechny výskyty zadaný znak v hodnotě přidružený k proměnné protokolu HTTP na malá písmena. |
| ^^ | Převeďte všechny výskyty zadaný znak v hodnotě přidružený k proměnné protokolu HTTP na velká písmena. |

## <a name="exceptions"></a>Výjimky
Následující tabulka popisuje podmínek, za kterých zadaný text není považován za proměnnou HTTP.

| Podmínka | Popis | Příklad |
| --------- | ----------- | --------|
| % Symbol uvozovací znaky | Symbol procenta můžete být uvozeny pomocí zpětného lomítka. <br />Ukázková hodnota pravé bude zacházet jako s hodnotou literálu a ne jako proměnnou HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Neznámý proměnné | Pro neznámý proměnné vždy je vrácen prázdný řetězec. | %{unknown_variable} |
| Neplatné znaky nebo syntaxe | Proměnné, které obsahují neplatné znaky nebo syntaxe jsou považovány za hodnoty literálu. <br /><br />Příklad #1: Zadaná hodnota obsahuje neplatné znaky (například-). <br /><br />Příklad #2: Zadaná hodnota obsahuje sadu dvojitých složených závorek. <br /><br />Příklad #3: Zadaná hodnota chybí uzavírací složenou závorku.<br /> | Příklad #1: % {resp_user agenta} <br /><br />Příklad #2: % {{hostitele}} <br /><br />Příklad #3: % {hostitele |
| Chybí název proměnné | Pokud není zadána proměnná vždy vrátí hodnotu NULL. | %{} |
| Koncové znaky | Znaky, které posloupnost proměnné jsou považovány za hodnoty literálu. <br />Ukázková hodnota pravé obsahuje koncové složené závorky, který se bude zacházet jako s hodnotou literálu. | %{host}} |

## <a name="setting-default-header-values"></a>Nastavení výchozích hodnot hlavičky
Výchozí hodnota může být přiřazena hlavičku při splňuje některá z následujících podmínek:
- Chybí nenastavené
- Nastavte na hodnotu NULL.

Následující tabulka popisuje, jak definovat výchozí hodnotu.

| Podmínka | Syntaxe | Příklad | Popis |
| --------- | ------ | --------| ----------- |
| Nastavte hlavičku na výchozí hodnotu, pokud splňuje některá z následujících podmínek: <br /><br />-Chybí hlavička <br /><br />-Header hodnota je nastavena na hodnotu NULL.| % {Proměnné: = Value} | % {http_referrer: = neurčené} | Hlavička odkazujícího serveru bude nastaveno pouze *neurčené* když se nachází buď chybí, nebo nastavte na hodnotu NULL. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud je nalezen. | % {Proměnných = Value} | %{http_referrer=unspecified} | Hlavička odkazujícího serveru bude nastaveno pouze *neurčené* když nebyl nalezen. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud nesplňuje některé z následujících podmínek: <br /><br />-Chybí<br /><br /> – Nastavte na hodnotu NULL. | % {Proměnné: + hodnota} | % {http_referrer: + neurčené} | Hlavička odkazujícího serveru bude nastaveno pouze *neurčené* při hodnotu se přiřadila k němu. Žádná akce bude mít místo, když je buď chybí, nebo nastavte na hodnotu NULL. |

## <a name="manipulating-variables"></a>Manipulace s proměnné
Proměnné lze ovládat následujícími způsoby:

- Rozbalení podřetězců
- Odebrání vzorků

### <a name="substring-expansion"></a>Podřetězec rozšíření
Ve výchozím nastavení proměnné se rozbalí na úplné hodnotu. Použijte následující syntaxi rozšířit pouze podřetězec hodnoty proměnné.

`%<Variable>:<Offset>:<Length>}`

Informace o klíči:

- Hodnota přiřazená k posunu termín Určuje počáteční znak dílčí řetězec:

     - Pozitivní: Počáteční znak podřetězec se počítá od prvního znaku v řetězci.
     - Nulová hodnota: Počáteční znak podřetězce je první znak v řetězci.
     - Záporná: Počáteční znak podřetězec se počítá od poslední znak v řetězci.

- Délka podřetězce je určena *délka* termín:

     - Tento parametr vynechán: Vynechání délka období umožňuje podřetězec zahrnout všechny znaky mezi počáteční znak a konce řetězce.
     - Pozitivní: Určuje dílčí řetězec z počátečního znaku na pravé straně.
     - Záporná: Určuje dílčí řetězec z počátečního znaku na levé straně.

#### <a name="example"></a>Příklad:

V následujícím příkladu se spoléhá na následující adrese URL požadavku vzorku:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Tento řetězec znázorňuje různé metody pro práci s proměnné:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Podle zadané adresy URL požadavku vzorku, výše uvedených proměnných manipulace vytvoří následující hodnotu:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Odebrání vzorku
Text, který odpovídá konkrétnímu vzor je odebrat ze začátku nebo konci hodnota proměnné.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable#Pattern} | Odeberte text, pokud je nalezen zadaný vzor na začátku hodnota proměnné. |
| %{Variable%Pattern} | Odeberte text, pokud je nalezen zadaný vzor na konci hodnota proměnné. |

#### <a name="example"></a>Příklad:

V tomto ukázkovém scénáři *request_uri* proměnná je nastavená na:

`/800001/myorigin/marketing/product.html?language=en-US`

Následující tabulka ukazuje, jak tato syntaxe funguje.

| Ukázky syntaxe | Výsledky | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/Product.HTML?Language=en-us | Protože proměnné, spustí se vzorem, byl nahrazen. |
| %{request_uri%html}htm | /800001/myorigin/marketing/Product.HTML?Language=en-us | Protože proměnné ale nekončí u vzor, že žádná změna nenastala.|

### <a name="find-and-replace"></a>Najít a nahradit
Syntaxe najít a nahradit je popsané v následující tabulce.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable/Find/replace} | Najít a nahradit první výskyt zadaného vzoru. |
| %{Variable//Find/replace} | Najít a nahradit všechny výskyty zadanému vzoru. |
| %{Variable^} |Celou hodnotu převeďte na velká písmena. |
| %{Variable^Find} | Převeďte na velká písmena první výskyt zadaného vzoru. |
| % {Proměnné} | Celou hodnotu převeďte na malá písmena. |
| {Proměnná, najít} % | První výskyt zadaného vzoru převeďte na malá písmena. |

### <a name="find-and-rewrite"></a>Najít a revize
Variantou najít a nahradit pomocí text, který odpovídá zadanému vzoru, pokud ji přepisuje. Syntaxe hledání a přepište je popsané v následující tabulce.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable/=Find/rewrite} | Najít, kopírovat a přepište všechny výskyty zadanému vzoru. |
| %{Variable/^Find/rewrite} | Najít, kopírování a přepište zadaný vzor dojde na začátku proměnné. |
| %{Variable/$Find/rewrite} | Najít, kopírování a přepište zadaný vzor dojde na konci proměnné. |
| %{Variable/Find} | Vyhledat a odstranit všechny výskyty zadanému vzoru. |

Informace o klíči:

- Rozbalte text, který odpovídá zadanému vzoru tak, že zadáte znak dolaru následovaný celá čísla (třeba 1 USD).

- Je možné zadat více vzorců. Pořadí, ve kterém je zadaný vzor Určuje celé číslo, které bude přiřazeno k němu. V následujícím příkladu první porovnávání vzorů "www,". druhý vzor odpovídá domény druhé úrovně, a třetí vzor odpovídá domény nejvyšší úrovně:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Přepsaný hodnota může obsahovat libovolnou kombinaci textu a tyto zástupné symboly.

    V předchozím příkladu je název hostitele přepsány, aby `cdn.$2.$3:80` (například cdn.mydomain.com:80).

- Případ zástupný vzor (například 1 USD) může být upraveno pomocí následujících příznaků:
     - U: Velká hodnota rozbalený.

         Ukázky syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Malá písmena rozšířené hodnotu.

         Ukázky syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operátor je třeba zadat před vzor. Zadaný operátor určuje chování zachytávání vzoru:

     - `=`: Označuje, že všechny výskyty zadaný vzor musí být zachycena a přepsán.
     - `^`: Označuje, že se dají zachytit pouze text, který se spustí se zadaným vzorem.
     - `$`: Určuje, že pouze text, který končí zadaný vzor zachycení.
 
- Vynecháte-li */přepisování* hodnotu, text, který se shoduje se vzorem se odstraní.


