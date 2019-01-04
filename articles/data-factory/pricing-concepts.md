---
title: Vysvětlení, příklady ceny služby Azure Data Factory | Dokumentace Microsoftu
description: Tento článek popisuje a předvádí cenový model s podrobné příklady služby Azure Data Factory
documentationcenter: ''
author: shlo
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: ec5ebfa29af13b5a34617be7f423fd7bc220636b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017748"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Vysvětlení, příklady ceny služby Data Factory

Tento článek popisuje a předvádí cenový model s podrobné příklady služby Azure Data Factory.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopírování dat z AWS S3 do úložiště objektů Blob v Azure každou hodinu

V tomto scénáři budete chtít kopírování dat z AWS S3 do úložiště objektů Blob v Azure na hodinový plán.

K provedení scénáře, budete muset vytvořit kanál s následujícími položkami:

1. Aktivitu kopírování, která se vstupní datovou sadu pro data, která mají být zkopírovány z AWS S3.

2. Výstupní datovou sadu dat ve službě Azure Storage.

3. Aktivační událost plánovače ke spuštění kanálu každou hodinu.

 ![Scenario1](media/pricing-concepts/scenario1.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvoření propojené služby | 2 r/w entity  |
| Vytvoření datových sad | 4 entity čtení/zápis (2 pro vytvoření datové sady a 2 pro odkazy na propojenou službu) |
| Vytvořit kanál | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získání kanálu | 1 pro čtení a zápis entity |
| Spuštění kanálu | 2 spuštění aktivit (1 pro spuštění, 1 pro aktivity spuštění aktivační události) |
| Kopírování dat z předpokladu: spuštění = 10 minut | 10 \* 4 prostředí azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizace výkonu kopírování najdete v tématu [v tomto článku](copy-activity-performance.md) |
| Monitorování kanálu předpokladů: Spustit pouze 1 došlo k chybě | 2 spuštění sledování záznamů opakovat (1 pro spuštění, 1 pro spuštění aktivity kanálu) |

**Celkový počet ceny scénář: $0.16811**

- Data Factory operace = **0,0001 $**
  - R/w = 10\*00001 = $0,0001 [R/W, 1 = $ 0,50/50000 = 0,00001]
  - Monitorování = 2\*000005 = $0,00001 [monitorování 1 = $ 0,25/50000 = 0.000005]
- Orchestrace kanálu &amp; spuštění = **0.168 $**
  - Spuštění aktivit = 001\*2 = 0,002 [1 spuštění = 1/1 000 USD = 0,001]
  - Aktivity přesunu dat = 0.166 (průběžné po dobu 10 minut doby provádění. $0,25 za hodinu v prostředí Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopírování dat a transformace s Azure Databricks každou hodinu

V tomto scénáři budete chtít kopírování dat z AWS S3 do úložiště objektů Blob v Azure a transformovat data pomocí Azure Databricks na hodinový plán.

K provedení scénáře, budete muset vytvořit kanál s následujícími položkami:

1. Aktivita kopírování jednu vstupní datovou sadu pro data, která mají být zkopírovány z AWS S3 a výstupní datovou sadu dat ve službě Azure storage.
2. Pro transformaci dat jednu aktivitu Azure Databricks.
3. Jedna aktivační událost ke spuštění kanálu každou hodinu.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvoření propojené služby | 3 entity čtení/zápisu  |
| Vytvoření datových sad | 4 entity čtení/zápis (2 pro vytvoření datové sady a 2 pro odkazy na propojenou službu) |
| Vytvořit kanál | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získání kanálu | 1 pro čtení a zápis entity |
| Spuštění kanálu | 3 spuštění aktivit (1 pro spuštění, 2 pro aktivity spuštění aktivační události) |
| Kopírování dat z předpokladu: spuštění = 10 minut | 10 \* 4 prostředí azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizace výkonu kopírování najdete v tématu [v tomto článku](copy-activity-performance.md) |
| Monitorování kanálu předpokladů: Spustit pouze 1 došlo k chybě | 3 monitorování spouštění záznamy opakovat (1 pro spuštění, 2 pro spuštění aktivity kanálu) |
| Spuštění aktivity Databricks předpokladů: doba provádění = 10 minut | Provádění aktivity externího kanálu 10 minut |

**Celkový počet ceny scénář: $0.16916**

- Data Factory operace = **0.00012 $**
  - R/w = 11\*00001 = $0.00011 [R/W, 1 = $ 0,50/50000 = 0,00001]
  - Monitorování = 3\*000005 = $0,00001 [monitorování 1 = $ 0,25/50000 = 0.000005]
- Orchestrace kanálu &amp; spuštění = **0.16904 $**
  - Spuštění aktivit = 001\*3 = 0,003 [1 spuštění = 1/1 000 USD = 0,001]
  - Aktivity přesunu dat = 0.166 (průběžné po dobu 10 minut doby provádění. $0,25 za hodinu v prostředí Azure Integration Runtime)
  - Externí aktivity kanálu = 0.000041 (průběžné po dobu 10 minut doby provádění. $0.00025 za hodinu v prostředí Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopírování dat a transformace s každou hodinu dynamické parametry

V tomto scénáři budete chtít kopírování dat z AWS S3 do úložiště objektů Blob v Azure a transformace s Azure Databricks (s dynamickými parametry ve skriptu) na hodinový plán.

K provedení scénáře, budete muset vytvořit kanál s následujícími položkami:

1. Jednu aktivitu kopírování se vstupní datovou sadu pro data, která mají být zkopírovány z AWS S3, výstupní datovou sadu dat ve službě Azure storage.
2. Jedna aktivita vyhledávání pro dynamicky předání parametrů do skriptu transformace.
3. Pro transformaci dat jednu aktivitu Azure Databricks.
4. Jedna aktivační událost ke spuštění kanálu každou hodinu.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvoření propojené služby | 3 entity čtení/zápisu  |
| Vytvoření datových sad | 4 entity čtení/zápis (2 pro vytvoření datové sady a 2 pro odkazy na propojenou službu) |
| Vytvořit kanál | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získání kanálu | 1 pro čtení a zápis entity |
| Spuštění kanálu | 4 spuštění aktivit (1 pro spuštění, 3 pro aktivity spuštění aktivační události) |
| Kopírování dat z předpokladu: spuštění = 10 minut | 10 \* 4 prostředí azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizace výkonu kopírování najdete v tématu [v tomto článku](copy-activity-performance.md) |
| Monitorování kanálu předpokladů: Spustit pouze 1 došlo k chybě | 4 monitorování spouštění záznamy opakovat (1 pro spuštění, 3 pro spuštění aktivity kanálu) |
| Aktivita vyhledávání předpokladů spuštění: doba provádění = 1 min. | 1 min provádění aktivity kanálu |
| Spuštění aktivity Databricks předpokladů: doba provádění = 10 minut | 10 minut externích aktivit v kanálu provádění |

**Celkový počet ceny scénář: $0.17020**

- Data Factory operace = **0.00013 $**
  - R/w = 11\*00001 = $0.00011 [R/W, 1 = $ 0,50/50000 = 0,00001]
  - Monitorování = 4\*000005 = $0.00002 [monitorování 1 = $ 0,25/50000 = 0.000005]
- Orchestrace kanálu &amp; spuštění = **0.17007 $**
  - Spuštění aktivit = 001\*4 = 0.004 [1 spuštění = 1/1 000 USD = 0,001]
  - Aktivity přesunu dat = 0.166 (průběžné po dobu 10 minut doby provádění. $0,25 za hodinu v prostředí Azure Integration Runtime)
  - Kanál aktivit = 0.00003 (průběžné 1 minutu doby provádění. 0,002 USD/hod. v prostředí Azure Integration Runtime)
  - Externí aktivity kanálu = 0.000041 (průběžné po dobu 10 minut doby provádění. $0.00025 za hodinu v prostředí Azure Integration Runtime)

## <a name="next-steps"></a>Další postup

Teď, když chápete, ceny za Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Úvod do Azure Data Factory](introduction.md)

- [Vizuální vytváření obsahu v Azure Data Factory](author-visually.md)