---
title: Přední dveře Azure – ukládání do mezipaměti | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door monitoruje stav back-endů
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
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471740"
---
# <a name="caching-with-azure-front-door"></a>Ukládání do mezipaměti pomocí předních dveří Azure
Následující dokument určuje chování pro přední dveře s pravidly směrování, které umožnily ukládání do mezipaměti. Přední dveře je moderní obsah doručovací síť (CDN), a tak spolu s dynamickou akceleraci webu a vyrovnávání zatížení, ale také podporuje ukládání do mezipaměti chování, stejně jako jakýkoli jiný CDN.

## <a name="delivery-of-large-files"></a>Doručování velkých souborů
Azure Front Door poskytuje velké soubory bez omezení velikosti souboru. Přední dveře používá techniku zvanou objekt chunking. Když služba Front Door přijme požadavek na velký soubor, načte z back-endu menší části daného souboru. Po přijetí požadavku na úplný soubor nebo rozsah bajtů souboru si prostředí služby Front Door vyžádá soubor z back-endu v blocích dat o velikosti 8 MB.

</br>Po bloku dorazí do prostředí Front Door, je uložen do mezipaměti a okamžitě doručena uživateli. Přední dveře pak pre-načte další kus paralelně. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud není stažen celý soubor (pokud je požadováno), všechny rozsahy bajtů jsou k dispozici (na vyžádání) nebo klient ukončí připojení.

</br>Další informace o požadavku na rozsah bajtů naleznete v [rfc 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Přední dveře ukládá všechny bloky, jakmile jsou přijaty, a proto celý soubor nemusí být uložen do mezipaměti front door. Následné požadavky na soubor nebo rozsahy bajtů jsou obsluhovány z mezipaměti. Pokud nejsou všechny bloky jsou uloženy v mezipaměti, předběžné načítání se používá k vyžádání bloků z back-endu. Tato optimalizace závisí na schopnosti back-endu podporovat požadavky rozsahu bajtů; Pokud back-end nepodporuje požadavky na rozsah bajtů, tato optimalizace není efektivní.

## <a name="file-compression"></a>Komprese souborů
Přední dveře mohou dynamicky komprimovat obsah na okraji, což má za následek menší a rychlejší reakci na vaše klienty. Všechny soubory jsou způsobilé pro kompresi. Soubor však musí být typu MIME, který je způsobilý pro seznam komprese. V současné době front door neumožňuje tento seznam změnit. Aktuální seznam je:</br>
- "aplikace/eot"
- "aplikace/písmo"
- "aplikace/písmo-sfnt"
- "aplikace/javascript"
- application/json
- "aplikace/opentype"
- "aplikace/otf"
- "aplikace/pkcs7-mime"
- "aplikace/truetype"
- "aplikace/ttf",
- "application/vnd.ms-fontobject"
- "aplikace/xhtml+xml"
- "aplikace/xml"
- "aplikace/xml+rss"
- "aplikace/x-font-opentype"
- "aplikace/x-font-truetype"
- "aplikace/x-font-ttf"
- "aplikace/x-httpd-cgi"
- "aplikace/x-mpegurl"
- "aplikace/x-opentype"
- "aplikace/x-otf"
- "aplikace/x-perl"
- "aplikace/x-ttf"
- "aplikace/x-javascript"
- "písmo/eot"
- "písmo/ttf"
- "písmo/otf"
- "písmo/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/prostý"
- "text/bohatý text"
- "hodnoty oddělené textem/tabulátory"
- "text/xml"
- "text/x-script"
- "text/součást x"
- "text/x-java-source"

Kromě toho soubor musí být také mezi 1 KB a 8 MB ve velikosti, včetně.

Tyto profily podporují následující kódování komprese:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Pokud požadavek podporuje kompresi gzip a Brotli, má přednost komprese Brotli.</br>
Když požadavek na prostředek určuje kompresi a výsledkem požadavku dojde k chybě mezipaměti, front door provede kompresi datového zdroje přímo na serveru POP. Poté je komprimovaný soubor obsluhován z mezipaměti. Výsledná položka je vrácena s kódováním přenosu: blokování.

## <a name="query-string-behavior"></a>Chování řetězce dotazu
Pomocí aplikace Front Door můžete řídit způsob ukládání souborů do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Ve webovém požadavku s řetězcem dotazu je řetězec dotazu ta část požadavku, ke které dochází po otazníku (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč-hodnota, ve kterém je název pole a jeho hodnota odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělen ampersand (&). Například, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Pokud je více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí nezáleží.
- **Ignorovat řetězce dotazu**: Výchozí režim. V tomto režimu Front Door předá řetězce dotazu z osazení do back-endu na první požadavek a uloží do mezipaměti prostředek. Všechny následné požadavky na prostředek, které jsou obsluhovány z prostředí Front Door ignorovat řetězce dotazu, dokud nevyprší platnost prostředku uloženého v mezipaměti.

- **Mezi každou jedinečnou adresu URL:** V tomto režimu je každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považován za jedinečný datový zdroj s vlastní mezipamětí. Například odpověď z back-endu pro `www.example.ashx?q=test1` požadavek pro je uložena do mezipaměti v prostředí Front Door a vrácena pro následné mezipaměti se stejným řetězcem dotazu. Požadavek `www.example.ashx?q=test2` pro je uložen do mezipaměti jako samostatný datový zdroj s vlastním nastavením time-to-live.

## <a name="cache-purge"></a>Vyčištění mezipaměti
Přední dveře budou ukládat prostředky do mezipaměti, dokud nevyprší čas vysílání datového zdroje (TTL). Po vypršení platnosti ttl prostředku, když klient požaduje prostředek, prostředí Přední dveře načte novou aktualizovanou kopii datového zdroje, aby sloužil požadavku klienta a ukládal aktualizaci mezipaměti.
</br>Osvědčeným postupem, jak zajistit, aby uživatelé vždy získali nejnovější kopii vašich datových zdrojů, je verze vašich datových zdrojů pro každou aktualizaci a jejich publikování jako nových adres URL. Front Door okamžitě načte nové prostředky pro další požadavky klienta. Někdy můžete chtít vyčistit obsah uložený v mezipaměti ze všech hraničních uzlů a vynutit je, aby načítaly nové aktualizované datové zdroje. To může být způsobeno aktualizacemi webové aplikace nebo rychlou aktualizací prostředků obsahujících nesprávné informace.

</br>Vyberte, jaké prostředky chcete vymazat z hraničních uzlů. Chcete-li vymazat všechny datové zdroje, zaškrtněte políčko Vymazat všechny. V opačném případě zadejte cestu ke každému datovému zdroji, který chcete vymazat, do textového pole Cesta. Níže uvedené formáty jsou podporovány v cestě.
1. **Vymazání jedné cesty**: Vyčistěte jednotlivé datové zdroje zadáním úplné cesty datového zdroje (bez protokolu a domény) s příponou souboru, například /pictures/strasbourg.png;
2. **Čistka se zástupnými symboly**: Hvězdička (\*) může být použita jako zástupný znak. Vymažte všechny složky, podsložky\* a soubory pod koncovým bodem s / v cestě nebo\*vyčistěte všechny podsložky a soubory pod určitou složkou zadáním složky následované / , například /pictures/\*.
3. **Vyčištění kořenové domény**: Vyčistěte kořenový adresář koncového bodu s "/" v cestě.

Vyčištění mezipaměti na předních dveřích nerozlišují malá a velká písmena. Navíc jsou řetězce dotazu agnostik, což znamená, že vymazání adresy URL vymaže všechny varianty řetězce dotazu. 

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti
Následující pořadí záhlaví se používá k určení, jak dlouho bude položka uložena v naší mezipaměti:</br>
1. Cache-Control: s-maxage\<= sekundy>
2. Cache-Control: max-věk\<= sekundy>
3. Vyprší: \<http-date>

Hlavičky odpovědí cache-Control, které označují, že odpověď nebude uložena do mezipaměti, například Cache-Control: private, Cache-Control: no-cache a Cache-Control: no-store jsou dodrženy. Pokud však existuje více požadavků za letu v bodě POP pro stejnou adresu URL, mohou sdílet odpověď. Pokud není k dispozici žádný cache-control výchozí chování je, že AFD bude mezipaměti prostředku x množství času, kde X je náhodně vybral mezi 1 až 3 dny.

## <a name="request-headers"></a>Hlavičky požadavku

Následující hlavičky požadavku nebudou při použití ukládání do mezipaměti předány do back-endu.
- Délka obsahu
- Kódování přenosu

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
