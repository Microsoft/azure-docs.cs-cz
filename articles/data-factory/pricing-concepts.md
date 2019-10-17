---
title: Seznámení se Azure Data Factory cenami prostřednictvím příkladů | Microsoft Docs
description: Tento článek vysvětluje a předvádí Azure Data Factory cenový model s podrobnými příklady
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: 168d977b9dc0ea6117796cf98a8562f168258d28
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387459"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Seznámení se Data Factory cenami prostřednictvím příkladů

Tento článek vysvětluje a předvádí Azure Data Factory cenový model s podrobnými příklady.

> [!NOTE]
> Ceny použité v těchto příkladech jsou hypotetické a nejsou určené k implikuje skutečné ceny.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopírování dat z AWS S3 do úložiště objektů BLOB v Azure za hodinu

V tomto scénáři budete chtít kopírovat data z AWS S3 do úložiště objektů BLOB v Azure s hodinovým plánem.

Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit kanál s následujícími položkami:

1. Aktivita kopírování se vstupní datovou sadou pro data, která se mají zkopírovat z AWS S3.

2. Výstupní datová sada pro data v Azure Storage.

3. Aktivační událost plánovače, která každou hodinu spustí kanál.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 2 spuštění aktivit (1 pro spuštění triggeru, 1 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 pokus o sledování záznamů o spuštění (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Ceny za celkový scénář: $0,16811**

- Operace Data Factory = **$0,0001**
  - Čtení/zápis = 10 @ no__t-000001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 2 @ no__t-0000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Orchestrace kanálu &amp; spuštění = **$0,168**
  - Spuštění aktivit = 001 @ no__t-02 = 0,002 [1 spuštění = $1/1000 = 0,001]
  - Aktivity přesunu dat = $0,166 (poměr po 10 minutách doby spuštění. měsíčně za hodinu v Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopírování dat a transformace pomocí Azure Databricks každou hodinu

V tomto scénáři budete chtít kopírovat data z AWS S3 do úložiště objektů BLOB v Azure a transformovat data pomocí Azure Databricks podle hodinového plánu.

Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit kanál s následujícími položkami:

1. Jedna aktivita kopírování se vstupní datovou sadou pro data, která se mají zkopírovat z AWS S3, a výstupní datovou sadu pro data v úložišti Azure.
2. Jedna aktivita Azure Databricks pro transformaci dat.
3. Jedna aktivační událost plánovače každou hodinu spustí kanál.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 3 spuštění aktivit (1 pro spuštění triggeru, 2 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 3 záznamy o spuštění sledování se opakovaly (1 pro spuštění kanálu, 2 pro spuštění aktivit). |
| Předpoklad aktivity datacihly: doba provádění = 10 min | 10 minut provádění externí aktivity kanálu |

**Ceny za celkový scénář: $0,16916**

- Operace Data Factory = **$0,00012**
  - Čtení/zápis = 11 @ no__t-000001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 3 @ no__t-0000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Orchestrace kanálu &amp; spuštění = **$0,16904**
  - Spuštění aktivit = 001 @ no__t-03 = 0,003 [1 spuštění = $1/1000 = 0,001]
  - Aktivity přesunu dat = $0,166 (poměr po 10 minutách doby spuštění. měsíčně za hodinu v Azure Integration Runtime)
  - Aktivita externího kanálu = $0,000041 (poměr po 10 minutách doby spuštění. $0.00025 za hodinu v Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopírovat data a transformovat s dynamickými parametry každou hodinu

V tomto scénáři budete chtít kopírovat data z AWS S3 do Azure Blob Storage a transformovat je pomocí Azure Databricks (s dynamickými parametry ve skriptu) podle hodinového plánu.

Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit kanál s následujícími položkami:

1. Jedna aktivita kopírování se vstupní datovou sadou pro data, která se mají zkopírovat z AWS S3, výstupní datová sada pro data v úložišti Azure.
2. Jedna aktivita vyhledávání pro dynamické předávání parametrů do transformačního skriptu.
3. Jedna aktivita Azure Databricks pro transformaci dat.
4. Jedna aktivační událost plánovače každou hodinu spustí kanál.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 4 spuštění aktivit (1 pro spuštění triggeru, 3 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 4 pokus o sledování záznamů o spuštění (1 pro běh kanálu, 3 pro spuštění aktivit) |
| Spustit předpoklad aktivity vyhledávání: doba provádění = 1 min. | 1 minimální spuštění aktivity kanálu |
| Předpoklad aktivity datacihly: doba provádění = 10 min | 10 minut provádění externí aktivity kanálu |

**Ceny za celkový scénář: $0,17020**

- Operace Data Factory = **$0,00013**
  - Čtení/zápis = 11 @ no__t-000001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 4 @ no__t-0000005 = $0,00002 [1 monitorování = $0,25/50000 = 0,000005]
- Orchestrace kanálu &amp; spuštění = **$0,17007**
  - Spuštění aktivit = 001 @ no__t-04 = 0,004 [1 spuštění = $1/1000 = 0,001]
  - Aktivity přesunu dat = $0,166 (poměr po 10 minutách doby spuštění. měsíčně za hodinu v Azure Integration Runtime)
  - Aktivita kanálu = $0,00003 (poměr 1 minuty doby spuštění. $0.002 za hodinu v Azure Integration Runtime)
  - Aktivita externího kanálu = $0,000041 (poměr po 10 minutách doby spuštění. $0.00025 za hodinu v Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Použití mapování toku dat pro normální pracovní den (ceny verze Preview)

Jako inženýr dat zodpovídáte za návrh, sestavování a testování toků dat mapování každý den. Přihlásíte se k uživatelskému rozhraní ADF v ráno a povolíte režim ladění pro toky dat. Výchozí hodnota TTL pro relace ladění je 60 minut. Po dobu 10 hodin budete pracovat v průběhu dne, takže relace ladění nikdy nevyprší. Proto poplatek za den bude:

**10 (hodiny) × 8 (jádra) × $0,112 = $8,96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Transformace dat v úložišti objektů BLOB s využitím toků mapování dat (verze Preview)

V tomto scénáři chcete transformovat data v úložišti objektů BLOB vizuálně v toku dat mapování ADF na hodinový plán.

Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit kanál s následujícími položkami:

1. Aktivita toku dat s logikou transformace

2. Vstupní datová sada pro data v Azure Storage.

3. Výstupní datová sada pro data v Azure Storage.

4. Aktivační událost plánovače, která každou hodinu spustí kanál.

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 2 spuštění aktivit (1 pro spuštění triggeru, 1 pro spuštění aktivit) |
| Předpoklady toku dat: doba provádění = 10 min + 10 min. TTL | 10 \* 8 jader obecného výpočtu s hodnotou TTL z 10 |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 pokus o sledování záznamů o spuštění (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Ceny za celkový scénář: $0,3011**

- Operace Data Factory = **$0,0001**
  - Čtení/zápis = 10 @ no__t-000001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 2 @ no__t-0000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Orchestrace kanálu &amp; spuštění = **$0,301**
  - Spuštění aktivit = 001 @ no__t-02 = 0,002 [1 spuštění = $1/1000 = 0,001]
  - Aktivity toku dat = $0,299 poměrná hodnota po dobu 20 minut (10 minut doba provádění + 10 minut TTL). $0.112 za hodinu na Azure Integration Runtime s 8 jádry obecného COMPUTE

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s cenami pro Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)

- [Seznámení se službou Azure Data Factory](introduction.md)

- [Vytváření vizuálů v Azure Data Factory](author-visually.md)
