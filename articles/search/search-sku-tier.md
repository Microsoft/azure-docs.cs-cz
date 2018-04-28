---
title: Zvolte SKU nebo cenovou úroveň pro službu Azure Search | Microsoft Docs
description: 'Vyhledávání systému Azure se dá zřídit na tyto identifikátory SKU: volné, Basic a Standard, kde Standard je k dispozici v různých konfigurace prostředků a kapacity úrovně.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 6fe41a8c4d184fef4d1bb0a12fed44a49ef8a6da
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Zvolte SKU nebo cenovou úroveň pro službu Azure Search.
Ve službě Azure Search [je služba zřízena](search-create-service-portal.md) na konkrétní cenová úroveň nebo SKU. Mezi možnosti patří **volné**, **základní**, nebo **standardní**, kde **standardní** je k dispozici v několika konfigurace a kapacity.

Účelem tohoto článku je vám pomohou zvolit vrstvu. Pokud kapacitu vrstvy jím je příliš malá, musíte zřídit novou službu na vyšší úroveň a potom ho znovu načtěte vaší indexy. Neexistuje žádné místní upgrade stejné služby z jednoho identifikátoru SKU do jiného.

> [!NOTE]
> Po zvolte úroveň a [zřídit službu vyhledávání](search-create-service-portal.md)počty oddílu v rámci služby, a může zvýšit repliky. Pokyny najdete v tématu [škálovat prostředek úrovně pro dotaz a indexování úlohy](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Postupy, dosahují cenovou úroveň rozhodnutí
Ve službě Azure Search určuje úroveň kapacitu, není dostupnosti funkce. Obecně platí funkce jsou dostupné v každé vrstvě, včetně funkce verze preview. Jedinou výjimkou je žádná podpora pro indexery ve vysokém rozlišení S3.

> [!TIP]
> Doporučujeme vám, že vždy zřizovat **volné** služby (jeden na předplatné s bez časového omezení) tak, aby jeho snadno dostupné pro šedé – projekty. Použití **volné** služba pro testování a vyhodnocení; vytvořte druhý fakturovatelný službu na **základní** nebo **standardní** vrstva pro produkci nebo větší testovací úlohy.
>
>

Kapacita a nákladů na běžící služba přejděte ruční v dolním. Informace v tomto článku vám může pomoct rozhodnout, které zajišťuje rovnováhu mezi SKU, ale pro některý z mohla být užitečná, je třeba alespoň hrubý odhady na následující:

* Počet a velikost indexy, které chcete vytvořit
* Počet a velikost dokumenty nahrát
* Přehled o dotazu svazku, z hlediska dotazy na druhý (QPS). Pokyny najdete v tématu [výkonu Azure Search a optimalizace](search-performance-optimization.md).

Počet a velikost jsou důležité, protože maximální limitu prostřednictvím pevný limit na počet indexů pro službu nebo na prostředky (úložiště nebo repliky), který používá služba. Skutečné limit služby je podle toho, co získá vyčerpáte první: prostředky nebo objekty.

Odhady v dolním má následující kroky zjednodušit proces:

* **Krok 1** zkontrolujte-li se dozvědět o dostupných parametrech SKU popisy.
* **Krok 2** odpovězte na otázky níže přicházejí na předběžné rozhodnutí.
* **Krok 3** dokončit vaše rozhodnutí Kontrola pevných limitů na úložiště a ceny.

## <a name="sku-descriptions"></a>Popisy SKU
Následující tabulka obsahuje popis každého vrstvy.

| Vrstva | Primární scénáře |
| --- | --- |
| **Volné** |Sdílené služby, bez poplatků, používat pro vyhodnocení, šetření nebo malé úlohy. Vzhledem k tomu, že jsou sdílena s další odběratele, propustnost dotazu a indexování se liší podle uživatele, kteří je pomocí služby. Kapacita je malý (50 MB nebo 3 indexy s až 10 000 dokumentů. každý). |
| **Basic** |Malé produkční úlohy na vyhrazeném hardwaru. Vysoce dostupný. Kapacita je až 3 repliky a 1 oddílu (2 GB). |
| **S1** |Standardní 1 podporuje flexibilní kombinace oddíly (12) a repliky (12), použít pro střední produkční zatížení na vyhrazeném hardwaru. Přidělením replik v kombinacích nepodporuje maximální počet jednotek 36 fakturovatelný vyhledávání a oddíly. Na této úrovni oddíly jsou 25 GB. |
| **S2** |Spustí standardní 2 větší úlohy v produkčním prostředí pomocí stejné 36 vyhledávání jednotky jako S1, ale s větší velikostí oddílů a repliky. Na této úrovni oddíly jsou 100 GB. |
| **S3** |Standardní 3 běží úměrně větší úlohy v produkčním prostředí na vyšší end systémy, v konfiguracích až 12 oddíly nebo 12 repliky jednotek v části 36 vyhledávání. Na této úrovni oddíly jsou 200 GB. |
| **S3 HD** |Standardní 3 hustotou je určená pro velký počet menší indexy. Může mít až 3 oddíly, na 200 GB.|

> [!NOTE]
> Maximální hodnoty repliky a oddíl se účtují se jako vyhledávání jednotek (36 maximální pro službu), které ukládá nižší limit efektivní než maximální znamená v hodnotě řez. Například pokud chcete použít maximálně 12 repliky, může mít nejvýše 3 oddíly (12 * 3 = 36 jednotek). Podobně použít maximální oddíly, snížíte repliky 3. V tématu [škálovat prostředek úrovně pro dotaz a indexování úlohy ve službě Azure Search](search-capacity-planning.md) pro graf na povolené kombinace.
>
>

## <a name="review-limits-per-tier"></a>Zkontrolujte omezení na vrstvě
Následující graf je podmnožinou omezení z [omezení služby ve službě Azure Search](search-limits-quotas-capacity.md). Zobrazí se seznam faktory nejpravděpodobněji mít vliv na rozhodnutí SKU. Tento graf může být při revizi níže uvedené otázky.

| Prostředek | Free | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Smlouva SLA |Ne <sup>1</sup> |Ano |Ano |Ano |Ano |Ano |
| Omezení indexu |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Omezení dokumentů vztahuje |10 000 celkem |1 milion pro službu |15 milionů na oddíly |60 milionů na oddíly |120 milionů na oddíly |1 milion na index |
| Maximální oddíly |neuvedeno |1 |12 |12 |12 |3 <sup>2</sup> |
| Velikost oddílu |Celkový počet 50 MB |2 GB pro službu |25 GB na oddíly |100 GB na oddíl (až do maximálního počtu 1.2 TB pro službu) |200 GB na oddíl (až do maximálního počtu 2.4 TB pro službu) |200 GB (až do maximálního počtu 600 GB pro službu) |
| Maximální repliky |neuvedeno |3 |12 |12 |12 |12 |

<sup>1</sup> volné vrstvy a preview funkce není součástí smlouvy o úrovni služeb (SLA). Pro všechny fakturovatelné úrovně SLA projeví při zřizování dostatečná redundance pro vaši službu. Dvě nebo více replik jsou požadovány pro SLA dotazu (načíst). Tři nebo více replik jsou požadovány pro dotazy a indexování smlouvy o úrovni služeb (pro čtení a zápis). Počet oddílů není k posouzení SLA. 

<sup>2</sup> S3 a S3 HD jsou zajišťované identické vysoké kapacity infrastruktury ale každé z nich dosáhne své maximální limit různými způsoby. S3 cílí menší počet velké indexy. Jako takový jeho maximální limit je vázán na prostředků (pro každou službu 2.4 TB). S3 HD cílí velký počet velmi malé indexy. Na 1000 indexy S3 HD dosaženo omezení ve formě indexu omezení. Pokud jste zákazník S3 HD, který vyžaduje více než 1 000 indexy, obraťte se na Microsoft Support informace o tom, jak pokračovat.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Odstranění položek SKU, které nesplňují požadavky
Na následující otázky vám může pomoct přicházejí na správné rozhodnutí SKU pro úlohy.

1. Máte **smlouvy o úrovni služeb (SLA)** požadavky? Můžete použít libovolné fakturovatelný úrovně (základní na nahoru), ale je nutné nakonfigurovat služby pro redundanci. Dvě nebo více replik jsou požadovány pro SLA dotazu (načíst). Tři nebo více replik jsou požadovány pro dotazy a indexování smlouvy o úrovni služeb (pro čtení a zápis). Počet oddílů není k posouzení SLA.
2. **Kolik indexuje** chcete? Jeden z největších proměnné řešení do rozhodnutí SKU je počet indexů nepodporuje každý SKU. Podpora index je výrazně liší úrovni v nižší cenové úrovně. Požadavky na počet indexů může být primární určující SKU rozhodnutí.
3. **Kolik dokumenty** bude načten do každé indexu? Počet a velikost dokumentů určí případnou velikost index. Za předpokladu, že můžete odhadnout velikost předpokládané indexu, můžete porovnat počet pro velikost oddílu za SKU, prodloužena počet oddílů, které jsou potřebné pro uložení indexu této velikosti.
4. **Co je očekávané dotazu zatížení**? Jakmile jsou požadavky na úložiště pochopeny, vezměte v úvahu dotazu úlohy. S2 a obě položky S3 nabízejí téměř ekvivalentní propustnost, ale bude požadavků SLA vyloučit všechny preview SKU.
5. Pokud uvažujete o úroveň S2 nebo S3, zjistěte, zda chcete, aby [indexery](search-indexer-overview.md). Indexery ještě nejsou k dispozici pro vrstvu S3 HD. Alternativní způsob je použití push model aktualizací index, kde můžete psát kód aplikace tak, aby nabízel sady dat do indexu.

Většina zákazníků může pravidlo konkrétní SKU příchozí nebo odchozí, na základě jejich odpovědí na otázky výše. Pokud si pořád nejste jistí které SKU pomocí, můžete Ptejte se na webu MSDN nebo StackOverflow fóra, nebo požádejte podporu Azure o další pokyny.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Decision ověření: verze SKU nabízí systém dostatečné úložiště a QPS?
V posledním kroku, pokroku [stránce s cenami](https://azure.microsoft.com/pricing/details/search/) a [částech-service a za index v omezení služby](search-limits-quotas-capacity.md) znovu zkontrolovat odhady proti limitů předplatného a služby.

Pokud požadavky na cenu nebo úložiště jsou mimo rozsah, můžete chtít Refaktorovat zatížení mezi více menší služeb (například). Na podrobnější úrovni můžete změnit návrh indexy na menší hodnotu, nebo použití filtrů k efektivní dotazy.

> [!NOTE]
> Požadavky na úložiště může být přepsání zvýšeným, pokud dokumenty obsahují nadbytečné data. V ideálním případě dokumenty obsahují pouze s možností vyhledávání dat nebo metadat. Binární data je jiný prohledávat a je třeba uložit odděleně (třeba v tabulce nebo objekt blob úložiště Azure) s pole v indexu pro uložení adresy URL odkaz na externí data. Maximální velikost jednotlivých dokumentu je 16 MB (nebo méně Pokud jste hromadné odesílání více dokumentů v jedné žádosti). V tématu [omezení ve službě Azure Search služby](search-limits-quotas-capacity.md) Další informace.
>
>

## <a name="next-step"></a>Další krok
Jakmile víte, které je vpravo fit SKU, pokračujte v těchto krocích:

* [Vytvoření služby search na portálu](search-create-service-portal.md)
* [Změnit přidělení oddíly a repliky škálování služby](search-capacity-planning.md)
