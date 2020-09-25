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
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 221627a756c69d11ec5385b12970bb835d6a0a0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318450"
---
# <a name="caching-with-azure-front-door"></a>Ukládání do mezipaměti s předními dvířky Azure
Následující dokument určuje chování před dveřmi pomocí pravidel směrování s povoleným ukládáním do mezipaměti. Přední dvířka jsou moderní Content Delivery Network (CDN), a to společně s akcelerací a vyrovnáváním zatížení na dynamickém webu, ale také podporuje chování ukládání do mezipaměti stejně jako jakékoli jiné CDN.

## <a name="delivery-of-large-files"></a>Doručení velkých souborů
Přední dveře Azure poskytují velké soubory bez Cap k velikosti souborů. Přední dvířka používají techniku nazvanou bloků objektů. Když služba Front Door přijme požadavek na velký soubor, načte z back-endu menší části daného souboru. Po přijetí požadavku na úplný soubor nebo rozsah bajtů souboru si prostředí služby Front Door vyžádá soubor z back-endu v blocích dat o velikosti 8 MB.

</br>Po doručení bloku dat do prostředí front-dveří je tato část ukládána do mezipaměti a okamžitě se dodrží uživateli. Přední dveře potom předem načítají další blok. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud nebude stažen celý soubor (Pokud se požaduje), jsou dostupné všechny rozsahy bajtů (Pokud se požaduje), nebo klient připojení ukončí.

</br>Pokud chcete získat další informace o požadavku na rozsah bajtů, přečtěte si [dokument RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Přední dveře ukládá do mezipaměti všechny bloky, jak jsou přijaty, a takže celý soubor není nutné ukládat do mezipaměti na front-dveřích. Následné požadavky na rozsahy souborů nebo bajtů jsou obsluhovány z mezipaměti. Pokud nejsou všechny bloky dat uložené v mezipaměti, pro vyžádání bloků dat z back-endu se používá předběžné načítání. Tato optimalizace spoléhá na schopnost back-endu podporovat požadavky na rozsah bajtů; Pokud back-end nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví.

## <a name="file-compression"></a>Komprese souboru
Přední dvířka můžou dynamicky Komprimovat obsah na hranici, což vede k menší a rychlejší reakci na vaše klienty. Všechny soubory mají nárok na kompresi. Soubor musí mít ale typ MIME, který je vhodný pro seznam komprese. V současné době přední dveře neumožňují změnu tohoto seznamu. Aktuální seznam je:</br>
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
Když požadavek na prostředek určí kompresi a výsledkem požadavku dojde k neúspěšnému vytvoření mezipaměti, přední dveře provádí kompresi assetu přímo na serveru POP. Následně bude komprimovaný soubor obsluhován z mezipaměti. Výsledná položka se vrátí s kódováním transferu: v bloku.

## <a name="query-string-behavior"></a>Chování řetězce dotazu
S předními dvířky můžete řídit, jak jsou soubory ukládány do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. V rámci webové žádosti s řetězcem dotazu je řetězec dotazu ta část požadavku, která se vyskytuje po otazníku (?). Řetězec dotazu může obsahovat jednu nebo více párů klíč-hodnota, ve kterých je název pole a jeho hodnota oddělená symbolem rovná se (=). Jednotlivé páry klíč-hodnota jsou oddělené znakem ampersand (&). Například, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Pokud je v řetězci dotazu požadavku více než jedna dvojice klíč-hodnota, nezáleží na jejich pořadí.
- **Ignorovat řetězce dotazů**: výchozí režim. V tomto režimu předá přední dvířka řetězce dotazů od žadatele k back-endu na první požadavek a uloží Asset do mezipaměti. Všechny následné požadavky na prostředky, které jsou obsluhovány z prostředí front-dveří, ignorují řetězce dotazu do vypršení platnosti prostředku uloženého v mezipaměti.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: v tomto režimu se každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považuje za jedinečný prostředek s vlastní mezipamětí. Například odpověď z back-endu pro požadavek na `www.example.ashx?q=test1` se ukládá do mezipaměti v prostředí front-endu a vrátí se pro následné mezipaměti se stejným řetězcem dotazu. Požadavek na `www.example.ashx?q=test2` aplikaci se ukládá do mezipaměti jako samostatný prostředek s vlastním nastavením Time to Live.

## <a name="cache-purge"></a>Vyprázdnění mezipaměti

Přední dveře ukládá prostředky do mezipaměti, dokud nevyprší hodnota TTL (Time to Live) prostředku. Po vypršení časového limitu TTL prostředku načte aplikace front-end novou aktualizovanou kopii assetu, která bude obsluhovat požadavky klienta a uloží mezipaměť do úložiště.

Osvědčeným postupem je zajistit, aby vaši uživatelé měli vždycky přístup k nejnovější kopii vašich assetů, aby si mohli každou aktualizaci nastavovat vaše prostředky a publikovat je jako nové adresy URL. Přední dvířka okamžitě načtou nové prostředky pro další požadavky klientů. Někdy možná budete chtít vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a pokaždé, když budou všechny získávat nové aktualizované prostředky. Důvodem může být aktualizace webové aplikace nebo rychlé aktualizace prostředků, které obsahují nesprávné informace.

Vyberte prostředky, které chcete vymazat z hraničních uzlů. Pokud chcete zrušit výběr všech prostředků, vyberte **Vymazat vše**. V opačném případě zadejte cestu k jednotlivým assetům **, které**chcete vymazat.

Tyto formáty jsou podporované v seznamech cest, které se mají vyprázdnit:

- **Vyprázdnit jednu cestu**: vyprázdnit jednotlivé prostředky zadáním úplné cesty prostředku (bez protokolu a domény) s příponou souboru, například/Pictures/strasbourg.png;
- **Zástupné znaky**: hvězdička ( \* ) se dá použít jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v rámci koncového bodu pomocí/ \* v cestě nebo vyprázdnit všechny podsložky a soubory v konkrétní složce zadáním složky následovaný/ \* , například/Pictures/ \* .
- **Kořenová doména vyprázdnění**: vyprázdní kořen koncového bodu znakem "/" v cestě.

> [!NOTE]
> **Vymazání domén se zástupnými znaky**: zadání cest v mezipaměti pro vymazání, jak je popsáno v této části, se nevztahuje na žádné domény se zástupnými znaky, které jsou přidruženy k V současné době nepodporujeme přímé vyprazdňování domén se zástupnými znaky. Cesty můžete z konkrétních subdomén vyprázdnit tak, že zadáte tuto subdoménu určitou skupinu a cestu vyprázdnění. Pokud má například moje přední dveře `*.contoso.com` možnost vyprázdnit prostředky subdomény `foo.contoso.com` zadáním `foo.contoso.com/path/*` . V současné době určení názvů hostitelů v cestě k obsahu vyprázdnění je imited k subdoménám domén se zástupnými znaky (Pokud je k dispozici).
>

Mezipaměť vyprázdnění na frontách v mezipaměti nerozlišuje velká a malá písmena. Kromě toho jsou dotazy řetězce nezávislá, což znamená, že při vymazání adresy URL se odstraní všechny variace řetězce dotazu. 

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti
Následující pořadí hlaviček se používá k určení, jak dlouho bude položka uložená v naší mezipaměti:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Řízení mezipaměti: max. stáří =\<seconds>
3. Expires \<http-date>

Hlavičky odpovědí, které určují, že odpověď nebude ukládána do mezipaměti, jako je například řízení mezipaměti: soukromé, Cache-Control: no-cache a Cache-Control: No-Store se nerespektuje.  Pokud není k dispozici žádný ovládací prvek Cache-Control, znamená to, že AFD bude prostředek ukládat do mezipaměti za X dobu, kdy je X náhodně vyskladněno mezi 1 až 3 dny.

## <a name="request-headers"></a>Hlavičky požadavku

Následující hlavičky žádosti nebudou předány do back-endu při použití ukládání do mezipaměti.
- Délka obsahu
- Přenos – kódování

## <a name="cache-duration"></a>Doba uložení mezipaměti

Doba trvání mezipaměti se dá nakonfigurovat jak v aplikaci, tak i v modulu pravidel. Doba trvání mezipaměti nastavená v Návrháři Frontdoor je minimální doba ukládání do mezipaměti. Toto přepsání nebude fungovat, pokud má záhlaví řízení mezipaměti od počátku větší hodnotu TTL než hodnota přepsání. 

Hodnota doba trvání mezipaměti nastavená pomocí stroje pravidel je hodnota true přepsání mezipaměti, což znamená, že bude používat hodnotu přepisu bez ohledu na to, co je záhlaví odpovědi původu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
