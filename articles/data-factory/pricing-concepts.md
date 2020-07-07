---
title: Seznámení se Azure Data Factory cenami prostřednictvím příkladů
description: Tento článek vysvětluje a předvádí Azure Data Factory cenový model s podrobnými příklady
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414946"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Seznámení s cenami služby Data Factory prostřednictvím příkladů

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 pokus o sledování záznamů o spuštění (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Ceny za celkový scénář: $0,16811**

- Operace Data Factory = **$0,0001**
  - Čtení/zápis = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 2 \* 000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Spuštění orchestrace kanálu &amp; = **$0,168**
  - Spuštění aktivit = 001 \* 2 = 0,002 [1 Run = $1/1000 = 0,001]
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
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 3 záznamy o spuštění sledování se opakovaly (1 pro spuštění kanálu, 2 pro spuštění aktivit). |
| Předpoklad aktivity datacihly: doba provádění = 10 min | 10 minut provádění externí aktivity kanálu |

**Ceny za celkový scénář: $0,16916**

- Operace Data Factory = **$0,00012**
  - Čtení/zápis = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 3 \* 000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Spuštění orchestrace kanálu &amp; = **$0,16904**
  - Spuštění aktivit = 001 \* 3 = 0,003 [1 Run = $1/1000 = 0,001]
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
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 4 pokus o sledování záznamů o spuštění (1 pro běh kanálu, 3 pro spuštění aktivit) |
| Spustit předpoklad aktivity vyhledávání: doba provádění = 1 min. | 1 minimální spuštění aktivity kanálu |
| Předpoklad aktivity datacihly: doba provádění = 10 min | 10 minut provádění externí aktivity kanálu |

**Ceny za celkový scénář: $0,17020**

- Operace Data Factory = **$0,00013**
  - Čtení/zápis = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 4 \* 000005 = $0,00002 [1 monitorování = $0,25/50000 = 0,000005]
- Spuštění orchestrace kanálu &amp; = **$0,17007**
  - Spuštění aktivit = 001 \* 4 = 0,004 [1 běh = $1/1000 = 0,001]
  - Aktivity přesunu dat = $0,166 (poměr po 10 minutách doby spuštění. měsíčně za hodinu v Azure Integration Runtime)
  - Aktivita kanálu = $0,00003 (poměr 1 minuty doby spuštění. $0.002 za hodinu v Azure Integration Runtime)
  - Aktivita externího kanálu = $0,000041 (poměr po 10 minutách doby spuštění. $0.00025 za hodinu v Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Použití mapování toku dat pro normální pracovní den

Jako inženýr dat zodpovídáte za návrh, sestavování a testování toků dat mapování každý den. Přihlásíte se k uživatelskému rozhraní ADF v ráno a povolíte režim ladění pro toky dat. Výchozí hodnota TTL pro relace ladění je 60 minut. Po dobu 8 hodin budete pracovat v průběhu dne, takže relace ladění nikdy nevyprší. Proto poplatek za den bude:

**8 (hodiny) × 8 (jader optimalizované pro výpočty) × $0,193 = $12,35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformace dat v úložišti objektů BLOB s využitím toků mapování dat

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
| Předpoklady toku dat: doba provádění = 10 min + 10 min. TTL | 10 až 10 \* jader obecného výpočetních prostředků s hodnotou TTL z 10 |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 pokus o sledování záznamů o spuštění (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Ceny za celkový scénář: $1,4631**

- Operace Data Factory = **$0,0001**
  - Čtení/zápis = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 2 \* 000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Spuštění orchestrace kanálu &amp; = **$1,463**
  - Spuštění aktivit = 001 \* 2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Aktivity toku dat = $1,461 poměrná hodnota po dobu 20 minut (10 minut doba provádění + 10 minut TTL). $0.274 za hodinu v Azure Integration Runtime se základními výpočetními jádry 16 jader

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s cenami pro Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)

- [Úvod do služby Azure Data Factory](introduction.md)

- [Vytváření vizuálů v Azure Data Factory](author-visually.md)
