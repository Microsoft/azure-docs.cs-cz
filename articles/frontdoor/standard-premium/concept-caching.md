---
title: 'Přední dveře Azure: ukládání do mezipaměti'
description: Tento článek vám pomůže pochopit chování služby Azure front-in standard/Premium s pravidly směrování, která povolují ukládání do mezipaměti.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564581"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Ukládání do mezipaměti s využitím služby Azure front-end Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

V tomto článku se dozvíte, jak se trasy a sady pravidel front-Premium (Preview) chovají, když máte povolené ukládání do mezipaměti. Přední dvířka Azure je moderní Content Delivery Network (CDN) s dynamickým zrychlením webu a vyrovnáváním zatížení.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Metody žádosti

Jenom metoda GET Request může vygenerovat obsah uložený v mezipaměti v frontách Azure. Všechny ostatní metody žádosti se vždycky proxy přes síť.

## <a name="delivery-of-large-files"></a>Doručení velkých souborů

Přední dveře Standard/Premium (Preview) poskytují velké soubory bez limitu velikosti souboru. Přední dvířka používají techniku nazvanou bloků objektů. Když je požadován velký soubor, přední dveře načte menší části souboru od počátku. Po přijetí úplné žádosti nebo souboru v bajtovém rozsahu požádá prostředí front-end soubor od počátků v blocích po 8 MB.

Po doručení bloku dat do prostředí front dveří je tato část ukládána do mezipaměti a okamžitě se jí dodrží uživateli. Přední dveře potom předem načítají další blok. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud se nestáhne celý soubor (Pokud se požaduje), nebo když klient připojení ukončí.

Pokud chcete získat další informace o požadavku na rozsah bajtů, přečtěte si [dokument RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Přední dveře ukládá do mezipaměti všechny bloky, jakmile jsou přijaty, takže celý soubor není nutné ukládat do mezipaměti na front-dveřích. V případě, že se požadavky na rozsahy souborů nebo bajtů docházejí z mezipaměti, jsou obsluhovány. Pokud se bloky dat neukládají do mezipaměti, pro vyžádání bloků dat z back-endu se používá předběžné načítání. Tato optimalizace spoléhá na zdrojovou schopnost podporovat požadavky na rozsah bajtů. Pokud zdroj nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví.

## <a name="file-compression"></a>Komprese souboru

Projděte si informace o vylepšení výkonu pomocí komprimace souborů v Azure front-dveřích.

## <a name="query-string-behavior"></a>Chování řetězce dotazu

S předními dvířky můžete řídit, jak jsou soubory ukládány do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. V rámci webové žádosti s řetězcem dotazu je řetězec dotazu ta část požadavku, která se vyskytuje po otazníku (?). Řetězec dotazu může obsahovat jednu nebo více párů klíč-hodnota, ve kterých je název pole a jeho hodnota oddělená symbolem rovná se (=). Jednotlivé páry klíč-hodnota jsou oddělené znakem ampersand (&). Například, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Pokud je v řetězci dotazu požadavku více než jedna dvojice klíč-hodnota, nezáleží na jejich pořadí.

* **Ignorovat řetězce dotazů**: v tomto režimu předá přední dvířka řetězce dotazu od žadatele až po počátek prvního požadavku a uloží prostředek do mezipaměti. Všechny požadavky v žádosti o Asset, které jsou obsluhovány z prostředí front-dveří, ignorují řetězce dotazu do vypršení platnosti prostředku uloženého v mezipaměti.

* **Ukládat do mezipaměti každou jedinečnou adresu URL**: v tomto režimu se každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považuje za jedinečný prostředek s vlastní mezipamětí. Například odpověď od počátku pro požadavek na `www.example.ashx?q=test1` je ukládána do mezipaměti v prostředí front-dveří a vrácena pro následná mezipaměť se stejným řetězcem dotazu. Požadavek na `www.example.ashx?q=test2` aplikaci se ukládá do mezipaměti jako samostatný prostředek s vlastním nastavením Time to Live.
* Sadu pravidel můžete použít také k určení chování **řetězce dotazu na klíč mezipaměti** , pro zahrnutí nebo vyloučení zadaných parametrů, když se vygeneruje klíč mezipaměti. Výchozí klíč mezipaměti je například:/foo/image/asset.html a ukázková žádost je `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Existuje pravidlo sady pravidel pro vyloučení řetězce dotazu userid. Pak by se měl zadat klíč mezipaměti řetězce dotazu `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Vyprázdnění mezipaměti

Přečtěte si informace o vyprázdnění mezipaměti.

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti
Následující pořadí hlaviček se používá k určení, jak dlouho bude položka uložená v naší mezipaměti:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Řízení mezipaměti: max. stáří =\<seconds>
3. Expires \<http-date>

Cache-Control hlavičky odpovědí, které naznačují, že odpověď nebude ukládána do mezipaměti, jako je například Cache-Control: Private, Cache-Control: no-cache a Cache-Control: No-Store se nerespektuje.  Pokud není k dispozici žádný Cache-Control, výchozí chování je, že přední dveře budou ukládat prostředky do mezipaměti po dobu X času. Kde X se náhodně vybere v rozmezí od 1 do 3 dnů.

## <a name="request-headers"></a>Hlavičky požadavku

Následující hlavičky žádosti nebudou předány do zdroje při použití ukládání do mezipaměti.
* Délka obsahu
* Transfer-Encoding

## <a name="cache-duration"></a>Doba uložení mezipaměti

Dobu trvání mezipaměti lze nakonfigurovat v sadě pravidel. Nastavení Doba trvání mezipaměti nastavená pomocí sady pravidel je přepsání skutečné mezipaměti. To znamená, že bude používat hodnotu přepisu bez ohledu na to, co je záhlaví odpovědi původu.

## <a name="next-steps"></a>Další kroky

* Další informace o [podmínkách shody sady pravidel](concept-rule-set-match-conditions.md)
* Další informace o [akcích sady pravidel](concept-rule-set-actions.md)