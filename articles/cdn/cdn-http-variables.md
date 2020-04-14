---
title: Proměnné HTTP pro modul pravidel Azure CDN | Dokumenty společnosti Microsoft
description: Proměnné HTTP umožňují načíst metadata požadavků HTTP a odpovědí.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: b9ced5d4a81effcd73e0243d09bb83ed0fe7667c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253692"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Proměnné HTTP pro modul pravidel Azure CDN
Proměnné HTTP poskytují prostředky, pomocí kterých můžete načíst metadata požadavku HTTP a odpovědi. Tato metadata pak lze dynamicky změnit požadavek nebo odpověď. Použití proměnných HTTP je omezeno na následující funkce modulu pravidel:

- [Přepis klíče mezipaměti](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Změnit hlavičku požadavku klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Změnit hlavičku odpovědi klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Přesměrování adresy URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Přepsání adresy URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definice
Následující tabulka popisuje podporované proměnné HTTP. Prázdná hodnota je vrácena, když geo metadata (například PSČ) není k dispozici pro konkrétní požadavek.


| Name (Název) | Proměnná | Popis | Ukázková hodnota |
| ---- | -------- | ----------- | ------------ |
| ASN (Žadatel) | %{geo_asnum} | Označuje číslo AS uchazeče. <br /><br />**Zastaralé:** %{virt_dst_asnum}. <br />Tato proměnná byla zastaralá ve prospěch %{geo_asnum}. Přestože pravidlo, které používá tuto zastaralou proměnnou, bude nadále fungovat, měli byste ji aktualizovat, abyste ji použili novou proměnnou. | AS15133 |
| Město (Žadatel) | %{geo_city} | Označuje město uchazeče. | Los Angeles |
| Kontinent (Žadatel) | %{geo_continent} | Označuje kontinent uchazeče prostřednictvím jeho zkratky. <br />Platné hodnoty jsou: <br />AF: Afrika<br />AS: Asie<br />EU: Evropa<br />NA: Severní Amerika<br />OC: Oceánie<br />SA: Jižní Amerika<br /><br />**Zastaralé:** %{virt_dst_continent}. <br />Tato proměnná byla zastaralá ve prospěch %{geo_continent}. <br />Přestože pravidlo, které používá tuto zastaralou proměnnou, bude nadále fungovat, měli byste ji aktualizovat, abyste ji použili novou proměnnou.| – |
| Hodnota souboru cookie | %{cookie_Cookie} | Vrátí hodnotu odpovídající klíči cookie určenému termínem cookie. | Použití vzorku: <br />%{cookie__utma}<br /><br />Ukázková hodnota:<br />111662281.2.10.1222100123 |
| Země (žadatel) | %{geo_country} | Označuje zemi původu uchazeče prostřednictvím kódu země. <br />**Zastaralé:** %{virt_dst_country}. <br /><br />Tato proměnná byla zastaralá ve prospěch %{geo_country}. Přestože pravidlo, které používá tuto zastaralou proměnnou, bude nadále fungovat, měli byste ji aktualizovat, abyste ji použili novou proměnnou. | USA |
| Určená tržní oblast (žadatel) | %{geo_dma_code} |Označuje mediální trh uchazeče podle kódu oblasti. <br /><br />Toto pole se vztahuje pouze na požadavky pocházející ze Spojených států.| 745 |
| Metoda požadavku HTTP | %{request_method} | Označuje metodu požadavku HTTP. | GET |
| Stavový kód HTTP | %{stav} | Označuje stavový kód HTTP pro odpověď. | 200 |
| IP adresa (žadatel) | %{virt_dst_addr} | Označuje IP adresu uchazeče. | 192.168.1.1 |
| Zeměpisná šířka (žadatel) | %{geo_latitude} | Označuje zeměpisnou šířku uchazeče. | 34.0995 |
| Zeměpisná doba | %{geo_longitude} | Označuje zeměpisnou dobu uchazeče. | -118.4143 |
| Metropolitní statistická oblast (žadatel) | %{geo_metro_code} | Označuje metropolitní oblast uchazeče. <br /><br />Toto pole se vztahuje pouze na požadavky pocházející ze Spojených států.<br />| 745 |
| Port (Žadatel) | %{virt_dst_port} | Označuje dočasný port uchazeče. | 55885 |
| PSČ (Žadatel) | %{geo_postal_code} | Označuje PSČ uchazeče. | 90210 |
| Byl nalezen řetězec dotazu. | %{is_args} | Hodnota pro tuto proměnnou se liší v závislosti na tom, zda požadavek obsahuje řetězec dotazu.<br /><br />- Dotaz řetězec nalezeno: ?<br />- Žádný řetězec dotazu: NULL | ? |
| Byl nalezen parametr řetězce dotazu. | %{is_amp} | Hodnota pro tuto proměnnou se liší podle toho, zda požadavek obsahuje alespoň jeden parametr řetězce dotazu.<br /><br />- Nalezený parametr: &<br />- Žádné parametry: NULL | & |
| Hodnota parametru řetězce dotazu | %{arg_&lt;&gt;parametr } | Vrátí hodnotu odpovídající parametru řetězce &lt;dotazu identifikovanému termínem parametru.&gt; | Použití vzorku: <br />%{arg_language}<br /><br />Parametr řetězce ukázkového dotazu: <br />?language=en<br /><br />Hodnota vzorku: en |
| Hodnota řetězce dotazu | %{query_string} | Označuje celou hodnotu řetězce dotazu definovanou v adrese URL požadavku. |key1=val1&key2=val2&key3=val3 |
| Doména odkazové odkazy | %{referring_domain} | Označuje doménu definovanou v hlavičce požadavku odkazné odkazné. | <www.google.com> |
| Region (žadatel) | %{geo_region} | Označuje oblast uchazeče (například stát nebo provincii) prostřednictvím alfanumerické zkratky. | CA |
| Hodnota hlavičky požadavku | %{http_RequestHeader} | Vrátí hodnotu odpovídající hlavičce požadavku identifikované termínem RequestHeader. <br /><br />Pokud název hlavičky požadavku obsahuje pomlčku (například User-Agent), nahraďte ji podtržítkem (například User_Agent).| Použití vzorku: %{http_Connection}<br /><br />Ukázková hodnota: Keep-Alive | 
| Požádat o hostitele | %{hostitel} | Označuje hostitele definovaného v adrese URL požadavku. | <www.mydomain.com> |
| Protokol požadavku | %{request_protocol} | Označuje protokol požadavku. | HTTP/1.1 |
| Schéma žádostí | %{schéma} | Označuje schéma požadavků. |HTTP |
| Identifikátor URI požadavku (relativní) | %{request_uri} | Označuje relativní cestu, včetně řetězce dotazu, definovanou v identifikátoru URI požadavku požadavku. | /marketing/foo.js?loggedin=true |
| Identifikátor URI požadavku (relativní bez řetězce dotazu) | %{uri} | Označuje relativní cestu k požadovanému obsahu. <br /><br/>Klíčové informace:<br />- Tato relativní cesta vyloučí řetězec dotazu.<br />- Tato relativní cesta odráží přepisy adres URL. Adresa URL bude přepsána za následujících podmínek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Funkce přepisu adresy URL: Tato funkce přepíše relativní cestu definovanou v uri požadavku požadavku.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: Tento typ požadavku je přepsán na odpovídající adresu URL CDN. |/800001/corigin/rewrittendir/foo.js |
| Identifikátor URI žádosti | %{požadavek} | Popisuje požadavek. <br />Syntaxe: &lt;&gt; &lt;Protokol&gt; &lt;HTTP relativní cesta metody HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Hodnota záhlaví odpovědi | %{resp_&lt;Hlavička odpovědi&gt;} | Vrátí hodnotu odpovídající záhlaví odpovědi &lt;identifikované&gt; termínem ResponseHeader. <br /><br />Pokud název hlavičky odpovědi obsahuje pomlčku (například User-Agent), nahraďte ji podtržítkem (například User_Agent). | Použití vzorku: %{resp_Content_Length}<br /><br />Hodnota vzorku: 100 |

## <a name="usage"></a>Využití
Následující tabulka popisuje správnou syntaxi pro určení proměnné HTTP.


| Syntaxe | Příklad | Popis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{hostitel} | Pomocí této syntaxe získáte celou hodnotu odpovídající zadané &lt;proměnné HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{hostitel,} | Tato syntaxe slouží k nastavení případu pro &lt;celou hodnotu&gt;odpovídající zadanému httpvariabledelimiteru . |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.\.([^ :]+)/cdn.$3.$3:80} | Regulární výraz &lt;pro httpvariabledelimiterexpression&gt; slouží k nahrazení, odstranění nebo manipulaci s hodnotou proměnné HTTP. |

Názvy proměnných HTTP podporují pouze abecední znaky a podtržítka. Převeďte nepodporované znaky na podtržítka.

## <a name="delimiter-reference"></a>Odkaz na oddělovač
Oddělovač lze zadat za proměnnou HTTP k dosažení některého z následujících účinků:

- Transformace hodnoty přidružené k proměnné.

     Příklad: Převeďte celou hodnotu na malá písmena.

- Odstraňte hodnotu přidruženou k proměnné.

- Manipulujte s hodnotou přidruženou k proměnné.

     Příklad: Pomocí regulárních výrazů změňte hodnotu přidruženou k proměnné HTTP.

Oddělovače jsou popsány v následující tabulce.

| Oddělovač | Popis |
| --------- | ----------- |
| := | Označuje, že výchozí hodnota bude přiřazena proměnné, pokud je buď: <br />- Chybí <br />- Nastaveno na hodnotu NULL. |
| :+ | Označuje, že výchozí hodnota bude přiřazena proměnné, když jí byla přiřazena hodnota. |
| : | Označuje, že dílčí řetězec hodnoty přiřazené proměnné bude rozbalen. |
| # | Označuje, že vzorek zadaný po tomto oddělovači by měl být odstraněn, pokud je nalezen na začátku hodnoty přidružené k proměnné. |
| % | Označuje, že vzorek zadaný po tomto oddělovači by měl být odstraněn, pokud je nalezen na konci hodnoty přidružené k proměnné. <br />Tato definice je použitelná pouze v případě, že symbol % je použit jako oddělovač. |
| / | Vymezuje proměnnou HTTP nebo vzorek. |
| // | Najděte a nahraďte všechny výskyty zadaného vzoru. |
| /= | Najít, zkopírovat a přepsat všechny výskyty zadaného vzoru. |
| , | Převeďte hodnotu přidruženou k proměnné HTTP na malá písmena. |
| ^ | Převeďte hodnotu přidruženou k proměnné HTTP na velká písmena. |
| ,, | Převeďte všechny instance zadaného znaku v hodnotě přidružené k proměnné HTTP na malá písmena. |
| ^^ | Převeďte všechny instance zadaného znaku v hodnotě přidružené k proměnné HTTP na velká písmena. |

## <a name="exceptions"></a>Výjimky
Následující tabulka popisuje okolnosti, za kterých zadaný text není považován za proměnnou HTTP.

| Podmínka | Popis | Příklad |
| --------- | ----------- | --------|
| Unikající symbol % | Symbol procenta lze uvozevat pomocí zpětného lomítka. <br />Ukázková hodnota vpravo bude považována za hodnotu literálu a nikoli jako proměnná HTTP.| \%{hostitel} |
| Neznámé proměnné | Prázdný řetězec je vždy vrácena pro neznámé proměnné. | %{unknown_variable} |
| Neplatné znaky nebo syntaxe | Proměnné, které obsahují neplatné znaky nebo syntaxi, jsou považovány za literálové hodnoty. <br /><br />Příklad #1: Zadaná hodnota obsahuje neplatný znak (například -). <br /><br />Příklad #2: Zadaná hodnota obsahuje dvojitou sadu složených závorek. <br /><br />Příklad #3: Zadanou hodnotu chybí uzavírací složená závorka.<br /> | Příklad #1: %{resp_user-agent} <br /><br />Příklad #2: %{{host}} <br /><br />Příklad #3: %{host |
| Chybějící název proměnné | Hodnota NULL je vždy vrácena, pokud není zadána proměnná. | %{} |
| Koncové znaky | Znaky, které sledují proměnnou, jsou považovány za literálové hodnoty. <br />Ukázková hodnota vpravo obsahuje koncovou složenou složenou závorku, která bude považována za hodnotu literálu. | %{hostitel}} |

## <a name="setting-default-header-values"></a>Nastavení výchozích hodnot záhlaví
Výchozí hodnotu lze přiřadit k záhlaví, pokud splňuje některou z následujících podmínek:
- Chybí/odstaví
- Nastaveno na hodnotu NULL.

Následující tabulka popisuje, jak definovat výchozí hodnotu.

| Podmínka | Syntaxe | Příklad | Popis |
| --------- | ------ | --------| ----------- |
| Nastavte záhlaví na výchozí hodnotu, pokud splňuje některou z následujících podmínek: <br /><br />- Chybějící záhlaví <br /><br />- Hodnota záhlaví je nastavena na hodnotu NULL.| %{Proměnná:=Hodnota} | %{http_referrer:=neurčeno} | Referrer záhlaví bude nastavena pouze na *nespecifikované,* pokud je buď chybí nebo nastavena na hodnotu NULL. Pokud byla nastavena, nebude provedena žádná akce. |
| Pokud záhlaví chybí, nastavte záhlaví na výchozí hodnotu. | %{Proměnná=Hodnota} | %{http_referrer=neurčeno} | Referrer záhlaví bude nastavena pouze na *nespecifikované,* pokud chybí. Pokud byla nastavena, nebude provedena žádná akce. |
| Nastavte záhlaví na výchozí hodnotu, pokud nesplňuje některou z následujících podmínek: <br /><br />- Chybí<br /><br /> - Nastaveno na hodnotu NULL. | %{Proměnná:+Hodnota} | %{http_referrer:+neurčeno} | Referrer záhlaví bude nastavena pouze na *nespecifikované,* pokud hodnota byla přiřazena k němu. Žádná akce bude probíhat, pokud chybí nebo je nastavena na hodnotu NULL. |

## <a name="manipulating-variables"></a>Manipulace s proměnnými
S proměnnými lze manipulovat následujícími způsoby:

- Rozbalení podřetězců
- Odstranění vzorků

### <a name="substring-expansion"></a>Rozšíření podřetězce
Ve výchozím nastavení se proměnná rozšíří na plnou hodnotu. Následující syntaxí můžete rozbalit pouze podřetězec hodnoty proměnné.

`%<Variable>:<Offset>:<Length>}`

Klíčové informace:

- Hodnota přiřazená termínu Posun určuje počáteční znak podřetězce:

     - Kladný: Počáteční znak podřetězce se vypočítá z prvního znaku v řetězci.
     - Nula: Počáteční znak podřetězce je první znak v řetězci.
     - Záporné: Počáteční znak podřetězce se vypočítá z posledního znaku v řetězci.

- Délka podřetězce je určena termínem *Délka:*

     - Vynechání: Vynechání termínu Délka umožňuje podřetězci zahrnout všechny znaky mezi počátečním znakem a koncem řetězce.
     - Kladný: Určuje délku podřetězce od počátečního znaku doprava.
     - Negativní: Určuje délku podřetězce od počátečního znaku doleva.

#### <a name="example"></a>Příklad:

Následující příklad závisí na následující adrese URL ukázkového požadavku:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Následující řetězec ukazuje různé metody pro manipulaci s proměnnými:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Na základě ukázkové adresy URL požadavku bude výše uvedená manipulace s proměnnými vytvářet následující hodnotu:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Odstranění vzorku
Text, který odpovídá určitému vzoru, lze odebrat buď z začátku, nebo z konce hodnoty proměnné.

| Syntaxe | Akce |
| ------ | ------ |
| %{Proměnná#Vzorek} | Odeberte text, pokud je zadaný vzorek nalezen na začátku hodnoty proměnné. |
| %{Proměnná%Vzorek} | Odeberte text, pokud je zadaný vzorek nalezen na konci hodnoty proměnné. |

#### <a name="example"></a>Příklad:

V tomto ukázkovém scénáři je proměnná *request_uri* nastavena na:

`/800001/myorigin/marketing/product.html?language=en-US`

Následující tabulka ukazuje, jak tato syntaxe funguje.

| Ukázková syntaxe | Výsledky | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/Původ zákazníka | /původ zákazníka/myorigin/marketing/product.html?language=en-US | Protože proměnná začíná vzorem, byla nahrazena. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Vzhledem k tomu, že proměnná nekončí vzorem, nedošlo k žádné změně.|

### <a name="find-and-replace"></a>Vyhledání a nahrazení
Syntaxe hledání a nahrazení je popsána v následující tabulce.

| Syntaxe | Akce |
| ------ | ------ |
| %{Proměnná/Najít/Nahradit} | Najděte a nahraďte první výskyt zadaného vzoru. |
| %{Proměnná/Hledání/nahrazení} | Najděte a nahraďte všechny výskyty zadaného vzoru. |
| %{Proměnná^} |Převeďte celou hodnotu na velká písmena. |
| %{Variable^Find} | Převeďte první výskyt zadaného vzorku na velká písmena. |
| %{Proměnná,} | Převeďte celou hodnotu na malá písmena. |
| %{Proměnná,Najít} | Převeďte první výskyt zadaného vzorku na malá písmena. |

### <a name="find-and-rewrite"></a>Najít a přepsat
Pro změnu hledání a nahrazování použijte text, který odpovídá zadanému vzoru při přepisování. Syntaxe hledání a přepisu je popsána v následující tabulce.

| Syntaxe | Akce |
| ------ | ------ |
| %{Variable/=Najít/Přepsat} | Najít, zkopírovat a přepsat všechny výskyty zadaného vzoru. |
| %{Variable/^Find/Rewrite} | Vyhledejte, zkopírujte a přepište zadaný vzor, pokud k němu dojde na začátku proměnné. |
| %{Proměnná/$Find/Přepis} | Vyhledejte, zkopírujte a přepište zadaný vzor, pokud k němu dojde na konci proměnné. |
| %{Proměnná/Najít} | Najděte a odstraňte všechny výskyty zadaného vzorku. |

Klíčové informace:

- Rozbalte text, který odpovídá zadanému vzoru, zadáním znaku dolaru následovaného celým celým e-temalem (například $1).

- Lze zadat více vzorků. Pořadí, ve kterém je vzorek zadán, určuje celé číslo, které k němu bude přiřazeno. V následujícím příkladu první vzorek odpovídá "www.", druhý vzor odpovídá doméně druhé úrovně a třetí vzorek odpovídá doméně nejvyšší úrovně:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Přepsaná hodnota se může skládat z libovolné kombinace textu a těchto zástupných symbolů.

    V předchozím příkladu je název hostitele `cdn.$2.$3:80` přepsán na (například cdn.mydomain.com:80).

- Případ zástupného symbolu vzoru (například $1) lze upravit pomocí následujících příznaků:
     - U: Velká malá a malá expandovaná hodnota.

         Ukázková syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Malá písmena rozšířené hodnoty.

         Ukázková syntaxe:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operátor musí být zadán před vzorem. Zadaný operátor určuje chování zachytávání vzoru:

     - `=`: Označuje, že všechny výskyty zadaného vzorku musí být zachyceny a přepsány.
     - `^`: Označuje, že bude zachycen pouze text, který začíná zadaným vzorkem.
     - `$`: Označuje, že bude zachycen pouze text, který končí zadaným vzorkem.
 
- Pokud vyneche hodnotu */Rewrite,* text, který odpovídá vzoru, bude odstraněn.


