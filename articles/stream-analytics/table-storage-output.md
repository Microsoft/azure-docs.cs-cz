---
title: Výstup tabulkového úložiště z Azure Stream Analytics
description: Tento článek popisuje službu Azure Table Storage jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ea775ef472687485dbd2f30c4f60adc33c0eaa73
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127344"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Výstup tabulkového úložiště z Azure Stream Analytics

[Azure Table Storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné, široce škálovatelné úložiště, aby se aplikace mohla automaticky škálovat tak, aby splňovala požadavky uživatelů. Table Storage je úložiště NoSQLch klíčů a atributů od Microsoftu, které můžete použít pro strukturovaná data s menším omezením schématu. Služba Azure Table Storage se dá použít k ukládání dat do trvalých a efektivních načtení.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu tabulky.

| Název vlastnosti | Description |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do tohoto úložiště tabulek. |
| Účet úložiště |Název účtu úložiště, do kterého posíláte výstup. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště |
| Název tabulky |Název tabulky Tabulka se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce, který obsahuje klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v tabulce, který tvoří první část primárního klíče entity. Je to hodnota řetězce, která může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce, který obsahuje klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí (100) je pro většinu úloh dostačující. Další informace o úpravách tohoto nastavení najdete v článku [specifikace operace dávky tabulky](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="partitioning"></a>Dělení

Klíč oddílu je libovolný výstupní sloupec. Počet zapisovačů výstupu následuje za vstupními oddíly pro [plně paralelní dotazy](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy najdete v části [omezení Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Výchozí hodnota je 100 entit na jednu transakci, ale můžete ji podle potřeby nakonfigurovat na menší hodnotu.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)