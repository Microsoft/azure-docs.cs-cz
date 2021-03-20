---
title: Proměnné HTTP pro modul pravidel Azure CDN | Microsoft Docs
description: Přečtěte si o proměnných HTTP, které vám umožní získat metadata požadavků a odpovědí HTTP pro některé funkce stroje pravidel. Použijte metadata k modifikaci žádosti nebo odpovědi.
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
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192683"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Proměnné HTTP pro modul pravidel Azure CDN
Proměnné HTTP poskytují prostředky, pomocí kterých můžete načíst metadata žádosti a odpovědi HTTP. Tato metadata pak můžete použít k dynamické změně žádosti nebo odpovědi. Použití proměnných HTTP je omezeno na následující funkce stroje pravidel:

- [Přepsání klíče do mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Upravit hlavičku žádosti klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Upravit hlavičku odpovědi klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [Přesměrování adresy URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [Přepsání adresy URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Definice
Následující tabulka popisuje podporované proměnné protokolu HTTP. Pokud GEOGRAFICKá metadata (například PSČ) nejsou pro konkrétní požadavek k dispozici, vrátí se prázdná hodnota.


| Name | Proměnná | Popis | Ukázková hodnota |
| ---- | -------- | ----------- | ------------ |
| ASN (žadatel) | % {geo_asnum} | Označuje žadatele jako číslo. <br /><br />**Zastaralé:** % {virt_dst_asnum}. <br />Tato proměnná se už nepoužívá namísto% {geo_asnum}. I když pravidlo, které používá tuto zastaralou proměnnou, bude fungovat i nadále, měli byste je aktualizovat, aby používala novou proměnnou. | AS15133 |
| Město (žadatel) | % {geo_city} | Označuje město žadatele. | Los Angeles |
| Kontinent (žadatel) | % {geo_continent} | Označuje kontinenta žadatele prostřednictvím zkratky. <br />Platné hodnoty jsou: <br />AF: Afrika<br />JAKO: Asie<br />EU: Evropa<br />NA: Severní Amerika<br />°C: Oceánie a<br />SA: Jižní Amerika<br /><br />**Zastaralé:** % {virt_dst_continent}. <br />Tato proměnná se už nepoužívá namísto% {geo_continent}. <br />I když pravidlo, které používá tuto zastaralou proměnnou, bude fungovat i nadále, měli byste je aktualizovat, aby používala novou proměnnou.| – |
| Hodnota souboru cookie | % {cookie_Cookie} | Vrátí hodnotu odpovídající klíči souboru cookie identifikovanému termínem souboru cookie. | Ukázka použití: <br />% {cookie__utma}<br /><br />Ukázková hodnota:<br />111662281.2.10.1222100123 |
| Země/oblast (žadatel) | % {geo_country} | Určuje zemi nebo oblast původu žadatele prostřednictvím kódu země nebo oblasti. <br />**Zastaralé:** % {virt_dst_country}. <br /><br />Tato proměnná se už nepoužívá namísto% {geo_country}. I když pravidlo, které používá tuto zastaralou proměnnou, bude fungovat i nadále, měli byste je aktualizovat, aby používala novou proměnnou. | USA |
| Vymezená oblast trhu (žadatel) | % {geo_dma_code} |Označuje mediální trh žadatele podle kódu oblasti. <br /><br />Toto pole se vztahuje pouze na žádosti, které pocházejí z USA.| 745 |
| Metoda požadavku HTTP | % {request_method} | Označuje metodu požadavku HTTP. | GET |
| Stavový kód HTTP | % {status} | Určuje stavový kód protokolu HTTP pro odpověď. | 200 |
| IP adresa (žadatel) | % {virt_dst_addr} | Určuje IP adresu žadatele. | 192.168.1.1 |
| Zeměpisná šířka (žadatel) | % {geo_latitude} | Indikuje zeměpisnou šířku žadatele. | 34,0995 |
| Zeměpisná délka (žadatel) | % {geo_longitude} | Určuje zeměpisnou délku žadatele. | -118,4143 |
| Metropolitní statistická oblast (žadatel) | % {geo_metro_code} | Označuje metropolitní oblast žadatele. <br /><br />Toto pole se vztahuje pouze na žádosti, které pocházejí z USA.<br />| 745 |
| Port (žadatel) | % {virt_dst_port} | Označuje dočasný port žadatele. | 55885 |
| Poštovní směrovací číslo (žadatel) | % {geo_postal_code} | Označuje poštovní směrovací číslo žadatele. | 90210 |
| Našel se řetězec dotazu. | % {is_args} | Hodnota této proměnné se liší v závislosti na tom, zda požadavek obsahuje řetězec dotazu.<br /><br />-Nalezen řetězec dotazu:?<br />-Žádný řetězec dotazu: NULL | ? |
| Našel se parametr řetězce dotazu. | % {is_amp} | Hodnota této proměnné se liší v závislosti na tom, zda požadavek obsahuje alespoň jeden parametr řetězce dotazu.<br /><br />-Nalezen parametr: &<br />-Žádné parametry: NULL | & |
| Hodnota parametru řetězce dotazu | % {arg_ &lt; parametr &gt; } | Vrátí hodnotu odpovídající parametru řetězce dotazu identifikovaného &lt; &gt; termínem parametru. | Ukázka použití: <br />% {arg_language}<br /><br />Vzorový parametr řetězce dotazu: <br />? Language = EN<br /><br />Ukázková hodnota: EN |
| Hodnota řetězce dotazu | % {query_string} | Označuje celou hodnotu řetězce dotazu definovanou v adrese URL požadavku. |klíč1 = Val1&key2 = Val2&key3 = Val3 |
| Doména odkazujícího serveru | % {referring_domain} | Určuje doménu definovanou v záhlaví požadavku odkazujícího serveru. | <www.google.com> |
| Oblast (žadatele) | % {geo_region} | Označuje oblast žadatele (například stát nebo provincie) prostřednictvím alfanumerické zkratky. | CA |
| Hodnota hlavičky žádosti | % {http_RequestHeader} | Vrátí hodnotu odpovídající hlavičce požadavku identifikovaného termínem RequestHeader. <br /><br />Pokud název hlavičky požadavku obsahuje spojovník (například User-Agent), nahraďte ho podtržítkem (například User_Agent).| Ukázkové použití:% {http_Connection}<br /><br />Ukázková hodnota: Keep-Alive | 
| Hostitel žádosti | % {Host} | Určuje hostitele definovaného v adrese URL požadavku. | <www.mydomain.com> |
| Protokol žádosti | % {request_protocol} | Označuje protokol žádosti. | HTTP/1.1 |
| Schéma žádosti | % {schéma} | Určuje schéma požadavků. |http |
| Identifikátor URI žádosti (relativní) | % {request_uri} | Určuje relativní cestu, včetně řetězce dotazu definovaného v identifikátoru URI požadavku. | /Marketing/foo.js? LoggedIn = true |
| Identifikátor URI žádosti (relativní bez řetězce dotazu) | % {URI} | Určuje relativní cestu k požadovanému obsahu. <br /><br/>Informace o klíči:<br />– Tato relativní cesta vyloučí řetězec dotazu.<br />– Tato relativní cesta odráží přepsání adresy URL. Adresa URL bude přepsána za následující podmínky:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Funkce přepisu adresy URL: Tato funkce přepíše relativní cestu definovanou v identifikátoru URI požadavku.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;– Adresa URL CNAME Edge: Tento typ požadavku se přepíše na odpovídající adresu URL CDN. |/800001/corigin/rewrittendir/foo.js |
| Identifikátor URI žádosti | % {Request} | Popisuje požadavek. <br />Syntaxe: &lt; protokol HTTP &gt; &lt; relativní cesty &gt; k metodě &lt; http&gt; | ZÍSKAT/marketing/foo.js? LoggedIn = true HTTP/1.1 |
| Hodnota hlavičky odpovědi | % {resp_ &lt; ResponseHeader &gt; } | Vrátí hodnotu odpovídající hlavičce Response identifikovaného &lt; &gt; termínem ResponseHeader. <br /><br />Pokud název hlavičky odpovědi obsahuje pomlčku (například User-Agent), nahraďte ji podtržítkem (například User_Agent). | Ukázkové použití:% {resp_Content_Length}<br /><br />Ukázková hodnota: 100 |

## <a name="usage"></a>Využití
V následující tabulce jsou popsány správné syntaxe pro určení proměnné HTTP.


| Syntax | Příklad | Description |
| ------ | -------- | ---------- |
| % { &lt; HTTPVariable &gt; } | % {Host} | Tuto syntaxi použijte k získání celé hodnoty odpovídající zadanému &lt; HTTPVariable &gt; . |
| % { &lt; HTTPVariableDelimiter &gt; } | % {Host,} | Tuto syntaxi použijte k nastavení velikosti písmen pro celou hodnotu odpovídající zadanému  &lt; HTTPVariableDelimiter &gt; . |
| % { &lt; HTTPVariableDelimiterExpression &gt; } | % {Host/= ^ www \. ([^ \. ] +) \. ([^ \. :] +)/CDN. $2. $3:80} | Použijte regulární výraz pro &lt; HTTPVariableDelimiterExpression &gt; k nahrazení, odstranění nebo manipulaci s hodnotou proměnné http. |

Názvy proměnných HTTP podporují jenom abecední znaky a podtržítka. Převede nepodporované znaky na podtržítka.

## <a name="delimiter-reference"></a>Reference oddělovače
Oddělovač lze zadat za proměnnou HTTP, aby bylo možné provést některý z následujících účinků:

- Transformuje hodnotu přidruženou k proměnné.

     Příklad: převede celou hodnotu na malá.

- Odstraňte hodnotu přidruženou k proměnné.

- Manipulace s hodnotou přidruženou k proměnné.

     Příklad: použijte regulární výrazy pro změnu hodnoty přidružené k proměnné HTTP.

Oddělovače jsou popsány v následující tabulce.

| Oddělovač | Description |
| --------- | ----------- |
| := | Označuje, že výchozí hodnota bude přiřazena proměnné, pokud je buď: <br />– Chybějící <br />-Nastavit na hodnotu NULL. |
| :+ | Označuje, že se k proměnné přiřadí výchozí hodnota, když je jí přiřazena hodnota. |
| : | Indikuje, že podřetězec hodnoty přiřazené proměnné se rozšíří. |
| # | Označuje, že vzorek zadaný za tímto oddělovačem by měl být odstraněn, když je nalezen na začátku hodnoty přidružené k proměnné. |
| % | Označuje, že vzorek zadaný za tímto oddělovačem by měl být odstraněn, když je nalezen na konci hodnoty přidružené k proměnné. <br />Tato definice je platná pouze v případě, že je symbol% použit jako oddělovač. |
| / | Odděluje proměnnou HTTP nebo vzor. |
| // | Najde a nahradí všechny výskyty zadaného vzoru. |
| /= | Vyhledejte, zkopírujte a přepište všechny výskyty zadaného vzoru. |
| , | Převeďte hodnotu přidruženou k proměnné HTTP na malá písmena. |
| ^ | Převeďte hodnotu přidruženou k proměnné HTTP na velká písmena. |
| ,, | Převede všechny výskyty zadaného znaku v hodnotě přidružené k proměnné HTTP na malá písmena. |
| ^^ | Převede všechny výskyty zadaného znaku v hodnotě přidružené k proměnné HTTP na velká písmena. |

## <a name="exceptions"></a>Výjimky
Následující tabulka popisuje okolnosti, za kterých se zadaný text nepovažuje za proměnnou HTTP.

| Podmínka | Popis | Příklad |
| --------- | ----------- | --------|
| Symbol uvozovacího znaku% | Symbol procenta může být uvozen pomocí zpětného lomítka. <br />Vzorová hodnota vpravo bude považována za hodnotu literálu a ne jako proměnnou HTTP.| \%provoz |
| Neznámé proměnné | U neznámých proměnných se vždycky vrátí prázdný řetězec. | % {unknown_variable} |
| Neplatné znaky nebo syntaxe | Proměnné, které obsahují neplatné znaky nebo syntaxe, jsou považovány za hodnoty literálu. <br /><br />Příklad #1: zadaná hodnota obsahuje neplatný znak (například-). <br /><br />Příklad #2: zadaná hodnota obsahuje dvojitou sadu složených závorek. <br /><br />Příklad #3: v zadané hodnotě chybí uzavírací složená závorka.<br /> | Příklad #1:% {resp_user-Agent} <br /><br />Příklad #2:% {{Host}} <br /><br />Příklad #3:% {Host |
| Chybí název proměnné. | Pokud není zadána proměnná, je vždy vrácena hodnota NULL. | %{} |
| Koncové znaky | Znaky, které označuje proměnnou, jsou považovány za hodnoty literálu. <br />Ukázková hodnota vpravo obsahuje koncovou složenou závorku, která bude považována za hodnotu literálu. | % {Host}} |

## <a name="setting-default-header-values"></a>Nastavení výchozích hodnot hlaviček
Výchozí hodnota může být přiřazena k hlavičce, pokud splňuje některou z následujících podmínek:
- Chybějící nebo nenastavené
- Nastavte na hodnotu NULL.

Následující tabulka popisuje, jak definovat výchozí hodnotu.

| Podmínka | Syntax | Příklad | Description |
| --------- | ------ | --------| ----------- |
| Nastavte hlavičku na výchozí hodnotu, pokud splňuje některou z následujících podmínek: <br /><br />– Chybějící hlavička <br /><br />-Hodnota hlavičky je nastavena na hodnotu NULL.| % {Proměnná: = hodnota} | % {http_referrer: = Neurčeno} | Záhlaví odkazujícího serveru bude nastaveno na *Neurčeno* , pokud buď chybí, nebo nastaveno na hodnotu null. Žádná akce se neprovede, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud chybí. | % {Variable = hodnota} | % {http_referrer = Neurčeno} | Záhlaví odkazujícího serveru bude nastaveno na *Neurčeno* , pokud chybí. Žádná akce se neprovede, pokud byla nastavena. |
| Nastavte hlavičku na výchozí hodnotu, pokud nesplňuje žádnou z následujících podmínek: <br /><br />– Chybějící<br /><br /> -Nastavit na hodnotu NULL. | % {Proměnná: + hodnota} | % {http_referrer: + Neurčeno} | Záhlaví odkazujícího serveru bude nastaveno na *Neurčeno* , je-li přiřazena hodnota. Žádná akce se neprovede, pokud buď chybí, nebo je nastavená na hodnotu NULL. |

## <a name="manipulating-variables"></a>Manipulace s proměnnými
Proměnné lze manipulovat následujícími způsoby:

- Rozbalování podřetězců
- Odebírají se vzory.

### <a name="substring-expansion"></a>Rozbalení podřetězců
Ve výchozím nastavení se proměnná rozšíří na celou hodnotu. Pomocí následující syntaxe rozbalíte pouze podřetězec hodnoty proměnné.

`%<Variable>:<Offset>:<Length>}`

Informace o klíči:

- Hodnota přiřazená k posunu určuje počáteční znak podřetězce:

     - Kladné: počáteční znak podřetězce je počítán od prvního znaku v řetězci.
     - Nula: počáteční znak podřetězce je prvním znakem v řetězci.
     - Záporné: počáteční znak podřetězce je vypočítán z posledního znaku v řetězci.

- Délka podřetězce je určena *délkou* :

     - Vynecháno: vynechání období délky umožňuje, aby dílčí řetězec zahrnoval všechny znaky mezi počátečním znakem a koncem řetězce.
     - Kladné: Určuje délku podřetězce od počátečního znaku vpravo.
     - Záporné: Určuje délku podřetězce od počátečního znaku vlevo.

#### <a name="example"></a>Příklad:

Následující příklad spoléhá na následující ukázkovou adresu URL požadavku:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Následující řetězec ukazuje různé metody pro manipulaci s proměnnými:

https: \/ /www%{HTTP_HOST: 3}/mobile/%{REQUEST_URI: 7:10}/% {REQUEST_URI:-5:-8}. htm

Na základě ukázkové adresy URL požadavku vyprodukuje výše uvedená manipulace proměnnou následující hodnotu:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Odebrání vzorku
Text, který odpovídá určitému vzoru, lze odebrat buď od začátku, nebo od konce hodnoty proměnné.

| Syntax | Akce |
| ------ | ------ |
| % {Variable # vzor} | Odebere text, pokud je zadaný vzor nalezen na začátku hodnoty proměnné. |
| % {Proměnná% vzor} | Odebere text, pokud je zadaný vzor nalezen na konci hodnoty proměnné. |

#### <a name="example"></a>Příklad:

V tomto ukázkovém scénáři je proměnná *REQUEST_URI* nastavena na:

`/800001/myorigin/marketing/product.html?language=en-US`

Následující tabulka ukazuje, jak tato syntaxe funguje.

| Ukázková syntaxe | Výsledky | Description |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html? Language = en-US | Vzhledem k tomu, že proměnná začíná vzorem, byla nahrazena. |
| % {request_uri% HTML} htm | /800001/myorigin/marketing/product.html? Language = en-US | Vzhledem k tomu, že proměnná nekončí vzorem, došlo k žádné změně.|

### <a name="find-and-replace"></a>Vyhledání a nahrazení
Syntaxe Find a Replace je popsána v následující tabulce.

| Syntax | Akce |
| ------ | ------ |
| % {Proměnná/najít/nahradit} | Najde a nahradí první výskyt zadaného vzoru. |
| % {Variable//Find/Replace} | Najde a nahradí všechny výskyty zadaného vzoru. |
| % {Variable ^} |Převede celou hodnotu na velká. |
| % {Variable ^ – hledání} | Převede první výskyt zadaného vzoru na velká písmena. |
| % {Variable,} | Převede celou hodnotu na malá. |
| % {Proměnná, Find} | Převede první výskyt zadaného vzoru na malá písmena. |

### <a name="find-and-rewrite"></a>Najít a přepsat
Pro variaci hledání a nahrazování použijte při přepisu text, který odpovídá zadanému vzoru. Syntaxe Find a Rewrite je popsána v následující tabulce.

| Syntax | Akce |
| ------ | ------ |
| % {Variable/= najít/přepsat} | Vyhledejte, zkopírujte a přepište všechny výskyty zadaného vzoru. |
| % {Variable/^ vyhledat a přepsat} | Najděte, zkopírujte a přepište zadaný vzor, když se vyskytne na začátku proměnné. |
| % {Variable/$Find/Rewrite} | Najděte, zkopírujte a přepište zadaný vzor, když se objeví na konci proměnné. |
| % {Variable/najít} | Najde a odstraní všechny výskyty zadaného vzoru. |

Informace o klíči:

- Rozbalí text, který odpovídá zadanému vzoru, zadáním znaku dolaru následovaného celým celým číslem (například $1).

- Lze zadat více vzorů. Pořadí, ve kterém je vzorek zadán, Určuje celé číslo, které bude přiřazeno. V následujícím příkladu první vzor porovnává "www.," druhý vzor odpovídá doméně druhé úrovně a třetí vzor odpovídá doméně nejvyšší úrovně:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Přepsaná hodnota se může skládat z jakékoli kombinace textu a zástupných symbolů.

    V předchozím příkladu se název hostitele přepsal do `cdn.$2.$3:80` (například CDN.mydomain.com:80).

- Případ zástupného symbolu vzoru (například $1) lze upravit pomocí následujících příznaků:
     - U: velká písmena rozšířené hodnoty.

         Ukázka syntaxe

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: malá písmena rozšířené hodnoty.

         Ukázka syntaxe

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Před vzorem je nutné zadat operátor. Zadaný operátor určuje chování zachycení vzoru:

     - `=`: Označuje, že se musí zachytit a přepsat všechny výskyty zadaného vzoru.
     - `^`: Označuje, že bude zachycen pouze text, který začíná zadaným vzorem.
     - `$`: Určuje, zda bude zachytávání pouze text, který končí zadaným vzorem.
 
- Pokud hodnotu */Rewrite* vynecháte, text, který odpovídá vzoru, se odstraní.


