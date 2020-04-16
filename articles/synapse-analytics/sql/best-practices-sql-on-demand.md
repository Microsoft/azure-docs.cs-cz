---
title: Doporučené postupy pro SQL na vyžádání (preview) v Azure Synapse Analytics
description: Doporučení a osvědčené postupy, které byste měli znát při práci s SQL na vyžádání (náhled).
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429067"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Doporučené postupy pro SQL na vyžádání (preview) v Azure Synapse Analytics

V tomto článku najdete kolekci osvědčených postupů pro použití SQL na vyžádání (náhled). SQL na vyžádání je další prostředek v rámci Azure Synapse Analytics.

## <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazovat se na soubory ve vašich účtech úložiště Azure. Nemá místní úložiště nebo možnosti ingestování. Jako takové všechny soubory, které cíle dotazu jsou externí SQL na vyžádání. Vše, co souvisí se čtením souborů z úložiště, může mít vliv na výkon dotazu.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Azure Storage účet a SQL na vyžádání

Chcete-li minimalizovat latenci, můžete spoluumístit účet úložiště Azure a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro optimální výkon, pokud máte přístup k jiným účtům úložiště s SQL na vyžádání, ujistěte se, že jsou ve stejné oblasti. Pokud nejsou ve stejné oblasti, bude zvýšena latence pro přenos sítě dat mezi vzdálené ho a koncového bodu oblastí.

## <a name="azure-storage-throttling"></a>Omezení úložiště Azure

K vašemu účtu úložiště může přistupovat více aplikací a služeb. Omezení úložiště dochází, když kombinované VOPS nebo propustnost generované aplikace, služby a sql úlohy na vyžádání překročí limity účtu úložiště. V důsledku toho dojde k významnému negativnímu vlivu na výkon dotazu.

Po zjištění omezení sql na vyžádání má integrované zpracování tohoto scénáře. SQL na vyžádání bude vyžadovat úložiště pomalejším tempem, dokud nebude vyřešeno omezení.

> [!TIP]
> Pro optimální spuštění dotazu byste neměli stres účet úložiště s jinými úlohami během provádění dotazu.

## <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převést CSV na parkety - Parkety je sloupcový formát. Vzhledem k tomu, že je komprimovaný, jeho velikost souborů je menší než soubory CSV se stejnými daty. SQL na vyžádání bude potřebovat méně času a požadavky na úložiště číst.
- Pokud dotaz cílí na jeden velký soubor, budete mít prospěch z jeho rozdělení do více menších souborů.
- Zkuste zachovat velikost souboru CSV pod 10 GB.
- Je lepší mít stejně velké soubory pro jednu cestu OPENROWSET nebo externí tabulku LOCATION.
- Rozdělte data uložením oddílů do různých složek nebo názvů souborů - zkontrolujte, zda [použijte funkce filename a path pro cílové oddíly](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí fileinfo a filepath k cílení na určité oddíly

Data jsou často uspořádána v oddílech. Můžete dát pokyn SQL na vyžádání k dotazování na určité složky a soubory. Tato funkce sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. Bonusem je, že dosáhnete lepšího výkonu.

Další informace naleznete v nabídce funkcí [název souboru](develop-storage-files-overview.md#filename-function) a [cesty k souborům](develop-storage-files-overview.md#filepath-function) a příklady [dotazování určitých souborů](query-specific-files.md).

Pokud vaše uložená data nejsou rozdělena na oddíly, zvažte jejich rozdělení, abyste mohli tyto funkce použít k optimalizaci dotazů zaměřených na tyto soubory. Při [dotazování rozdělených tabulek Spark](develop-storage-files-spark-tables.md) z SQL na vyžádání se dotaz automaticky zaměří pouze na potřebné soubory.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití cetas umocnění výkonu dotazů a spojení

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí dostupných v SQL na vyžádání. CETAS je paralelní operace, která vytváří metadata externí tabulky a exportuje výsledky dotazu SELECT do sady souborů v účtu úložiště.

Cetas můžete použít k ukládání často používaných částí dotazů, jako jsou spojené referenční tabulky, do nové sady souborů. Dále se můžete připojit k této jedné externí tabulce namísto opakování běžných spojení ve více dotazech.

Jak CETAS generuje soubory parket, statistiky budou automaticky vytvořeny, když první dotaz cílí na tuto externí tabulku, což vede ke zlepšení výkonu.

## <a name="next-steps"></a>Další kroky

V článku [Řešení potíží](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) najdete běžné problémy a řešení. Pokud pracujete s fondem SQL spíše než SQL na vyžádání, naleznete v článku [doporučené postupy pro fond SQL](best-practices-sql-pool.md) pro konkrétní pokyny.
