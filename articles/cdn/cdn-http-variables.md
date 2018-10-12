---
title: Proměnné protokolu HTTP pro stroj pravidel Azure CDN | Dokumentace Microsoftu
description: Proměnné protokolu HTTP umožňují načíst metadata žádostí a odpovědí HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 39084fd8408a123e8152ad96fa92025fd04ed42b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092809"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Proměnné protokolu HTTP pro Azure CDN stroje pravidel
HTTP proměnné poskytují prostředky, pomocí kterého můžete získat metadata žádostí a odpovědí HTTP. Tato metadata pak lze dynamicky měnit požadavek nebo odpověď. Použití proměnných HTTP je omezen na následující funkce stroje pravidel:

- [Přepište klíč mezipaměti](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Upravit hlavičku požadavku klienta](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Upravit klienta hlavička odpovědi](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Adresa URL pro přesměrování](cdn-rules-engine-reference-features.md#url-redirect)
- [Přepsání adresy URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definice
Následující tabulka popisuje podporované proměnné protokolu HTTP. Při Geograficky metadata (například PSČ) není k dispozici pro konkrétní žádost byla vrácena prázdná hodnota.


| Název | Proměnná | Popis | Ukázková hodnota |
| ---- | -------- | ----------- | ------------ |
| Číslo ASN (žadateli) | % {geo_asnum} | Označuje žadatele jako číslo. <br /><br />**Zastaralé:** % {virt_dst_asnum}. <br />Tato proměnná je zastaralá a místo toho použití % {geo_asnum}. I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné. | AS15133 |
| Město (žadateli) | % {geo_city} | Označuje žadatele město. | Los Angeles |
| Kontinent (žadateli) | % {geo_continent} | Označuje žadatele kontinent prostřednictvím jeho zkratkou. <br />Platné hodnoty jsou: <br />AF: Afrika<br />AS: Asie<br />Evropa: Evropa<br />Není k dispozici: Severní Amerika<br />OS: Oceánie<br />SA: Jižní Amerika<br /><br />**Zastaralé:** % {virt_dst_continent}. <ber />Tato proměnná je zastaralá a místo toho použití % {geo_continent}. <br />I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné.| neuvedeno |
| Hodnota souboru cookie | % {cookie_Cookie} | Vrací hodnotu odpovídající klíč souboru cookie, který je identifikován jako souboru Cookie. | Využití vzorků: <br />% {cookie__utma}<br /><br />Hodnota vzorku:<br />111662281.2.10.1222100123 |
| Země (žadateli) | % {geo_country} | Označuje žadatele země původu prostřednictvím jeho směrové číslo země. <br />**Zastaralé:** % {virt_dst_country}. <br /><br />Tato proměnná je zastaralá a místo toho použití % {geo_country}. I když pravidlo, které používá tento nepoužívané proměnnou budou nadále fungovat, měli byste aktualizovat ho na použití nové proměnné. | USA |
| Určený trh oblasti (žadateli) | % {geo_dma_code} |Označuje žadatele média trhu podle oblasti kódu. <br /><br />Toto pole platí pouze pro požadavky, které pocházejí z USA.| 745 |
| Metoda požadavku HTTP | % {request_method} | Označuje metodu požadavku HTTP. | GET |
| Kód stavu HTTP | % {status} | Určuje kód stavu HTTP pro odpověď. | 200 |
| IP adresa (žadateli) | % {virt_dst_addr} | Označuje žadatele IP adresu. | 192.168.1.1 |
| Zeměpisná šířka (žadateli) | % {geo_latitude} | Určuje zeměpisnou šířku žadatele. | 34.0995 |
| Zeměpisná délka (žadateli) | % {geo_longitude} | Určuje zeměpisnou délku žadatele. | -118.4143 |
| Statistické metropolitní oblasti (žadateli) | % {geo_metro_code} | Označuje metropolitní oblasti žadatele. <br /><br />Toto pole platí pouze pro požadavky, které pocházejí z USA.<br />| 745 |
| Port (žadateli) | % {virt_dst_port} | Určuje dočasný port žadatele. | 55885 |
| Poštovní směrovací číslo (žadateli) | % {geo_postal_code} | Určuje poštovní směrovací číslo žadatele. | 90210 |
| Řetězec dotazu nalezen | % {is_args} | Hodnota pro tuto proměnnou, se liší podle Určuje, zda žádost obsahuje řetězec dotazu.<br /><br />-Nalezen řetězec dotazu:?<br />-Žádný řetězec dotazu: NULL | ? |
| Nebyl nalezen parametr řetězce dotazu | % {is_amp} | Hodnota pro tuto proměnnou se liší podle Určuje, zda požadavek obsahuje nejméně jeden parametr řetězce dotazu.<br /><br />-Nebyl nalezen parametr: &<br />-Žádné parametry: NULL | & |
| Hodnotu parametru řetězce dotazu | % {arg_&lt;parametr&gt;} | Vrací hodnotu odpovídající parametru řetězce dotazu, který je identifikován &lt;parametr&gt; termín. | Využití vzorků: <br />% {arg_language}<br /><br />Ukázka parametru řetězce dotazu: <br />? jazyk = en<br /><br />Ukázkové hodnoty: cs |
| Hodnota řetězce dotazu | % {query_string} | Určuje řetězcovou hodnotu celý dotaz definovaný v adrese URL požadavku. |key1 = val1 & key2 = val2 & klíč3 = val3 |
| Odkazující server domény | % {referring_domain} | Určuje doménu definovaná v hlavičce požadavku odkazující server. | www.google.com |
| Oblast (žadateli) | % {geo_region} | Označuje oblast žadatele (například stát nebo kraj) prostřednictvím jeho alfanumerické zkratkou. | CA |
| Hodnota hlavičky žádosti | % {http_RequestHeader} | Vrací hodnotu odpovídající identifikován jako RequestHeader hlavičky žádosti. <br /><br />Pokud název hlavičky žádosti obsahuje pomlčku (například uživatelský Agent), nahraďte ho podtržítko (například User_Agent).| Využití vzorků: % {http_Connection}<br /><br />Hodnota vzorku: Keep-Alive | 
| Hostitel požadavku | % {hostitele} | Označuje hostitelů definovaný v adrese URL požadavku. | www.mydomain.com |
| Žádost o protokol | % {request_protocol} | Určuje protokol žádosti. | HTTP/1.1 |
| Schéma požadavku | % {scheme} | Určuje schéma požadavku. |http |
| Identifikátor URI (relativní) žádosti | % {request_uri} | Určuje relativní cestu, včetně řetězce dotazu, které jsou definovány v identifikátoru URI požadavku. | /Marketing/foo.js?loggedin=true |
| Identifikátor URI (relativní bez řetězce dotazu) žádosti | % {identifikátor uri} | Určuje relativní cestu do požadovaného obsahu. <br /><br/>Informace o klíči:<br />– Tento relativní cesta vyloučí řetězec dotazu.<br />– Tento relativní cesta odráží přepisů adresy URL. Adresa URL bude přepsán při splnění následujících podmínek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;– Funkce adresa URL revize: Tato funkce přepíše relativní cestu definovanou v identifikátoru URI požadavku.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Adresa URL CNAME edge: Tento typ požadavku je přepsán na adresu URL odpovídající CDN. |/800001/corigin/rewrittendir/foo.js |
| Identifikátor URI žádosti | % {požadavek} | Popisuje požadavek. <br />Syntaxe: &lt;metodu HTTP&gt; &lt;relativní cesta&gt; &lt;protokolu HTTP&gt; | ZÍSKAT /marketing/foo.js?loggedin=true HTTP/1.1 |
| Hodnota hlavičky odpovědi | % {resp_&lt;ResponseHeader&gt;} | Vrací hodnotu odpovídající hlavičku odpovědi identifikován &lt;ResponseHeader&gt; termín. <br /><br />Pokud název hlavičky odpovědi obsahuje pomlčku (například uživatelský Agent), nahraďte ho podtržítko (například User_Agent). | Využití vzorků: % {resp_Content_Length}<br /><br />Ukázkové hodnoty: 100 |

## <a name="usage"></a>Využití
Následující tabulka popisuje správná syntaxe pro určení proměnnou HTTP.


| Syntaxe | Příklad: | Popis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | % {hostitele} | Použijte následující syntaxi zobrazíte celou hodnotu odpovídající zadanému &lt;HTTPVariable&gt;. |
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
| ,  | Převeďte hodnotu přidružený k proměnné protokolu HTTP na malá písmena. |
| ^ | Převeďte hodnotu přidružený k proměnné protokolu HTTP na velká písmena. |
| ,, | Převeďte všechny výskyty zadaný znak v hodnotě přidružený k proměnné protokolu HTTP na malá písmena. |
| ^^ | Převeďte všechny výskyty zadaný znak v hodnotě přidružený k proměnné protokolu HTTP na velká písmena. |

## <a name="exceptions"></a>Výjimky
Následující tabulka popisuje podmínek, za kterých zadaný text není považován za proměnnou HTTP.

| Podmínka | Popis | Příklad: |
| --------- | ----------- | --------|
| % Symbol uvozovací znaky | Symbol procenta můžete být uvozeny pomocí zpětného lomítka. <br />Ukázková hodnota pravé bude zacházet jako s hodnotou literálu a ne jako proměnnou HTTP.| \%{hostitele} |
| Neznámý proměnné | Pro neznámý proměnné vždy je vrácen prázdný řetězec. | % {unknownvariable} |
| Neplatné znaky nebo syntaxe | Proměnné, které obsahují neplatné znaky nebo syntaxe jsou považovány za hodnoty literálu. <br /><br />Příklad #1: Zadaná hodnota obsahuje neplatný znak (například-). <br /><br />Příklad #2: Zadaná hodnota obsahuje sadu dvojitých složených závorek. <br /><br />Příklad #3: Zadaná hodnota chybí uzavírací složenou závorku.<br /> | Příklad #1: % {resp_user agenta} <br /><br />Příklad #2: % {{hostitele}} <br /><br />Příklad #3: % {hostitele |
| Chybí název proměnné | Pokud není zadána proměnná vždy vrátí hodnotu NULL. | %{} |
| Koncové znaky | Znaky, které posloupnost proměnné jsou považovány za hodnoty literálu. <br />Ukázková hodnota pravé obsahuje koncové složené závorky, který se bude zacházet jako s hodnotou literálu. | % {hostitele}} |

## <a name="setting-default-header-values"></a>Nastavení výchozích hodnot hlavičky
Výchozí hodnota může být přiřazena hlavičku při splňuje některá z následujících podmínek:
- Chybí nenastavené
- Nastavte na hodnotu NULL.

Následující tabulka popisuje, jak definovat výchozí hodnotu.

| Podmínka | Syntaxe | Příklad: | Popis |
| --------- | ------ | --------| ----------- |
| Nastavte hlavičku na výchozí hodnotu, pokud splňuje některá z následujících podmínek: <br /><br />-Chybí hlavička <br /><br />-Header hodnota je nastavena na hodnotu NULL.| % {Proměnné: = Value} | % {HTTP_REFERER, abyste: = neurčené} | Hlavička odkazující strany se nastaví jenom *neurčené* když se nachází buď chybí, nebo nastavte na hodnotu NULL. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud je nalezen. | % {Proměnných = Value} | % {HTTP_REFERER, abyste = neurčené} | Hlavička odkazující strany se nastaví jenom *neurčené* když nebyl nalezen. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud nesplňuje některé z následujících podmínek: <br /><br />-Chybí<br /><br /> – Nastavte na hodnotu NULL. | % {Proměnné: + hodnota} | % {HTTP_REFERER, abyste: + neurčené} | Hlavička odkazující strany se nastaví jenom *neurčené* při hodnotu se přiřadila k němu. Žádná akce bude mít místo, když je buď chybí, nebo nastavte na hodnotu NULL. |

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
     - Nula: Počátečního znaku podřetězce je první znak v řetězci.
     - Záporná: Počáteční znak podřetězec se počítá od poslední znak v řetězci.

- Délka podřetězce je určena *délka* termín:

     - Tento parametr vynechán: Vynechání délka období umožňuje podřetězec zahrnout všechny znaky mezi počáteční znak a konce řetězce.
     - Pozitivní: Určuje dílčí řetězec z počátečního znaku na pravé straně.
     - Záporná: Určuje dílčí řetězec z počátečního znaku na levé straně.

#### <a name="example"></a>Příklad:

V následujícím příkladu se spoléhá na následující adrese URL požadavku vzorku:

protokol https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Tento řetězec znázorňuje různé metody pro práci s proměnné:

protokol https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Podle zadané adresy URL požadavku vzorku, výše uvedených proměnných manipulace vytvoří následující hodnotu:

protokol https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Odebrání vzorku
Text, který odpovídá konkrétnímu vzor je odebrat ze začátku nebo konci hodnota proměnné.

| Syntaxe | Akce |
| ------ | ------ |
| {Proměnná #Pattern} % | Odeberte text, pokud je nalezen zadaný vzor na začátku hodnota proměnné. |
| % {Model proměnná %} | Odeberte text, pokud je nalezen zadaný vzor na konci hodnota proměnné. |

#### <a name="example"></a>Příklad:

V tomto ukázkovém scénáři *request_uri* proměnná je nastavená na:

`/800001/myorigin/marketing/product.html?language=en-US`

Následující tabulka ukazuje, jak tato syntaxe funguje.

| Ukázky syntaxe | Výsledky |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/Product.HTML?Language=en-us | Protože proměnné, spustí se vzorem, byl nahrazen. |
| htm % {request_uri % html} | /800001/myorigin/marketing/Product.HTML?Language=en-us | Protože proměnné ale nekončí u vzor, že žádná změna nenastala.|

### <a name="find-and-replace"></a>Najít a nahradit
Syntaxe najít a nahradit je popsané v následující tabulce.

| Syntaxe | Akce |
| ------ | ------ |
| %{Variable/Find/replace} | Najít a nahradit první výskyt zadaného vzoru. |
| %{Variable//Find/replace} | Najít a nahradit všechny výskyty zadanému vzoru. |
| % {Proměnné ^} |Celou hodnotu převeďte na velká písmena. |
| % {Proměnné ^ najít} | Převeďte na velká písmena první výskyt zadaného vzoru. |
| % {Proměnné} | Celou hodnotu převeďte na malá písmena. |
| {Proměnná, najít} % | První výskyt zadaného vzoru převeďte na malá písmena. |

### <a name="find-and-rewrite"></a>Najít a revize
Variantou najít a nahradit pomocí text, který odpovídá zadanému vzoru, pokud ji přepisuje. Syntaxe hledání a přepište je popsané v následující tabulce.

| Syntaxe | Akce |
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
     - U: velká hodnota rozbalený.

         Ukázky syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: malá rozšířené hodnotu.

         Ukázky syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operátor je třeba zadat před vzor. Zadaný operátor určuje chování zachytávání vzoru:

     - `=`: Označuje, že všechny výskyty zadaný vzor musí být zachycena a přepsán.
     - `^`: Označuje, že se dají zachytit pouze text, který se spustí se zadaným vzorem.
     - `$`: Určuje, že pouze text, který končí zadaný vzor zachycení.
 
- Vynecháte-li */přepisování* hodnotu, text, který se shoduje se vzorem se odstraní.


