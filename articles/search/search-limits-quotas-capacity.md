---
title: Limity služeb pro úrovně a skus
titleSuffix: Azure Cognitive Search
description: Limity služeb používané pro plánování kapacity a maximální limity pro požadavky a odpovědi pro Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549054"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity služby ve službě Azure Cognitive Search

Maximální limity pro úložiště, úlohy a množství indexů a dalších objektů závisí na tom, jestli [zřídíte azure kognitivní vyhledávání](search-create-service-portal.md) na cenových úrovních **Optimalizované pro free**, basic , **standard**nebo **storage Optimize.** **Basic**

+ **Zdarma** je sdílená služba s více tenanty, která je dodávána s vaším předplatným Azure. Indexování a požadavky na dotazy spustit na repliky a oddíly, které jsou používány jinými klienty.

+ **Basic** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, ale sdílí některé síťové infrastruktury s jinými klienty.

+ **Standard** běží na vyhrazených počítačích s větší kapacitou pro ukládání a zpracování na všech úrovních. Standard je dodáván ve čtyřech úrovních: S1, S2, S3 a S3 HD.

+ **Úložiště Optimalizované** běží na vyhrazených počítačích s větší celkovou šířkou pásma úložiště a pamětí než **Standard**. Optimalizované úložiště je k dispozici ve dvou úrovních: L1 a L2

> [!NOTE]
> července jsou všechny úrovně obecně dostupné, včetně úrovně Optimalizované pro úložiště. Všechny ceny naleznete na stránce [Podrobnosti o cenách.](https://azure.microsoft.com/pricing/details/search/)

  S3 S3 S3 High Density (S3 HD) je navržen pro konkrétní úlohy: [víceklientské](search-modeling-multitenant-saas-applications.md) a velké množství malých indexů (tři tisíce indexů na službu). Tato úroveň neposkytuje [funkci indexeru](search-indexer-overview.md). Na S3 HD, ingestování dat musí využít přístup push pomocí volání rozhraní API pro nabízení dat ze zdroje do indexu. 

> [!NOTE]
> Služba je zřízena na určité úrovni. Přechodové úrovně pro získání kapacity zahrnuje zřizování nové služby (neexistuje žádný upgrade na místě). Další informace naleznete [v tématu Volba skladové položky nebo úrovně](search-sku-tier.md). Další informace o úpravě kapacity v rámci služby, kterou jste už zřízené, najdete v [tématu Škálování úrovní prostředků pro dotaz a indexování úloh](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Prostředek | Free | Základní&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |10 |10 |
| Maximální počet jednoduchých polí na index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximální počet komplexních sběrných polí na index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximální počet prvků ve všech&nbsp;komplexních kolekcích na dokument<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximální hloubka složitých polí |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximální [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggesters) na index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximální [profily hodnocení](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximální počet funkcí na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Základní služby vytvořené před prosincem 2017 mají nižší limity (5 místo 15) indexů. Základní úroveň je jediná skladová položka s dolním limitem 100 polí na index.

<sup>2</sup> S velmi velký počet prvků ve složitých kolekcí na dokument aktuálně způsobuje vysoké využití úložiště. Jedná se o známý problém. Do té doby je limit 3000 bezpečnou horní mez pro všechny úrovně služeb. Toto omezení je vynuceno pouze pro operace indexování, které využívají nejstarší obecně`2019-05-06`dostupnou verzi rozhraní API (GA), která podporuje pole komplexního typu ( ) a dále. Chcete-li nepřerušit klienty, kteří mohou používat starší verze rozhraní PREVIEW API (které podporují pole s komplexním typem), nebudeme vynucovat tento limit pro operace indexování, které používají tyto verze rozhraní API preview. Všimněte si, že verze rozhraní PREVIEW rozhraní API nejsou určeny pro produkční scénáře a důrazně doporučujeme zákazníkům přejít na nejnovější verzi rozhraní GA API.

> [!NOTE]
> Zatímco maximální kapacita jednoho indexu je obvykle omezena dostupným úložištěm, existují maximální horní hranice celkového počtu dokumentů, které mohou být uloženy v jednom indexu. Tento limit je přibližně 24 miliard dokumentů na index pro služby basic, S1, S2 a S3 vyhledávání a 2 miliardy dokumentů na index pro vyhledávací služby S3HD. Každý prvek komplexní kolekce počítat jako samostatné dokumenty pro účely těchto limitů.

<a name="document-limits"></a>

## <a name="document-limits"></a>Omezení dokumentů 

Od října 2018 již neexistují žádné limity počtu dokumentů pro žádnou novou službu vytvořenou na libovolné fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD) v libovolné oblasti. Starší služby vytvořené před říjnem 2018 mohou stále podléhat limitům počtu dokumentů.

Chcete-li zjistit, zda má vaše služba omezení dokumentů, použijte [rozhraní REST API statistiky služby GET](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Limity dokumentu se projeví v `null` odpovědi, s označující žádné limity.

### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentu na volání rozhraní API

Maximální velikost dokumentu při volání rozhraní API indexu je přibližně 16 megabajtů.

Velikost dokumentu je ve skutečnosti omezení velikosti těla požadavku rozhraní API indexu. Vzhledem k tomu, že můžete předat dávku více dokumentů do rozhraní API indexu najednou, limit velikosti realisticky závisí na tom, kolik dokumentů je v dávce. Pro dávku s jedním dokumentem je maximální velikost dokumentu 16 MB JSON.

Chcete-li zachovat zmenšil velikost dokumentu, nezapomeňte vyloučit z požadavku nedotazovatelná data. Obrázky a další binární data nejsou přímo dotazovatelné a neměly by být uloženy v indexu. Chcete-li integrovat nedotazovatelná data do výsledků hledání, definujte pole, které není možné prohledávat, které ukládá odkaz na adresu URL na prostředek.

## <a name="indexer-limits"></a>Limity indexeru

Maximální provozní doby existují poskytnout rovnováhu a stabilitu služby jako celku, ale větší datové sady může vyžadovat více času indexování, než umožňuje maximální. Pokud úloha indexování nemůže být dokončena v maximálnípovolené lhůtě, zkuste ji spustit podle plánu. Plánovač sleduje stav indexování. Pokud je z nějakého důvodu přerušena naplánovaná úloha indexování, indexer může při dalším naplánovaném spuštění pokračovat tam, kde naposledy skončil.


| Prostředek | Volný&nbsp;<sup>1</sup> | Základní&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;&nbsp;HD<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |Není dostupné. |10 |10 |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |Není dostupné. |10 |10 |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |Není dostupné. |10 |10 |
| Maximální zatížení indexování na vyvolání |10 000 dokumentů |Omezeno pouze maximálními dokumenty |Omezeno pouze maximálními dokumenty |Omezeno pouze maximálními dokumenty |Omezeno pouze maximálními dokumenty |Není dostupné. |Bez omezení |Bez omezení |
| Minimální plán | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maximální doba chodu <sup>5</sup> | 1-3 minuty |24 hodin |24 hodin |24 hodin |24 hodin |Není dostupné.  |24 hodin |24 hodin |
| Maximální provozní doba pro kognitivní vyhledávání skillsets nebo blob indexování s analýzou obrazu <sup>5</sup> | 3-10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |Není dostupné.  |2 hodiny |2 hodiny |
| Indexer objektů blob: maximální velikost objektu blob, MB |16 |16 |128 |256 |256 |Není dostupné.  |256 |256 |
| Indexer objektů blob: maximální počet znaků obsahu extrahovaného z objektu blob |32,000 |64,000 |4&nbsp;miliony |8&nbsp;milionů |16&nbsp;milionů |Není dostupné. |4&nbsp;miliony |4&nbsp;miliony |

<sup>1</sup> Bezplatné služby mají indexer maximální dobu provádění 3 minuty pro zdroje objektů blob a 1 minutu pro všechny ostatní zdroje dat. Pro indexování ai, která volá do cognitive services, bezplatné služby jsou omezeny na 20 volných transakcí za den, kde je transakce definována jako dokument, který úspěšně prochází kanálu obohacení.

<sup>2</sup> Základní služby vytvořené před prosincem 2017 mají nižší limity (5 místo 15) na indexery, zdroje dat a dovednosti.

<sup>3</sup> Služby S3 HD nezahrnují podporu indexeru.

<sup>4</sup> Maximálně 30 dovedností na skillset.

<sup>5</sup> Úlohy kognitivního vyhledávání a analýza image v indexování objektů blob Azure mají kratší dobu provozu než běžné indexování textu. Analýza obrazu a zpracování přirozeného jazyka jsou výpočtově náročné a spotřebovávají neúměrné množství dostupného výpočetního výkonu. Doba běhu byla zkrácena, aby ostatní úlohy ve frontě měly možnost ke spuštění.  

> [!NOTE]
> Jak je uvedeno v [limitech indexu](#index-limits), indexery budou také vynucovat horní limit 3000 prvků`2019-05-06`ve všech komplexních kolekcích na dokument počínaje nejnovější verzí rozhraní GA API, která podporuje komplexní typy ( ) a dále. To znamená, že pokud jste vytvořili indexer s předchozí verzí rozhraní API, nebudete podléhat tomuto omezení. Chcete-li zachovat maximální kompatibilitu, indexer, který byl vytvořen s `2019-05-06` předchozí verzí rozhraní API a pak aktualizován s verzí rozhraní API nebo novější, bude stále **vyloučen** z omezení. Zákazníci by si měli být vědomi nepříznivého dopadu velmi rozsáhlých komplexních kolekcí (jak bylo uvedeno dříve) a důrazně doporučujeme vytvořit nové indexery s nejnovější verzí rozhraní GA API.

## <a name="synonym-limits"></a>Synonymní limity

Maximální počet povolených map synonym se liší podle cenové úrovně. Každé pravidlo může mít až 20 rozšíření, kde rozšíření je ekvivalentní termín. Například vzhledem k tomu, "kočka", sdružení s "kitty", "kočičí", a "felis" (rod pro kočky) by se počítají jako 3 expanze.

| Prostředek | Free | Základní | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximální mapy synonym |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximální počet pravidel na mapě |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Dotazy za sekundu (QPS)

Odhady QPS musí být vypracovány nezávisle každým zákazníkem. Velikost a složitost indexu, velikost a složitost dotazu a množství provozu jsou primárními determinanty QPS. Neexistuje žádný způsob, jak nabídnout smysluplné odhady, pokud tyto faktory nejsou známy.

Odhady jsou předvídatelnější při výpočtu služeb spuštěných na vyhrazené prostředky (základní a standardní úrovně). QPS můžete odhadnout podrobněji, protože máte kontrolu nad více parametry. Pokyny k přístupu k odhadu najdete v [tématu Výkon a optimalizace azure kognitivního vyhledávání](search-performance-optimization.md).

Pro vrstvy Optimalizované pro úložiště byste měli očekávat nižší propustnost dotazů a vyšší latenci než úrovně Standard.  Metodika pro odhad výkonu dotazu, který budete zažívat, je stejná jako úrovně Standard.

## <a name="data-limits-ai-enrichment"></a>Datové limity (obohacení umělou ai)

Kanál [obohacení ai,](cognitive-search-concept-intro.md) který provádí volání prostředku analýzy textu pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [extrakci klíčových frází](cognitive-search-skill-keyphrases.md), [analýzu mínění](cognitive-search-skill-sentiment.md), [detekci jazyka](cognitive-search-skill-language-detection.md)a [detekci pii](cognitive-search-skill-pii-detection.md) podléhají omezením dat. Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do analyzátoru mínění, použijte [dovednost Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Omezení omezení

Požadavky na vyhledávací dotaz a indexování jsou omezeny, protože systém se blíží maximální kapacitě. Omezení se chová odlišně pro různá api. Soubor API dotazu (hledání/navrhnout/automatické dokončování) a indexování API omezení dynamicky na základě zatížení služby. Index ová l.A. mají statické limity rychlosti požadavků. 

Limity požadavků statické sazby pro operace související s indexem:

+ Indexy seznamu (GET /indexy): 5 za sekundu na vyhledávací jednotku
+ Získat index (GET /indexes/myindex): 10 za sekundu na vyhledávací jednotku
+ Vytvořit index (POST /indexy): 12 za minutu na vyhledávací jednotku
+ Vytvořit nebo aktualizovat index (PUT /indexes/myindex): 6 za sekundu na vyhledávací jednotku
+ Odstranit index (DELETE /indexes/myindex): 12 za minutu na vyhledávací jednotku 

## <a name="api-request-limits"></a>Omezení požadavků rozhraní API
* Maximálně 16 MB na požadavek <sup>1</sup>
* Maximálně 8 kb délka ADRESY URL
* Maximálně 1000 dokumentů na dávku nahrání, sloučení nebo odstranění indexu
* Maximálně 32 polí v $orderby klauzuli
* Maximální velikost hledaného výrazu je 32 766 bajtů (32 kB mínus 2 bajty) zakódovaného textu UTF-8

<sup>1</sup> V Azure Cognitive Search tělo požadavku podléhá horní limit 16 MB, kterým se stanoví praktické omezení obsahu jednotlivých polí nebo kolekcí, které nejsou jinak omezeny teoretické limity (viz [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) další informace o složení pole a omezení).

## <a name="api-response-limits"></a>Limity odezvy rozhraní API
* Maximálně 1000 dokumentů vrácených na stránku s výsledky hledání
* Maximálně 100 vrácených návrhů na žádost o rozhraní API suggest

## <a name="api-key-limits"></a>Limity klíčů rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou určeny v hlavičce požadavku a udělují službu úplný přístup pro čtení a zápis. Klávesy dotazu jsou jen pro čtení, zadané na adrese URL a obvykle distribuovány do klientských aplikací.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíčů dotazu na službu