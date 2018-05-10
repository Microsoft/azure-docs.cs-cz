---
title: Proměnné HTTP pro stroj pravidel Azure CDN | Microsoft Docs
description: Proměnné HTTP umožní načíst metadata žádosti a odpovědi HTTP.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: v-deasim
ms.openlocfilehash: ea7469b1d1c3d1c20beca9b1fb3bef0d4dac9492
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Proměnné HTTP pro stroj pravidel Azure CDN
Proměnné HTTP prostředkem, pomocí kterého můžete načíst metadata žádosti a odpovědi HTTP. Tato metadata pak lze dynamicky upravit požadavek nebo odpověď. Použití proměnných HTTP je omezen na tyto funkce stroj pravidel:

- [Přepište klíče mezipaměti](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Upravit hlavička požadavku klienta](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Upravit hlavičku odpovědi klienta](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Adresa URL přesměrování](cdn-rules-engine-reference-features.md#url-redirect)
- [Přepisování adres URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definice
Následující tabulka popisuje podporované proměnné HTTP. Na prázdnou hodnotu se vrátí, když Geograficky metadata (například PSČ) není k dispozici pro konkrétní žádost.


| Název | Proměnná | Popis | Ukázková hodnota |
| ---- | -------- | ----------- | ------------ |
| Číslo ASN (žadateli) | % {geo_asnum} | Označuje žadatele jako číslo. <br /><br />**Zastaralé:** % {virt_dst_asnum}. <br />Tato proměnná je zastaralá považuje % {geo_asnum}. I když pravidlo, které používá tato proměnná nepoužívané budou nadále fungovat, měli byste ho na použití nové proměnné aktualizovat. | AS15133 |
| Město (žadateli) | % {geo_city} | Označuje města žadatele. | Los Angeles |
| Kontinentě (žadateli) | % {geo_continent} | Označuje žadatele kontinentě prostřednictvím jeho zkratka. <br />Platné hodnoty jsou: <br />AF: Afrika<br />AS: Asie<br />Evropa: Evropa<br />NA: Severní Amerika<br />° C: Oceánie<br />SA: Jižní Amerika<br /><br />**Zastaralé:** % {virt_dst_continent}. <ber />Tato proměnná je zastaralá považuje % {geo_continent}. <br />I když pravidlo, které používá tato proměnná nepoužívané budou nadále fungovat, měli byste ho na použití nové proměnné aktualizovat.| neuvedeno |
| Hodnota souboru cookie | % {cookie_Cookie} | Vrátí hodnotu odpovídající klíč souboru cookie identifikovaný termín souboru Cookie. | Využití vzorků: <br />% {cookie__utma}<br /><br />Hodnota vzorku:<br />111662281.2.10.1222100123 |
| Země (žadateli) | % {geo_country} | Určuje zemi žadatele původu prostřednictvím jeho kód země. <br />**Zastaralé:** % {virt_dst_country}. <br /><br />Tato proměnná je zastaralá považuje % {geo_country}. I když pravidlo, které používá tato proměnná nepoužívané budou nadále fungovat, měli byste ho na použití nové proměnné aktualizovat. | USA |
| Oblasti určené trhu (žadateli) | % {geo_dma_code} |Označuje žadatele média trhu podle jeho kód oblasti. <br /><br />Toto pole je jenom pro požadavky, které pocházejí z USA.| 745 |
| Metoda požadavku HTTP | % {request_method} | Určuje metodu požadavku HTTP. | GET |
| Kód stavu HTTP | % {status} | Označuje stavový kód HTTP pro odpověď. | 200 |
| IP adresa (žadateli) | % {virt_dst_addr} | Označuje žadatele IP adresu. | 192.168.1.1 |
| Zeměpisná šířka (žadateli) | % {geo_latitude} | Určuje zeměpisnou šířku žadatele. | 34.0995 |
| Zeměpisná délka (žadateli) | % {geo_longitude} | Určuje zeměpisnou délku žadatele. | -118.4143 |
| Metropolitní statistické (žadateli) | % {geo_metro_code} | Označuje metropolitní oblasti žadatele. <br /><br />Toto pole je jenom pro požadavky, které pocházejí z USA.<br />| 745 |
| Port (žadateli) | % {virt_dst_port} | Určuje dočasný port žadatele. | 55885 |
| PSČ (žadateli) | % {geo_postal_code} | Určuje poštovní směrovací číslo žadatele. | 90210 |
| Řetězec dotazu nalezen | % {is_args} | Hodnotu pro tuto proměnnou závisí na tom, zda požadavek obsahuje řetězec dotazu.<br /><br />-Nalezen řetězec dotazu:?<br />-Žádný řetězec dotazu: hodnotu NULL. | ? |
| Parametr řetězce dotazu nalezen | % {is_amp} | Hodnotu pro tuto proměnnou závisí na tom, zda požadavek obsahuje alespoň jeden parametr řetězce dotazu.<br /><br />– Parametr najít: &<br />-Žádné parametry: hodnotu NULL. | & |
| Hodnotu parametru řetězce dotazu | % {arg_&lt;parametr&gt;} | Vrátí hodnotu odpovídající parametr řetězce dotazu, který se identifikovanou pomocí &lt;parametr&gt; termín. | Využití vzorků: <br />% {arg_language}<br /><br />Parametr řetězce dotazu ukázka: <br />? jazyk = en<br /><br />Ukázkové hodnoty: en |
| Hodnotu řetězce dotazu | % {query_string} | Určuje hodnotu řetězce dotazu celý definované v adrese URL žádosti. |key1 = hodnota1 & key2 = hodnota2 & klíč3 = val3 |
| Odkazující server domény | % {referring_domain} | Určuje doménu definované v hlavičce požadavku odkazující server. | www.google.com |
| Oblast (žadateli) | % {geo_region} | Určuje oblast žadatele (například stát nebo provincii) prostřednictvím jeho alfanumerické zkratka. | CA |
| Hodnota hlavičky požadavku | % {http_RequestHeader} | Vrátí hodnotu odpovídající identifikovaný termín RequestHeader hlavičky žádosti. <br /><br />Pokud název hlavičky žádosti obsahuje pomlčkou (například User-Agent), nahraďte ji metodou podtržítko (například User_Agent).| Využití vzorků: % {http_Connection}<br /><br />Hodnota vzorku: Keep-Alive | 
| Hostitel požadavku | % {hostitele} | Určuje hostitele definované v adrese URL žádosti. | www.mydomain.com |
| Žádost protokolu | % {request_protocol} | Určuje protokol požadavku. | PROTOKOL HTTP/1.1 |
| Schéma požadavku | % {schéma} | Určuje schéma požadavku. |http |
| Žádost o identifikátor URI (relativní) | % {request_uri} | Určuje relativní cestu, včetně řetězce dotazu, který je definován v identifikátoru URI požadavku. | /Marketing/foo.js?loggedin=true |
| Žádost o identifikátor URI (relativní vůči bez řetězce dotazu) | % {uri} | Určuje relativní cestu k požadovaným obsahem. <br /><br/>Informace o klíči:<br />– Tato relativní cestu vyloučí řetězec dotazu.<br />– Tato relativní cesta odráží přepisů adresy URL. Adresa URL bude přepsaná za následujících podmínek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;– Funkce přepisování adresa URL: Tato funkce přepíše relativní cestu definovanou v identifikátoru URI požadavku.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Adresa URL CNAME edge: Tento typ požadavku je přepisuje, aby adresa URL odpovídajícího CDN. |/800001/corigin/rewrittendir/foo.js |
| Identifikátor URI žádosti | % {žádost} | Popisuje požadavek. <br />Syntaxe: `HTTPMethod RelativePath Protocol` | ZÍSKAT /marketing/foo.js?loggedin=true HTTP/1.1. |
| Hodnota hlavičky odpovědi | % {resp_&lt;ResponseHeader&gt;} | Vrátí hodnotu odpovídající hlavičku odpovědi se identifikovanou pomocí &lt;ResponseHeader&rt; termín. <br /><br />Pokud název hlavičky odpovědi obsahuje pomlčkou (například User-Agent), nahraďte ji metodou podtržítko (například User_Agent). | Využití vzorků: % {resp_Content_Length}<br /><br />Ukázkové hodnoty: 100 |

## <a name="usage"></a>Využití
Následující tabulka popisuje správnou syntaxi pro zadání Proměnná HTTP.


| Syntaxe | Příklad: | Popis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | % {hostitele} | Pomocí této syntaxe můžete získat celou hodnotu odpovídající zadanému &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {hostitele} | Pomocí této syntaxe nastavte případ celou hodnotu odpovídající zadanému &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Použijte regulární výraz pro &lt;HTTPVariableDelimiterExpression&gt; nahradit, odstranit nebo upravit hodnoty proměnné HTTP. |

Názvy proměnných HTTP podporují pouze znaky abecedy a podtržítka. Převeďte nepodporované znaky podtržítka.

## <a name="delimiter-reference"></a>Oddělovač odkaz
Oddělovač lze zadat po Proměnná HTTP k dosažení následující důsledky:

- Transformace hodnota přidružený k proměnné.

     Příklad: Celou hodnotu převeďte na malá písmena.

- Odstraňte hodnotu přidružený k proměnné.

- Upravit hodnotu přidružený k proměnné.

     Příklad: Použití regulárních výrazů ke změně hodnoty přidružený k proměnné HTTP.

Oddělovače jsou popsané v následující tabulce.

| Oddělovač | Popis |
| --------- | ----------- |
| := | Označuje, že výchozí hodnotu přiřadit proměnnou, když je buď: <br />-Chybí <br />– Nastavte na hodnotu NULL. |
| :+ | Označuje, že výchozí hodnotu přiřazen do proměnné, když jí byla přiřazena hodnota. |
| : | Označuje, že s podřetězcem hodnotu přiřazenou proměnné bude rozšířena. |
| # | Označuje, že mají být odstraněny vzorem zadaným po této oddělovač nalezený na začátku hodnoty přidružený k proměnné. |
| % | Označuje, že mají být odstraněny vzorem zadaným po této oddělovač nalezený na konci hodnoty přidružený k proměnné. <br />Tuto definici lze použít pouze při % symbol se používá jako oddělovač. |
| / | Vymezuje Proměnná HTTP nebo vzor. |
| // | Najít a nahradit všechny instance zadanému vzoru. |
| /= | Najít, kopírování a přepište všechny výskyty zadanému vzoru. |
| , | Převeďte hodnotu přidružený k proměnné HTTP na malá písmena. |
| ^ | Převeďte hodnotu přidružený k proměnné HTTP na velká písmena. |
| ,, | Převeďte všechny instance je zadaný znak v poli hodnota přidružený k proměnné HTTP na malá písmena. |
| ^^ | Převeďte všechny instance je zadaný znak v poli hodnota přidružený k proměnné HTTP na velká písmena. |

## <a name="exceptions"></a>Výjimky
Následující tabulka popisuje podmínek, za kterých zadaný text nepovažuje se za Proměnná HTTP.

| Podmínka | Popis | Příklad: |
| --------- | ----------- | --------|
| % Symbol uvozovací znaky | Symbol procenta můžete uvozené prostřednictvím zpětné lomítko. <br />Ukázková hodnota pravé nakládáno jako literálovou hodnotou a ne jako na proměnnou HTTP.| \%{hostitele} |
| Neznámý proměnné | Pro neznámý proměnné vždy je vrácen prázdný řetězec. | % {unknownvariable} |
| Syntaxe nebo neplatné znaky. | Proměnné, které obsahují neplatné znaky nebo syntaxe jsou zpracovány jako literálových hodnot. <br /><br />Příklad #1: Zadaná hodnota obsahuje neplatný znak (například-). <br /><br />Příklad #2: Zadaná hodnota obsahuje sadu dvojité složené závorky. <br /><br />Příklad #3: Zadaná hodnota chybí uzavírací složené závorky.<br /> | Příklad #1: % {resp_user-agent} <br /><br />Příklad #2: % {{hostitele}} <br /><br />Příklad #3: % {hostitele |
| Chybí název proměnné | Pokud není zadán proměnné vždy vrácená hodnota NULL. | %{} |
| Koncové znaky | Znaky, které sledu proměnné jsou považovány za literálových hodnot. <br />Ukázka hodnota pravé obsahuje konci složené závorky, který bude považována za literálovou hodnotou. | % {hostitele}} |

## <a name="setting-default-header-values"></a>Nastavení výchozích hodnot hlavičky
Výchozí hodnota je možné přiřadit do záhlaví při splnění některé z následujících podmínek:
- Chybí nastavení
- Nastavte na hodnotu NULL.

Následující tabulka popisuje, jak definovat výchozí hodnotu.

| Podmínka | Syntaxe | Příklad: | Popis |
| --------- | ------ | --------| ----------- |
| Nastavte hlavičku na výchozí hodnotu, při splnění některé z následujících podmínek: <br /><br />-Chybí záhlaví <br /><br />-Hodnota hlavičky nastavena na hodnotu NULL.| % {Proměnné: = hodnota} | % {HTTP_REFERER, abyste: = neurčené} | Odkazující server záhlaví bude nastaveno pouze *neurčené* když je chybí nebo je nastavený na hodnotu NULL. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud není nalezena. | % {Proměnné = hodnota} | % {HTTP_REFERER, abyste = neurčené} | Odkazující server záhlaví bude nastaveno pouze *neurčené* při nebyl nalezen. Žádná akce bude provedena, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud nesplňuje některé z následujících podmínek: <br /><br />-Chybí<br /><br /> – Nastavte na hodnotu NULL. | % {Proměnné: + hodnota} | % {HTTP_REFERER, abyste: + neurčené} | Odkazující server záhlaví bude nastaveno pouze *neurčené* Pokud hodnotu přiřazen k němu. Žádná akce bude mít místní, když je chybí nebo je nastavený na hodnotu NULL. |

## <a name="manipulating-variables"></a>Manipulace s proměnné
Proměnné práce s následujícími způsoby:

- Rozšiřování dílčích řetězců
- Odebrání vzorků

### <a name="substring-expansion"></a>Substring – rozšíření
Ve výchozím nastavení proměnné rozšíří na úplné hodnotu. Použijte následující syntaxi pouze rozbalte podřetězcem hodnota proměnné.

`%<Variable>:<Offset>:<Length>}`

Informace o klíči:

- Hodnota přiřazená k posunu termín Určuje počáteční znak dílčí řetězec:

     - Kladné: Počátečního znaku dílčí řetězec se počítá z první znak v řetězci.
     - Nulová hodnota: Počátečního znaku dílčí řetězec je první znak v řetězci.
     - Záporné: Počátečního znaku dílčí řetězec se počítá z poslední znak v řetězci.

- Délka dílčí řetězec je dáno *délka* termín:

     - Tento parametr vynechán: Vynechání termín délka umožňuje dílčí řetězec zahrnout všechny znaky mezi počáteční a koncové řetězce.
     - Kladné: Určuje dílčí řetězec z počátečního znaku vpravo.
     - Záporné: Určuje dílčí řetězec z počátečního znaku vlevo.

#### <a name="examples"></a>Příklady:

Následující příklad spoléhá na následující adrese URL žádosti ukázka:

`https://cdn.mydomain.com/folder/marketing/myconsultant/proposal.html`

Následující řetězec ukazuje různé metody pro manipulaci s proměnné:

`https://www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm`

Založená na adrese URL žádosti ukázkové, výše uvedené proměnné manipulaci vytvoří následující hodnotu:

`https://www.mydomain.com/mobile/marketing/proposal.htm`


### <a name="pattern-removal"></a>Odebrání vzorku
Text, který odpovídá konkrétní vzoru lze odebrat ze začátku nebo konci hodnoty proměnné.

| Syntaxe | Akce |
| ------ | ------ |
| % {Proměnná #Pattern} | Odeberte text zadaný vzor nalezený na začátku hodnoty proměnné. |
| % {Proměnné % vzor} | Odeberte text při zadanému vzoru se nachází na konci hodnoty proměnné. |

#### <a name="example"></a>Příklad:

V tomto scénáři ukázka proměnnou request_uri je nastaven na:

`/800001/myorigin/marketing/product.html?language=en-US`

Následující tabulka ukazuje, jak tato syntaxe funguje.

| Ukázka syntaxe | Výsledky |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/Product.HTML?Language=en-us | Vzhledem k tomu, že proměnná začíná vzor, byl nahrazen. |
| % {request_uri % html} htm | /800001/myorigin/marketing/Product.HTML?Language=en-us | Vzhledem k tomu, že proměnná nemá končit vzor, se žádná změna.|

### <a name="find-and-replace"></a>Najít a nahradit
Syntaxe najít a nahradit je popsané v následující tabulce.

| Syntaxe | Akce |
| ------ | ------ |
| %{Variable/Find/replace} | Najít a nahradit první výskyt zadanému vzoru. |
| %{Variable//Find/replace} | Najít a nahradit všechny výskyty zadanému vzoru. |
| % {Proměnné ^} |Celou hodnotu převeden na velká písmena. |
| % {Proměnné ^ najít} | První výskyt zadaný vzor převeden na velká písmena. |
| % {Proměnnou} | Celou hodnotu převeden na malá písmena. |
| % {Proměnnou, najít} | První výskyt zadaný vzor převeden na malá písmena. |

### <a name="find-and-rewrite"></a>Najít a přepsání
Variace na Najít a nahradit použijte text, který odpovídá zadanému vzoru při přepisování ho. Najít a přepište syntaxe je popsané v následující tabulce.

| Syntaxe | Akce |
| ------ | ------ |
| %{Variable/=Find/rewrite} | Najít, kopírování a přepište všechny výskyty zadanému vzoru. |
| %{Variable/^Find/rewrite} | Najít, kopírování a přepište zadaný vzor případě začátku proměnnou. |
| %{Variable/$Find/rewrite} | Najít, kopírování a přepisování zadaný vzor, když dojde ke konci proměnnou. |
| %{Variable/Find} | Vyhledejte a odstraňte všechny výskyty zadanému vzoru. |

Informace o klíči:

- Rozbalte text, který odpovídá zadanému vzoru zadáním dolaru, za nímž následuje celé číslo (například 1 USD).

- Je možné zadat více vzorů. Pořadí, ve které je uvedené vzor Určuje celé číslo, které bude přiřazeno k němu. V následujícím příkladu první vzor odpovídá "www.," druhý vzor odpovídá domény druhé úrovně a třetí vzor odpovídá nejvyšší domény:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Rewritten hodnota může obsahovat libovolnou kombinaci textu a tyto zástupné symboly.

    V předchozím příkladu je název hostitele přepisuje, aby `cdn.$2.$3:80` (například cdn.mydomain.com:80).

- V případě vzoru zástupný znak (například 1 USD) se dají změnit pomocí následujících příznaků:
     - U: velká rozšířené hodnota.

         Ukázka syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: malá písmena rozšířené hodnota.

         Ukázka syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operátor je třeba zadat před vzoru. Zadaný operátor určuje chování zaznamenávání vzoru:

     - `=`: Určuje, že musí být všechny výskyty zadaný vzor zachytit a přepsaná.
     - `^`: Určuje, že bude možné zaznamenat pouze text, který začíná zadanému vzoru.
     - `$`: Určuje, že pouze text, který končí se zadaným vzorem zachycení.
 
- V případě vynechání */přepisování* hodnota text, který odpovídá vzorku odstraněna.


