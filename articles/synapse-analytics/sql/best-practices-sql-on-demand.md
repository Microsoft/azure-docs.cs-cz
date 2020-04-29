---
title: Osvědčené postupy pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics
description: Doporučení a osvědčené postupy, které byste měli znát při práci s SQL na vyžádání (Preview).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429067"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Osvědčené postupy pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku najdete kolekci osvědčených postupů pro používání SQL na vyžádání (Preview). SQL na vyžádání je další prostředek ve službě Azure synapse Analytics.

## <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazování souborů ve vašich účtech úložiště Azure. Nemá místní úložiště ani možnosti ingestování. V takovém případě všechny soubory, které dotaz cílí, jsou externí pro SQL na vyžádání. Všechno, co souvisí s čtením souborů ze služby Storage, může mít dopad na výkon dotazů.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Vyhledání účtu Azure Storage a SQL na vyžádání

Pokud chcete minimalizovat latenci, Najděte svůj účet služby Azure Storage a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro zajištění optimálního výkonu při přístupu k jiným účtům úložiště s SQL na vyžádání se ujistěte, že jsou ve stejné oblasti. Pokud nejsou ve stejné oblasti, dojde k vyšší latenci pro přenos dat v síti mezi oblastmi vzdáleného a koncového bodu.

## <a name="azure-storage-throttling"></a>Omezení Azure Storage

K vašemu účtu úložiště může mít přístup více aplikací a služeb. K omezování úložiště dochází v případě, že kombinované IOPS nebo propustnost vygenerované aplikacemi, službami a úlohami SQL na vyžádání překračují limity účtu úložiště. Výsledkem je, že budete mít výrazný negativní vliv na výkon dotazů.

Po zjištění omezení má na vyžádání SQL na vyžádání vestavěnou manipulaci s tímto scénářem. SQL na vyžádání bude podávat požadavky do úložiště pomalejším tempem, dokud se nevyřeší omezení.

> [!TIP]
> Pro optimální provádění dotazů byste během provádění dotazů neměli nastrese účet úložiště dalšími úlohami.

## <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převede CSV na Parquet-Parquet je sloupcový formát. Vzhledem k tomu, že jsou komprimované, jsou velikosti souborů menší než soubory CSV se stejnými daty. SQL na vyžádání bude potřebovat kratší dobu a požadavky na úložiště pro jeho čtení.
- Pokud se dotaz zaměřuje na jeden velký soubor, budete ho moci rozdělit do několika menších souborů.
- Zkuste zachovat velikost souboru CSV pod 10 GB.
- Je lepší mít soubory stejné velikosti pro jednu cestu OPENROWSET nebo externí umístění tabulky.
- Rozdělení dat do oddílů ukládáním oddílů do různých složek nebo názvů souborů – Pokud [chcete cílit na konkrétní oddíly, ověřte použití funkcí filename a FilePath](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí FileInfo a FilePath k cílení na konkrétní oddíly

Data jsou často organizována v oddílech. SQL na vyžádání můžete dát zadat dotaz na konkrétní složky a soubory. Tato funkce omezí počet souborů a množství dat, které musí dotaz číst a zpracovat. Přidaný bonus znamená, že dosáhnete lepšího výkonu.

Další informace najdete v tématu funkce [filename](develop-storage-files-overview.md#filename-function) a [FilePath](develop-storage-files-overview.md#filepath-function) a příklady, jak [zadávat dotazy na konkrétní soubory](query-specific-files.md).

Pokud vaše uložená data nejsou rozdělená na oddíly, zvažte jejich dělení, aby bylo možné použít tyto funkce k optimalizaci dotazů, které cílí na tyto soubory. Při [dotazování na dělené tabulky Spark](develop-storage-files-spark-tables.md) z SQL na vyžádání bude dotaz automaticky cílit jenom na potřebné soubory.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití CETAS ke zvýšení výkonu a spojení dotazů

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí, které jsou k dispozici v SQL na vyžádání. CETAS je paralelní operace, která vytváří externí metadata tabulky a exportuje výsledky dotazu SELECT do sady souborů v účtu úložiště.

Pomocí CETAS můžete ukládat často používané části dotazů, jako jsou připojené referenční tabulky, do nové sady souborů. V dalším kroku se můžete k této jedné externí tabulce připojit místo opakujících se běžných spojení ve více dotazech.

Když CETAS generuje soubory Parquet, Statistika se automaticky vytvoří, když první dotaz cílí na tuto externí tabulku, což vede k lepšímu výkonu.

## <a name="next-steps"></a>Další kroky

Běžné problémy a řešení najdete v článku [věnovaném řešení potíží](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . Pokud pracujete s fondem SQL místo SQL na vyžádání, přečtěte si prosím článek [osvědčené postupy pro fond SQL](best-practices-sql-pool.md) , kde najdete konkrétní pokyny.
