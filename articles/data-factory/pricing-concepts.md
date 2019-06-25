---
title: Vysvětlení, příklady ceny služby Azure Data Factory | Dokumentace Microsoftu
description: Tento článek popisuje a předvádí cenový model s podrobné příklady služby Azure Data Factory
documentationcenter: ''
author: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: a825982532047f6e311c5508394df243310f02ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233919"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Vysvětlení, příklady ceny služby Data Factory

Tento článek popisuje a předvádí cenový model s podrobné příklady služby Azure Data Factory.

> [!NOTE]
> V těchto příkladech níže uvedené ceny jsou hypotetické a nejsou určeny k implikují skutečné ceny.

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
| Vytvoření kanálu | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
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
| Vytvoření kanálu | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
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
| Vytvoření kanálu | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
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

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Použití mapování ladění toku dat pro běžné pracovní doby (ceny verze Preview)

Jako datových inženýrů zodpovídáte za navrhování, sestavování a testování mapování toků dat každý den. Přihlaste se do uživatelského rozhraní ADF ráno a povolit režim ladění pro Data proudí. Výchozí hodnota TTL pro relace ladění je 60 minut. Pracujete v průběhu dne 10 hodin, takže nikdy vyprší platnost vaší relace ladění. Proto bude váš poplatek za den:

**10 (hodiny) x 8 (jádra) x 0.112 $ $8.96 =**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Transformujte data v úložišti objektů blob s mapováním datové toky (ceny verze Preview)

V tomto scénáři budete chtít transformace dat v Blob Store vizuálně v ADF mapování dat toky na hodinový plán.

K provedení scénáře, budete muset vytvořit kanál s následujícími položkami:

1. Tok dat aktivitu s logikou transformace.

2. Vstupní datová sada pro data ve službě Azure Storage.

3. Výstupní datovou sadu dat ve službě Azure Storage.

4. Aktivační událost plánovače ke spuštění kanálu každou hodinu.

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvoření propojené služby | 2 r/w entity  |
| Vytvoření datových sad | 4 entity čtení/zápis (2 pro vytvoření datové sady a 2 pro odkazy na propojenou službu) |
| Vytvoření kanálu | 3 entity čtení/zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získání kanálu | 1 pro čtení a zápis entity |
| Spuštění kanálu | 2 spuštění aktivit (1 pro spuštění, 1 pro aktivity spuštění aktivační události) |
| Doba provádění předpoklady toku dat: = 10 min + 10 min. hodnota TTL | 10 \* 8jádrový obecné výpočetní s TTL 10 |
| Monitorování kanálu předpokladů: Spustit pouze 1 došlo k chybě | 2 spuštění sledování záznamů opakovat (1 pro spuštění, 1 pro spuštění aktivity kanálu) |

**Celkový počet ceny scénář: $0.3011**

- Data Factory operace = **0,0001 $**
  - R/w = 10\*00001 = $0,0001 [R/W, 1 = $ 0,50/50000 = 0,00001]
  - Monitorování = 2\*000005 = $0,00001 [monitorování 1 = $ 0,25/50000 = 0.000005]
- Orchestrace kanálu &amp; spuštění = **0.301 $**
  - Spuštění aktivit = 001\*2 = 0,002 [1 spuštění = 1/1 000 USD = 0,001]
  - Aktivity toku dat = $0.299 průběžné 20 minut (10 minut doby provádění + 10 minut hodnotu TTL). compute 0.112 $za hodinu v prostředí Azure Integration Runtime s 8 jádry, obecné

## <a name="next-steps"></a>Další postup

Teď, když chápete, ceny za Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Úvod do Azure Data Factory](introduction.md)

- [Vizuální vytváření obsahu v Azure Data Factory](author-visually.md)
