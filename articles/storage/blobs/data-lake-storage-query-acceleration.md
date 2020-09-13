---
title: Akcelerace dotazů Azure Data Lake Storage
description: Zrychlení dotazů umožňuje aplikacím a analytickým architekturám významně optimalizovat zpracování dat tak, že načte jenom data, která jsou potřebná pro zpracování operace.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ae92828f08ae4abf9cc28f18872cca27ce747be4
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657638"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Akcelerace dotazů Azure Data Lake Storage

Zrychlení dotazů umožňuje aplikacím a analytickým architekturám významně optimalizovat zpracování dat tím, že načte jenom data, která potřebují k provedení dané operace. Tím se snižuje čas a výpočetní výkon, který je potřeba k získání důležitých přehledů uložených dat.

## <a name="overview"></a>Přehled

Akcelerace dotazů akceptuje *predikáty* filtrování a *projekce sloupců* , které umožňují aplikacím filtrovat řádky a sloupce v okamžiku, kdy data čtou z disku. V rámci sítě do aplikace se přenesou jenom data, která splňují podmínky predikátu. Tím se sníží latence sítě a náklady na výpočetní výkon.  

Pomocí jazyka SQL můžete zadat predikáty filtru řádků a projekce sloupců v požadavku na zrychlení dotazu. Požadavek zpracovává pouze jeden soubor. Proto nejsou podporovány pokročilé relační funkce SQL, například spojení a seskupení podle agregace. Akcelerace dotazů podporuje jako vstup pro každý požadavek data ve formátu CSV a JSON.

Funkce zrychlení dotazu není omezena na Data Lake Storage (účty úložiště s povoleným hierarchickým oborem názvů). Zrychlení dotazů je zcela kompatibilní s objekty BLOB v účtech úložiště, u kterých **není** povolený hierarchický obor názvů. To znamená, že při zpracovávání dat, která jste už uložili jako objekty BLOB v účtech úložiště, můžete dosáhnout stejného snížení latence sítě a nákladů na výpočetní výkon.

Příklad použití zrychlení dotazů v klientské aplikaci najdete v tématu [filtrování dat pomocí Azure Data Lake Storage akceleraci dotazů](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Tok dat

Následující diagram znázorňuje, jak Typická aplikace používá zrychlení dotazu ke zpracování dat.

> [!div class="mx-imgBorder"]
> ![Přehled zrychlení dotazů](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Klientská aplikace požaduje data souborů zadáním predikátů a projekce sloupců.

2. Akcelerace dotazů analyzuje zadaný dotaz SQL a distribuuje práci k analýze a filtrování dat.

3. Procesory čtou data z disku, analyzují data pomocí příslušného formátu a pak filtruje data pomocí zadaných predikátů a projekce sloupců.

4. Zrychlení dotazů kombinuje odpověď horizontálních oddílů ke streamování zpátky do klientské aplikace.

5. Klientská aplikace přijímá a analyzuje odpověď streamování. Aplikace nemusí filtrovat žádná další data a může přímo použít požadovaný výpočet nebo transformaci.

## <a name="better-performance-at-a-lower-cost"></a>Lepší výkon za nižší náklady

Zrychlení dotazů optimalizuje výkon tím, že snižuje množství dat, která vaše aplikace přenáší a zpracovává.

Pro výpočet agregované hodnoty aplikace běžně načítají **všechna** data ze souboru a následně zpracovávají a filtrují data místně. Analýza vstupních/výstupních vzorů pro úlohy analýzy odhalí, že aplikace obvykle vyžadují jenom 20% dat, která si přečtou, aby provedla daný výpočet. Tato statistika je pravdivá i po použití technik, jako je například [vyřazení oddílů](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). To znamená, že 80% těchto dat je zbytečně přenesených přes síť, analyzovat a filtrovat podle aplikací. Tento model, který je v podstatě navržený tak, aby odstranil nepotřebná data, vzniká významné výpočetní náklady.  

I když Azure nabízí špičkovou síť, a to z hlediska propustnosti i latence, zbytečně přenosu dat napříč touto sítí je pro výkon aplikace stále nákladné. Filtrováním nežádoucích dat během žádosti o úložiště vyloučí akcelerace dotazu tyto náklady.

Zatížení procesoru, které je nutné k analýze a filtrování nepotřebných dat, vyžaduje, aby vaše aplikace mohla zřídit větší počet a větší virtuální počítače, aby bylo možné svou práci provést. Díky přenosu tohoto výpočetního zatížení na urychlení dotazů můžou aplikace realizovat výrazné úspory nákladů.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplikace, které můžou využívat akceleraci dotazů

Zrychlení dotazů je navržené pro distribuované analytické architektury a aplikace pro zpracování dat. 

Distribuované analytické architektury, jako jsou Apache Spark a Apache Hive, zahrnují vrstvu abstrakce úložiště v rámci rozhraní. Tyto moduly také obsahují optimalizaci dotazů, které mohou zahrnovat znalosti základních možností vstupně-výstupních služeb při určování optimálního plánu dotazů na dotazy uživatelů. Tyto architektury začínají integrovat urychlení dotazů. Výsledkem je, že uživatelé těchto platforem uvidí lepší latenci dotazů a nižší celkové náklady na vlastnictví, aniž by museli dělat změny v dotazech. 

Akcelerace dotazů je také navržena pro aplikace pro zpracování dat. Tyto typy aplikací obvykle provádějí rozsáhlou transformaci dat, která by nemusela přímo vést k analytickým přehledům, takže nikdy nepoužívají zavedené distribuované analytické architektury. Tyto aplikace často poskytují přímý vztah s podkladovou službou úložiště, takže můžou těžit přímo z funkcí, jako je například zrychlení dotazů. 

Příklad toho, jak může aplikace integrovat akceleraci dotazů, najdete v tématu [filtrování dat pomocí Azure Data Lake Storage akceleraci dotazů](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Ceny

V důsledku zvýšeného výpočetního zatížení v rámci služby Azure Data Lake Storage se cenový model pro použití akcelerace dotazů liší od normálního transakčního modelu Azure Data Lake Storage. Zrychlení dotazů účtuje náklady na množství prověřených dat a také náklady na množství dat vrácených volajícímu. Další informace najdete v tématu [Azure Data Lake Storage Gen2 ceny](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Bez ohledu na změnu modelu fakturace je cenový model zrychlení dotazu navržen tak, aby snížil celkové náklady na vlastnictví úlohy, a to s ohledem na snížení mnohem dražších nákladů na virtuální počítače.

## <a name="next-steps"></a>Další kroky

- [Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů](data-lake-storage-query-acceleration-how-to.md)
- [Referenční dokumentace jazyka SQL pro zrychlení dotazů](query-acceleration-sql-reference.md)


