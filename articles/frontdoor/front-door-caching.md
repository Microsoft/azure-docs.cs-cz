---
title: Služba Azure branou – ukládání do mezipaměti | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit, jak služba Azure branou monitoruje stav vaší back-EndY
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 97c02726c7e359195c6bf4ea793404562f2acccf
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001940"
---
# <a name="caching-with-azure-front-door-service"></a>Ukládání do mezipaměti službou Azure přední dveře
Následující dokument pro branou určuje chování pomocí pravidel směrování, které mají povolené ukládání do mezipaměti.

## <a name="delivery-of-large-files"></a>Doručování velkých souborů
Služba Azure branou přináší velké soubory bez limitu velikosti souboru. Přední dveře pomocí techniky označované jako objekt bloků. Pokud se požaduje s velkými soubory, načte branou menších souboru z back-endu. Po přijetí požadavku na soubor full nebo rozsah bajtů, prostředí s branou požadavků souboru z back-endu v blocích 8 MB.

</br>Po bloku dat dorazí prostředí branou, je uložit do mezipaměti a okamžitě obsluhovat uživateli. Přední dveře pak předem načte další blok paralelně. Tento před načtením zajistí, že obsah zůstane jeden blok před časem uživatele, což snižuje latence. Tento proces pokračuje, dokud se celý soubor se stáhne (je-li požadovány), všechny rozsahů bajtů jsou k dispozici (je-li požadovány), nebo klient ukončí připojení.

</br>Další informace o žádosti o rozsah bajtů, najdete v článku [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
Přední dveře ukládá do mezipaměti všechny bloky dat po přijetí, a proto není nutné uložit do mezipaměti, základem je mezipaměť branou celý soubor. Odeslání dalších žádostí o souboru nebo bajt rozsahy se obsluhují z mezipaměti. Není-li všechny bloky dat jsou uložené v mezipaměti, předběžného načítání slouží k vyžádání bloků dat z back-endu. Tato optimalizace se spoléhá na schopnost back-endu podporovat požadavky na zjištění rozsahu bajtů; Pokud back-endu nepodporuje požadavky na zjištění rozsahu bajtů, optimalizací není platná.

## <a name="file-compression"></a>Komprese souborů
Vstupní brána lze dynamicky Komprimovat obsah na hraničních zařízeních, výsledkem jsou menší a rychlejší reakci na vaši klienti. Všechny soubory jsou způsobilé pro kompresi. Soubor ale musí být typu MIME to nárok komprese seznamu. Přední dveře v současné době neumožňuje tento seznam se změnit. Aktuální seznam je:</br>
- "application/eot"
- "application/písmo"
- "application/písma – sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/písem ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml + xml"
- "application/xml"
- "application/xml rss plus (+)
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-písem ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-písem ttf"
- "application/x-javascript"
- "písmo/eot"
- "písem a písem ttf"
- "písmo/otf"
- "písmo/opentype"
- "image/svg + xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/kartu hodnot oddělených čárkami"
- "text/xml"
- "text/x-script"
- "text/x součást"
- "text/x-java-source"

Kromě toho soubor musí také být mezi 1 KB a 8 MB velikosti, včetně.

Tyto profily podporují následující kódování komprese:
- [GZIP (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [brotli](https://en.wikipedia.org/wiki/Brotli)

Pokud žádost o podporuje gzip a provádí kompresi, Brotli, komprese Brotli přednost.</br>
Když žádost pro určitý prostředek určuje komprese a výsledky požadavku v neúspěšnému přístupu do mezipaměti, branou provádí kompresi majetku přímo na serveru POP. Následně komprimovaný soubor se načítají z mezipaměti. Vrátí se výsledná položka transfer-encoding: rozdělený do bloků dat.

## <a name="query-string-behavior"></a>Chování řetězců dotazu
S branou můžete řídit, jak jsou soubory uložené v mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Řetězec dotazu v webový požadavek s řetězcem dotazu, je část žádosti, které je pozdější otazník (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč hodnota, ve kterých název pole a jeho hodnota jsou odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělených ampersandem (&). Například, http://www.contoso.com/content.mov?field1=value1&field2=value2. Pokud existuje více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí není důležitá.
- **Ignorovat řetězce dotazů**: výchozí režim. V tomto režimu branou předává řetězce dotazu z žadatel back-endu na první požadavek a uloží do mezipaměti assetu. Všechny následné požadavky assetu, které se obsluhují z prostředí branou ignorovat řetězce dotazu do vypršení platnosti mezipaměti asset.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: V tomto režimu se každý požadavek s jedinečnou adresu URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipaměti. Například odpověď z back-endu pro žádost o `www.example.ashx?q=test1` se ukládají do mezipaměti na branou prostředí a vrátil pro následné mezipaměti s stejný řetězec dotazu. Žádost o `www.example.ashx?q=test2` se uloží do mezipaměti jako samostatný prostředek s vlastní nastavení time to live.

## <a name="cache-purge"></a>Vymazání mezipaměti
Přední dveře ukládá prostředky do vypršení platnosti prostředku time to live (TTL). Po vypršení platnosti TTL prostředku, když klient požádá o prostředek, branou prostředí budou načítat nové aktualizovanou kopii prostředku obsluhovat žádosti klienta a úložiště aktualizovat mezipaměť.
</br>Osvědčeným postupem zajistit, aby vaši uživatelé vždy získat nejnovější verzi vaše prostředky se vaše prostředky pro každou aktualizaci na verzi a publikujte je jako nové adresy URL. Přední dveře okamžitě budou načítat nové prostředky pro další požadavky klienta. Někdy můžete chtít Vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a vynuťte jejich všechny k načtení nové materiály aktualizované. To může být způsobeno aktualizace do vaší webové aplikace, a rychle aktualizace prostředky, které obsahují nesprávné informace.

</br>Vyberte prostředky, které chcete vyprázdnit z hraničních uzlů. Pokud chcete vymazat všechny prostředky, klikněte na tlačítko k vyprázdnění všech zaškrtávací políčko. V opačném případě zadejte cestu k každý prostředek, který chcete vyprázdnit v textovém poli cesta. Následující formáty jsou podporovány v cestě.
1. **Vyprázdnění jedné adresy URL**: vymazání jednotlivý prostředek tak, že zadáte úplnou adresu URL s příponou souboru, například /pictures/strasbourg.png;
2. **Zástupné vyprázdnění**: hvězdičky (\*) může sloužit jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v koncovém bodě s /\* v cestě nebo vymazat všechny podsložky a soubory v rámci konkrétní složky tak, že zadáte složce následovaný /\*, třeba /obrazky/\*.
3. **Kořenové domény vyprázdnění**: vyprázdnit kořenový koncový bod s "/" v cestě.

Vymazat mezipaměť na branou jsou malá a velká písmena. Kromě toho jsou nezávislá na řetězec dotazu, což znamená, že vyprazdňování adresy URL se vymažou všechny varianty je řetězec dotazu. 

## <a name="cache-expiration"></a>Doba vypršení platnosti mezipaměti
Následující pořadí záhlaví se používá k určení, jak dlouho bude položka uložená v našich mezipaměti:</br>
1. Cache-Control: parametr s-maxage =<seconds>
2. Cache-Control: Parametr maxage =<seconds>
3. Vypršení platnosti: < http datum >

Hlavičky Cache-Control odpovědi, které označují, že odpověď nebudou zapisována do mezipaměti jako je například Cache-Control: privátní, Cache-Control: no-cache a Cache-Control: no-store jsou zachované. Pokud na serveru POP pro stejnou adresu URL jsou vydávaných za pochodu více požadavků, jsou však může sdílet odpovědi.


## <a name="request-headers"></a>Hlavičky požadavku

Následující hlavičky požadavku do back-end se nepředají při použití ukládání do mezipaměti.
- Autorizace
- Content-Length
- Transfer-Encoding

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).