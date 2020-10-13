---
title: Azure SQL Database výstup z Azure Stream Analytics
description: Tento článek popisuje Azure SQL Database jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 4310bd94edd5ebe14eab40b6d19e2bacbdd1b03c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906217"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure SQL Database výstup z Azure Stream Analytics

Můžete použít [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako výstup pro data, která jsou v podstatě relační nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Azure Stream Analytics úlohy zapisují do existující tabulky v SQL Database. Schéma tabulky musí přesně odpovídat polím a jejich typům ve výstupu vaší úlohy. Můžete také zadat [Azure synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako výstup pomocí možnosti výstup SQL Database. Další informace o způsobech zvýšení propustnosti zápisu naleznete v článku [Stream Analytics s Azure SQL Database jako výstup](stream-analytics-sql-output-perf.md) .

Můžete také použít [spravovanou instanci Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) jako výstup. Musíte [nakonfigurovat veřejný koncový bod ve spravované instanci SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a pak ručně nakonfigurovat následující nastavení v Azure Stream Analytics. Virtuální počítač Azure se spuštěným SQL Server s připojenou databází je také podporován ruční konfigurací nastavení níže.

## <a name="output-configuration"></a>Konfigurace výstupu

Následující tabulka uvádí seznam názvů vlastností a jejich popis pro vytvoření výstupu SQL Database.

| Název vlastnosti | Description |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k směrování výstupu dotazu do této databáze. |
| databáze | Název databáze, do které posíláte výstup. |
| Název serveru | Název logického serveru SQL nebo název spravované instance. Pro spravovanou instanci SQL je nutné zadat port 3342. Například *sampleserver. Public. Database. Windows. NET, 3342* |
| Uživatelské jméno | Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Tabulka | Název tabulky, do které se zapisuje výstup V názvu tabulky se rozlišují velká a malá písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typům vygenerovaných výstupem úlohy. |
|Zdědit schéma oddílu| Možnost dědění schématu dělení vašeho předchozího dotazu, která umožňuje úplnou paralelní topologii s více zapisovači v tabulce. Další informace najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximální počet dávek| Doporučený horní limit počtu záznamů odeslaných při každé hromadné vložené transakci.|

Existují dva adaptéry, které umožňují výstup z Azure Stream Analytics do služby Azure synapse Analytics (dřív SQL Data Warehouse): SQL Database a Azure synapse. Pokud platí některá z následujících podmínek, doporučujeme místo SQL Database adaptéru zvolit adaptér Azure synapse Analytics:

* **Propustnost**: Pokud je vaše očekávaná propustnost nyní nebo v budoucnosti větší než 10 MB/s, použijte pro lepší výkon možnost výstup Azure synapse.

* **Vstupní oddíly**: Pokud máte osm nebo více vstupních oddílů, použijte možnost výstup Azure synapse pro lepší horizontální navýšení kapacity.

## <a name="partitioning"></a>Dělení

Dělení je nutné povolit a je založené na klauzuli PARTITION BY v dotazu. Když je povolená možnost zdědit dělení, bude postupovat podle vstupního dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lepšího výkonu propustnosti zápisu při načítání dat do Azure SQL Database naleznete v tématu [Azure Stream Analytics Output to Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy můžete nakonfigurovat pomocí **maximálního počtu dávek**. Výchozí maximum je 10 000 a výchozí minimum je 100 řádků na jedno hromadné vložení. Další informace najdete v tématu [omezení Azure SQL](../sql-database/sql-database-resource-limits.md). Každá dávka je zpočátku hromadně vložena s maximálním počtem dávek. Dávka je rozdělena na polovinu (až do minimálního počtu dávek) na základě opakovaných chyb z SQL.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)
