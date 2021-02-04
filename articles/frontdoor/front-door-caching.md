---
title: Přední vrátka Azure – ukládání do mezipaměti | Microsoft Docs
description: Tento článek vám pomůže porozumět chování pro přední dveře pomocí pravidel směrování, která povolují ukládání do mezipaměti.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538047"
---
# <a name="caching-with-azure-front-door"></a>Ukládání do mezipaměti s předními dvířky Azure
Následující dokument určuje chování před dveřmi pomocí pravidel směrování s povoleným ukládáním do mezipaměti. Přední dveře jsou moderní Content Delivery Network (CDN) s dynamickým zrychlením webu a vyrovnáváním zatížení, ale také podporuje chování ukládání do mezipaměti stejně jako jakékoli jiné CDN.

## <a name="delivery-of-large-files"></a>Doručení velkých souborů
Přední dveře Azure poskytují velké soubory bez Cap k velikosti souborů. Přední dvířka používají techniku nazvanou bloků objektů. Když služba Front Door přijme požadavek na velký soubor, načte z back-endu menší části daného souboru. Po přijetí úplné žádosti nebo souboru v bajtovém rozsahu požádá prostředí front dveří soubor z back-endu v blocích o velikosti 8 MB.

Po doručení bloku dat do prostředí front dveří je tato část ukládána do mezipaměti a okamžitě se jí dodrží uživateli. Přední dveře potom předem načítají další blok. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud se nestáhne celý soubor (Pokud se požaduje), nebo když klient připojení ukončí.

Pokud chcete získat další informace o požadavku na rozsah bajtů, přečtěte si [dokument RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Přední dveře ukládá do mezipaměti všechny bloky, jakmile jsou přijaty, takže celý soubor není nutné ukládat do mezipaměti na front-dveřích. V případě, že se požadavky na rozsahy souborů nebo bajtů docházejí z mezipaměti, jsou obsluhovány. Pokud se bloky dat neukládají do mezipaměti, pro vyžádání bloků dat z back-endu se používá předběžné načítání. Tato optimalizace spoléhá na možnosti back-endu pro podporu požadavků na rozsah bajtů. Pokud back-end nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví.

## <a name="file-compression"></a>Komprese souboru
Přední dvířka můžou dynamicky Komprimovat obsah na hranici, což má za následek menší a rychlejší odezvu na vaše klienty. Aby byl soubor způsobilý pro komprimaci, musí být povoleno ukládání do mezipaměti a soubor musí být typu MIME, aby byl vhodný pro kompresi. V současné době přední dveře neumožňují změnu tohoto seznamu. Aktuální seznam je:
- "Application/EOT"
- "Application/Font"
- "Application/Font-sfnt"
- "Application/JavaScript"
- application/json
- "Application/OpenType"
- "Application/OTF"
- "Application/PKCS7-MIME"
- "Application/TrueType"
- "Application/TTF",
- "application/vnd. MS-fontobject"
- "Application/XHTML + XML"
- "Application/XML"
- "Application/XML + RSS"
- "application/x-Font-OpenType"
- "application/x-Font-TrueType"
- "application/x-Font-TTF"
- "application/x-httpd-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-perl"
- "application/x-TTF"
- "application/x-JavaScript"
- Font/EOT
- Font/TTF
- Font/OTF
- Font/OpenType
- Image/SVG + XML
- text/CSS
- text/CSV
- text/HTML
- text/JavaScript
- "text/js", "text/prostý"
- text/RTF
- "text/tabulátor – oddělené hodnoty"
- text/XML
- "text/x-Script"
- "text/x-Component"
- "text/x-Java-Source"

Kromě toho musí mít soubor také velikost od 1 KB do 8 MB (včetně).

Tyto profily podporují následující kódování komprese:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Pokud požadavek podporuje kompresi gzip a Brotli, má přednost komprese Brotli.</br>
Když požadavek na prostředek určí kompresi a výsledkem požadavku dojde k neúspěšnému přihlášení do mezipaměti, přední dveře provádí kompresi assetu přímo na serveru POP. Následně bude komprimovaný soubor obsluhován z mezipaměti. Výsledná položka se vrátí s kódováním transferu: v bloku.

## <a name="query-string-behavior"></a>Chování řetězce dotazu
S předními dvířky můžete řídit, jak jsou soubory ukládány do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. V rámci webové žádosti s řetězcem dotazu je řetězec dotazu ta část požadavku, která se vyskytuje po otazníku (?). Řetězec dotazu může obsahovat jednu nebo více párů klíč-hodnota, ve kterých je název pole a jeho hodnota oddělená symbolem rovná se (=). Jednotlivé páry klíč-hodnota jsou oddělené znakem ampersand (&). Například `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Pokud je v řetězci dotazu požadavku více než jedna dvojice klíč-hodnota, nezáleží na jejich pořadí.
- **Ignorovat řetězce dotazů**: v tomto režimu předá přední dvířka řetězce dotazů od žadatele k back-endu na první požadavek a uloží Asset do mezipaměti. Všechny požadavky v žádosti o Asset, které jsou obsluhovány z prostředí front-dveří, ignorují řetězce dotazu do vypršení platnosti prostředku uloženého v mezipaměti.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: v tomto režimu se každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považuje za jedinečný prostředek s vlastní mezipamětí. Například odpověď z back-endu pro požadavek na `www.example.ashx?q=test1` se ukládá do mezipaměti v prostředí front-endu a vrátí se pro následná mezipaměť se stejným řetězcem dotazu. Požadavek na `www.example.ashx?q=test2` aplikaci se ukládá do mezipaměti jako samostatný prostředek s vlastním nastavením Time to Live.

## <a name="cache-purge"></a>Vyprázdnění mezipaměti

Přední dveře ukládá prostředky do mezipaměti, dokud nevyprší hodnota TTL (Time to Live) prostředku. Když klient požádá o Asset s vypršenou hodnotou TTL, prostředí front-dveří načte novou aktualizovanou kopii assetu, která bude obsluhovat požadavek, a pak uloží obnovenou mezipaměť.

Osvědčeným postupem je zajistit, aby vaši uživatelé měli vždycky přístup k nejnovější kopii vašich assetů, aby si mohli každou aktualizaci nastavovat vaše prostředky a publikovat je jako nové adresy URL. Přední dvířka okamžitě načtou nové prostředky pro další požadavky klientů. Někdy možná budete chtít vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a pokaždé, když budou všechny získávat nové aktualizované prostředky. Důvodem může být aktualizace webové aplikace nebo rychlé aktualizace prostředků, které obsahují nesprávné informace.

Vyberte prostředky, které chcete vymazat z hraničních uzlů. Pokud chcete zrušit výběr všech prostředků, vyberte **Vymazat vše**. V opačném případě zadejte cestu k jednotlivým assetům **, které** chcete vymazat.

Tyto formáty jsou podporované v seznamech cest, které se mají vyprázdnit:

- **Vyprázdnit jednu cestu**: vyprázdnit jednotlivé prostředky zadáním úplné cesty prostředku (bez protokolu a domény) s příponou souboru, například/Pictures/strasbourg.png;
- **Zástupné znaky**: hvězdička ( \* ) se dá použít jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v rámci koncového bodu pomocí/ \* v cestě nebo vyprázdnit všechny podsložky a soubory v konkrétní složce zadáním složky následovaný/ \* , například/Pictures/ \* .
- **Kořenová doména vyprázdnění**: vyprázdní kořen koncového bodu znakem "/" v cestě.

> [!NOTE]
> **Vymazání domén se zástupnými znaky**: zadání cest v mezipaměti pro vymazání, jak je popsáno v této části, se nevztahuje na žádné domény se zástupnými znaky, které jsou přidruženy k V současné době nepodporujeme přímé vyprazdňování domén se zástupnými znaky. Cesty můžete z konkrétních subdomén vyprázdnit tak, že zadáte tuto subdoménu určitou skupinu a cestu vyprázdnění. Pokud má například moje přední dveře `*.contoso.com` možnost vyprázdnit prostředky subdomény `foo.contoso.com` zadáním `foo.contoso.com/path/*` . V současné době určení názvů hostitelů v cestě k obsahu vyprázdnění je imited k subdoménám domén se zástupnými znaky (Pokud je k dispozici).
>

Mezipaměť vyprázdnění na frontách v mezipaměti nerozlišuje velká a malá písmena. Kromě toho se dotazují na řetězec nezávislá, což znamená, že vymazání adresy URL vyprázdní všechny variace řetězce dotazu. 

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti
Následující pořadí hlaviček se používá k určení, jak dlouho bude položka uložená v naší mezipaměti:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Řízení mezipaměti: max. stáří =\<seconds>
3. Expires \<http-date>

Cache-Control hlavičky odpovědí, které naznačují, že odpověď nebude ukládána do mezipaměti, jako je například Cache-Control: Private, Cache-Control: no-cache a Cache-Control: No-Store se nerespektuje.  Pokud není k dispozici žádný Cache-Control, výchozí chování je, že přední dveře budou ukládat prostředky do mezipaměti po dobu X času, kdy se X náhodně vybere 1 až 3 dny.

## <a name="request-headers"></a>Hlavičky požadavku

Při použití ukládání do mezipaměti nebudou předávány následující hlavičky požadavků do back-endu.
- Délka obsahu
- Transfer-Encoding

## <a name="cache-duration"></a>Doba uložení mezipaměti

Doba trvání mezipaměti se dá nakonfigurovat jak v aplikaci, tak i v modulu pravidel. Minimální doba uložení v mezipaměti je nastavená doba trvání mezipaměti v Návrháři front-dveří. Toto přepsání nebude fungovat, pokud má záhlaví řízení mezipaměti od počátku větší hodnotu TTL než hodnota přepsání. 

Doba trvání mezipaměti nastavená pomocí stroje pravidel je hodnota true přepsání mezipaměti, což znamená, že se použije hodnota přepsat bez ohledu na to, co je hlavička odpovědi na zdroj.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
