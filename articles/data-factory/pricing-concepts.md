---
title: Seznámení se Azure Data Factory cenami prostřednictvím příkladů
description: Tento článek vysvětluje a předvádí Azure Data Factory cenový model s podrobnými příklady
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: b9f163a7632ca59d4f97aef21d8d62157610ba73
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372803"
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

   ![Diagram znázorňuje kanál s triggerem plánovače. V kanálu aktivity kopírování přechází do vstupní datové sady, která přechází do propojené služby a W S S3 propojená služba a aktivita kopírování také vede do výstupní datové sady, která se přetéká na Azure Storage propojenou službu.](media/pricing-concepts/scenario1.png)

| **Operace** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 2 spuštění aktivit (1 pro spuštění triggeru, 1 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 načetly se záznamy spuštění monitorování (1 pro spuštění kanálu, 1 pro spuštění aktivit). |

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

![Diagram znázorňuje kanál s triggerem plánovače. V kanálu aktivity kopírování toků do vstupní datové sady, výstupní datové sady a aktivity datacihly, které běží na Azure Databricks. Vstupní datová sada vede k propojeným službám A W S S3. Výstupní datová sada vede toky do propojené služby Azure Storage.](media/pricing-concepts/scenario2.png)

| **Operace** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 3 spuštění aktivit (1 pro spuštění triggeru, 2 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 3 načtené záznamy běhu monitorování (1 pro běh kanálu, 2 pro spuštění aktivit) |
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

![Diagram znázorňuje kanál s triggerem plánovače. V kanálu aktivity kopírování natéká do vstupní datové sady, výstupní datové sady a aktivity vyhledávání, které se přetokůují do aktivity datacihly, která běží na Azure Databricks. Vstupní datová sada vede k propojeným službám A W S S3. Výstupní datová sada vede toky do propojené služby Azure Storage.](media/pricing-concepts/scenario3.png)

| **Operace** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 4 spuštění aktivit (1 pro spuštění triggeru, 3 pro spuštění aktivit) |
| Předpoklad Kopírování dat: doba provádění = 10 min | 10 \* 4 Azure Integration runtime (výchozí nastavení diú = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 4 načtené záznamy běhu monitorování (1 pro běh kanálu, 3 pro spuštění aktivit) |
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

Jako inženýr dat zodpovídá Sam za návrh, sestavování a testování toků dat mapování každý den. Protokol Sam se v ráno přihlásí do uživatelského rozhraní ADF a povolí režim ladění pro toky dat. Výchozí hodnota TTL pro relace ladění je 60 minut. Sam funguje během dne po dobu 8 hodin, aby relace ladění nikdy nevypršela. Proto budou poplatky za tento den v Sam:

**8 (hodiny) × 8 (jader optimalizované pro výpočty) × $0,193 = $12,35**

Zároveň se k uživatelskému rozhraní prohlížeče ADF pro profilaci dat a práci s návrhem ETL přihlásí zároveň pracovníka s jiným datovým inženýrem. Chris nefunguje v ADF celý den jako Sam. Pracovník Novák potřebuje použít ladicí program toku dat 1 hodinu během stejné doby a stejný den jako Sam výše. Tyto poplatky se účtují za použití ladění:

**1 (hodina) × 8 (jádra pro obecné účely) × $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformace dat v úložišti objektů BLOB s využitím toků mapování dat

V tomto scénáři chcete transformovat data v úložišti objektů BLOB vizuálně v toku dat mapování ADF na hodinový plán.

Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit kanál s následujícími položkami:

1. Aktivita toku dat s logikou transformace

2. Vstupní datová sada pro data v Azure Storage.

3. Výstupní datová sada pro data v Azure Storage.

4. Aktivační událost plánovače, která každou hodinu spustí kanál.

| **Operace** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 entita pro čtení a zápis  |
| Vytvoření datových sad | 4 entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazované odkazy na službu) |
| Vytvořit kanál | 3 entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datovou sadu) |
| Získat kanál | 1 entita pro čtení a zápis |
| Spustit kanál | 2 spuštění aktivit (1 pro spuštění triggeru, 1 pro spuštění aktivit) |
| Předpoklady toku dat: doba provádění = 10 min + 10 min. TTL | 10 až 10 \* jader obecného výpočetních prostředků s hodnotou TTL z 10 |
| Předpoklad monitorování kanálu: došlo k pouze 1 spuštění. | 2 načetly se záznamy spuštění monitorování (1 pro spuštění kanálu, 1 pro spuštění aktivit). |

**Ceny za celkový scénář: $1,4631**

- Operace Data Factory = **$0,0001**
  - Čtení/zápis = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 2 \* 000005 = $0,00001 [1 monitorování = $0,25/50000 = 0,000005]
- Spuštění orchestrace kanálu &amp; = **$1,463**
  - Spuštění aktivit = 001 \* 2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Aktivity toku dat = $1,461 poměrná hodnota po dobu 20 minut (10 minut doba provádění + 10 minut TTL). $0.274 za hodinu v Azure Integration Runtime se základními výpočetními jádry 16 jader

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Integrace dat ve Azure Data Factory spravovaná virtuální síť
V tomto scénáři chcete odstranit původní soubory na Azure Blob Storage a kopírovat data z Azure SQL Database do Azure Blob Storage. Toto spuštění provedete dvakrát na různých kanálech. Doba provádění těchto dvou kanálů se překrývá.
![Scenario4 ](media/pricing-concepts/scenario-4.png) Chcete-li dosáhnout tohoto scénáře, je nutné vytvořit dva kanály s následujícími položkami:
  - Aktivita kanálu – odstranit aktivitu.
  - Aktivita kopírování se vstupní datovou sadou pro data, která se mají zkopírovat z úložiště objektů BLOB v Azure
  - Výstupní datová sada pro data v Azure SQL Database.
  - Časový Trigger pro spuštění kanálu.


| **Operace** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 4 entita pro čtení a zápis |
| Vytvoření datových sad | 8 entit pro čtení a zápis (4 pro vytvoření datové sady, 4 pro odkazy na propojené služby) |
| Vytvořit kanál | 6 entit pro čtení/zápis (2 pro vytvoření kanálu, 4 pro odkazy na datovou sadu) |
| Získat kanál | 2 entita pro čtení a zápis |
| Spustit kanál | 6 spuštění aktivit (2 pro spuštění triggeru, 4 pro spuštění aktivit) |
| Provést odstranění aktivity: každou dobu běhu = 5 min. Spuštění aktivity odstranit v prvním kanálu je od 10:00 do 10:05 UTC. Provedení aktivity odstranit v druhém kanálu je od 10:02 do 10:07 UTC.|Celkem 7 minut spuštění aktivity kanálu ve spravované virtuální síti Aktivita kanálu podporuje ve spravované virtuální síti až 50 souběžnosti. |
| Předpoklad Kopírování dat: každá doba provádění = 10 min. Provedení kopírování v prvním kanálu je od 10:06 do 10:15 UTC. Provedení aktivity odstranit v druhém kanálu je od 10:08 do 10:17 UTC. | 10 * 4 Azure Integration Runtime (výchozí nastavení DIÚ = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování najdete v [tomto článku](copy-activity-performance.md) . |
| Předpokládá se sledování kanálu: narazilo se jenom na 2 běhy. | 6 načtených záznamů sledovacích běhů (2 pro běh kanálu, 4 pro spuštění aktivit) |


**Ceny za celkový scénář: $0,45523**

- Operace Data Factory = $0,00023
  - Čtení/zápis = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorování = 6 * 000005 = $0,00003 [1 monitorování = $0,25/50000 = 0,000005]
- Orchestrace kanálu & provádění = $0,455
  - Spuštění aktivit = 0,001 * 6 = 0,006 [1 Run = $1/1000 = 0,001]
  - Aktivity přesunu dat = $0,333 (poměr po 10 minutách doby spuštění. měsíčně za hodinu v Azure Integration Runtime)
  - Aktivita kanálu = $0,116 (poměr po 7 minutách doby spuštění. za hodinu v Azure Integration Runtime. 1/hod.)

> [!NOTE]
> Tyto ceny slouží pouze jako příklad pro účely.

**Nejčastější dotazy**

Otázka: Pokud chci spustit více než 50 aktivit kanálu, mohou být tyto aktivity spouštěny současně?

Odpověď: maximální 50 souběžných aktivit kanálu bude povolen.  Aktivita kanálu 51th se zařadí do fronty, dokud se neotevře "volné místo". Stejné pro externí aktivitu. Maximální počet 800 souběžných externích aktivit bude povolen.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s cenami pro Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)

- [Úvod do služby Azure Data Factory](introduction.md)

- [Vytváření vizuálů v Azure Data Factory](author-visually.md)
