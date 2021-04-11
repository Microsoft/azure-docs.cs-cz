---
title: Tipy pro zvýšení výkonu
titleSuffix: Azure Cognitive Search
description: Přečtěte si o tipůch a osvědčených postupech pro maximalizaci výkonu služby vyhledávání.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582222"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Tipy pro lepší výkon v Azure Kognitivní hledání

Tento článek je kolekcí tipů a osvědčených postupů, které se často doporučují pro zvýšení výkonu. Poznáte, které faktory mají největší dopad na výkon hledání, vám pomůžou vyhnout se neefektivitám a využívat službu Search. Mezi klíčové faktory patří:

+ Sestavení indexu (schéma a velikost)
+ Typy dotazů
+ Kapacita služby (úroveň a počet replik a oddílů)

## <a name="index-size-and-schema"></a>Velikost indexu a schéma

Dotazy běží rychleji na menších indexech. To je částečně funkce, která má méně polí ke kontrole, ale je také způsobena tím, jak systém ukládá obsah do mezipaměti pro budoucí dotazy. Po prvním dotazu zůstane část obsahu v paměti, kde je prohledávána efektivněji. Vzhledem k tomu, že velikost indexu je v průběhu času v průběhu času, jedním z osvědčených postupů je pravidelně znovu navštěvovat složení indexu, a to jak schématu, tak dokumentů, aby bylo možné hledat možnosti snížení obsahu. Pokud je ale indexem pravá, jediná další kalibrace, kterou můžete udělat, je zvýšit kapacitu: buď [přidáním replik](search-capacity-planning.md#adjust-capacity) nebo upgradem vrstvy služby. Oddíl ["Tip: upgrade na standardní vrstvu S2"]] (#tip-upgrade-na-a-standardní-úroveň) ukazuje, jak vyhodnotit horizontální navýšení kapacity a rozhodnutí o horizontálním navýšení kapacity.

Složitost schématu může také nepříznivě ovlivnit výkon při indexování a dotazování. Nadměrné sestavení připisující pole v omezeních a požadavcích na zpracování. U [komplexních typů](search-howto-complex-data-types.md) se index a dotaz trvá déle. Další části Prozkoumejte jednotlivé podmínky.

### <a name="tip-be-selective-in-field-attribution"></a>Tip: výběr v poli přidělení pole

Běžná chyba, kterou správci a vývojáři vytvářejí při vytváření vyhledávacího indexu, vybírá všechny dostupné vlastnosti pro pole, a to na rozdíl od výběru pouze těch vlastností, které jsou potřeba. Pokud například není nutné, aby pole bylo fulltextově prohledávatelné, přeskočte toto pole při nastavování vyhledávacího atributu.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Selektivní přiřazení" border="true":::

Podpora filtrů, omezujících vlastností a řazení může mít čtyřikrát požadavky na úložiště. Pokud přidáte moduly pro návrhy, požadavky na úložiště se dostanou ještě více. Ilustraci vlivu atributů na úložiště najdete v tématu [atributy a velikost indexu](search-what-is-an-index.md#index-size).

Shrnuto, důsledky převzetí služeb při selhání zahrnují:

+ Snížení výkonu indexování vzhledem k dodatečné práci potřebné ke zpracování obsahu v poli a jeho následnému uložení v rámci indexu obráceného hledání (nastavení "prohledávatelné" atributu pouze pro pole, která obsahují prohledávatelný obsah).

+ Vytvoří větší plochu, kterou musí každý dotaz pokrýt. Všechna pole označená jako prohledávatelný je prohledávána při fulltextovém vyhledávání.

+ Zvyšuje provozní náklady kvůli dodatečnému úložišti. Filtrování a řazení vyžaduje dodatečné místo pro ukládání původních (neanalyzovaných) řetězců. Vyhněte se nastavení filtrovatelné nebo seřaditelné na pole, která je nepotřebují.

+ V mnoha případech přetížení omezuje možnosti daného pole. Pokud je například pole plošky, filtrovatelné a prohledávatelné, můžete ukládat pouze 16 KB textu v rámci pole, zatímco prohledávatelné pole může obsahovat až 16 MB textu.

> [!NOTE]
> Měli byste se vyhnout pouze zbytečnému přidělení. Filtry a omezující vlastnosti jsou často nezbytné pro vyhledávání a v případech, kdy se používají filtry, budete často potřebovat řazení, abyste mohli seřadit výsledky (filtry se vrátí do neuspořádané sady).

### <a name="tip-consider-alternatives-to-complex-types"></a>Tip: Zvažte alternativy pro komplexní typy.

Komplexní datové typy jsou užitečné, pokud data mají složitou vnořenou strukturu, jako je například element nadřízený-podřízený, který se nachází v dokumentech JSON. Nevýhodou komplexních typů je dodatečné požadavky na úložiště a další prostředky, které jsou potřebné k indexování obsahu, v porovnání s nesložitými datovými typy. 

V některých případech se můžete vyhnout těmto kompromisům tím, že namapujete složitou datovou strukturu na zjednodušený typ pole, jako je například kolekce. Alternativně můžete použít možnost sloučit hierarchii polí do jednotlivých polí na úrovni root.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="struktura plochých polí" border="true":::

## <a name="types-of-queries"></a>Typy dotazů

Typy dotazů, které odesíláte, jsou jedním z nejdůležitějších faktorů pro výkon a optimalizace dotazů může významně zlepšit výkon. Při navrhování dotazů Vezměte v úvahu následující body:

+ **Počet vyhledávacích polí.** Každé další prohledávatelné pole vyžaduje další práci vyhledávací služby. Pole prohledávaná v době dotazu můžete omezit pomocí parametru "searchFields". Doporučujeme zadat jenom ta pole, která vás zajímají, aby se zlepšil výkon.

+ **Množství vrácených dat.** Načítání velkého množství obsahu může zpomalit dotazy. Při vytváření struktury dotazu vrátí pouze ta pole, která potřebujete k vykreslení stránky výsledků, a pak načtěte zbývající pole pomocí [vyhledávacího rozhraní API](/rest/api/searchservice/lookup-document) , jakmile uživatel vybere shodu.

+ **Použití částečných hledání** [Částečné hledání](search-query-partial-matching.md), jako je hledání předpon, hledání přibližných výsledků a hledání v regulárních výrazech, je složitější, než typické hledání klíčových slov, protože k tvorbě výsledků vyžaduje úplné prohledávání indexu.

+ **Počet omezujících vlastností.** Přidávání omezujících vlastností do dotazů vyžaduje agregace pro každý dotaz. Obecně platí, že do své aplikace plánujete přidávat jenom omezující vlastnosti, které chcete vykreslit.

+ **Omezte pole s vysokou mohutnou.**  *Pole s vysokým mohutným* odkazem odkazuje na plošku nebo filtrovatelné pole, které má velký počet jedinečných hodnot, a v důsledku toho spotřebovává významné prostředky při výpočtu výsledků. Například když nastavíte pole ID produktu nebo popis jako plošku a filtr, bude se počítat jako vysoká mohutnost, protože většina hodnot z dokumentu do dokumentu je jedinečná.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Tip: místo toho, aby se načetla kritéria filtru, použijte funkci hledání

Když dotaz používá stále [složitá kritéria filtru](search-query-odata-filter.md#filter-size-limitations), výkon vyhledávacího dotazu se sníží. Vezměte v úvahu následující příklad, který demonstruje použití filtrů pro oříznutí výsledků na základě identity uživatele:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

V tomto případě se výrazy filtru používají ke kontrole, zda je jedno pole v každém dokumentu rovno jedné z mnoha možných hodnot identity uživatele. Tento vzor pravděpodobně najdete v aplikacích, které implementují [oříznutí zabezpečení](search-security-trimming-for-azure-search.md) (kontroluje pole obsahující jedno nebo více identifikátorů zabezpečení v seznamu identifikátorů zabezpečení, který představuje uživatele, který dotaz vystavil).

Efektivnější způsob, jak spouštět filtry obsahující velký počet hodnot, je použití [ `search.in` funkce](search-query-odata-search-in-function.md), jak je znázorněno v následujícím příkladu:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Tip: Přidání oddílů pro pomalé jednotlivé dotazy

Pokud je výkon dotazů obecně pomalý, přidání dalších replik často vyřeší tento problém. Ale co když jde o jediný dotaz, který dokončení trvá příliš dlouho? V tomto scénáři nebudou přidány repliky, ale další oddíly můžou. Oddíl rozdělí data mezi další výpočetní prostředky. Dva oddíly rozdělí data na polovinu, třetí oddíl je rozdělí na třetinu a tak dále. 

Jedním z pozitivních vedlejších účinků přidávání oddílů je to, že pomalejší dotazy někdy provádějí rychleji v důsledku paralelního zpracování. Poznamenali jsme paralelní zpracování dotazů s nízkou selektivitou, jako jsou dotazy, které odpovídají mnoha dokumentům, nebo omezující vlastnosti, které poskytují počty přes velký počet dokumentů. Vzhledem k tomu, že je potřeba významné výpočty, aby bylo možné určit skóre relevanci dokumentů nebo počet dokumentů, přidání dalších oddílů pomáhá rychleji provádět dotazy.  

Pokud chcete přidat oddíly, použijte [Azure Portal](search-create-service-portal.md), [PowerShell](search-manage-powershell.md), rozhraní příkazového [řádku Azure](search-manage-azure-cli.md)nebo sadu Management SDK.

## <a name="service-capacity"></a>Kapacita služby

Služba je přetížena v případě, že dotazy trvají příliš dlouho nebo když služba zahajuje žádosti. Pokud k tomu dojde, můžete problém vyřešit upgradem služby nebo přidáním kapacity.

Úroveň vyhledávací služby a počet replik/oddílů mají také velký vliv na výkon. Každá vyšší úroveň poskytuje rychlejší procesory a více paměti, přičemž oba mají pozitivní dopad na výkon.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Tip: upgrade na úroveň Standard S2

Standardní úroveň hledání S1 je často tam, kde zákazníci začínají. Běžným vzorem pro služby S1 je, že indexy se postupně zvětšují, což vyžaduje více oddílů. Více oddílů vede k pomalejším dobám odezvy, takže se přidávají další repliky, které zpracovávají zatížení dotazů. Jak je možné si představit, náklady na spuštění služby S1 teď přestaly úrovněmi nad počáteční konfigurací.

V tomto situaci důležité otázky pro dotazování je, jestli by bylo výhodné přejít na vyšší úroveň, a to na rozdíl od postupného zvýšení počtu oddílů nebo replik aktuální služby. 

Vezměte v úvahu následující topologii jako příklad služby, která se povedla na zvýšení úrovně kapacity:

+ Úroveň Standard S1
+ Velikost indexu: 190 GB
+ Počet oddílů: 8 (na S1, velikost oddílu je 25 GB na oddíl)
+ Počet replik: 2
+ Celkový počet jednotek vyhledávání: 16 (8 oddílů × 2 replik)
+ Hypotetická maloobchodní cena: ~ $4 000 USD/měsíc (Předpokládejme $250 USD × 16 jednotek vyhledávání)

Předpokládejme, že správce služeb stále zobrazuje vyšší míru latence a zvažuje přidání další repliky. Tím se změnil počet replik od 2 do 3 a výsledkem změny počtu jednotek hledání je 24 a výsledná cena $6 000 USD za měsíc.

Pokud se však správce rozhodl přesunout do úrovně Standard S2, topologie by vypadala takto:

+ Úroveň Standard S2
+ Velikost indexu: 190 GB
+ Počet oddílů: 2 (v S2, velikost oddílu je 100 GB na oddíl)
+ Počet replik: 2
+ Celkový počet jednotek vyhledávání: 4 (2. oddíly × 2 repliky)
+ Hypotetická maloobchodní cena: ~ $4 000 USD/měsíc ($1000 USD × 4 jednotky vyhledávání)

Jak je znázorněno v tomto hypotetickém scénáři, můžete mít konfigurace na nižších úrovních, které vedou k podobným nákladům, jako by se vám v prvním místě rozhodlo na vyšší úroveň. Vyšší úrovně však přináší služby Premium Storage, což urychluje indexování. Vyšší úrovně mají také mnohem vyšší výpočetní výkon a navíc paměť. Pro stejné náklady můžete mít výkonnější infrastrukturu, která má stejný index.

Důležitou výhodou přidané paměti je, že více rejstříků může být uloženo do mezipaměti, výsledkem je nižší latence hledání a větší počet dotazů za sekundu. U tohoto doplňkového výkonu nemusí správce potřebovat ani zvýšit počet replik a může potenciálně zaplatit méně než tím, že se zabývají ve službě S1.

## <a name="next-steps"></a>Další kroky

Projděte si tyto další články týkající se výkonu služby.

+ [Analýza výkonu](search-performance-analysis.md)
+ [Zvolit úroveň služby](search-sku-tier.md)
+ [Přidat kapacitu (repliky a oddíly)](search-capacity-planning.md#adjust-capacity)
