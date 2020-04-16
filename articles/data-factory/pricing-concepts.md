---
title: Principy cen Azure Data Factory prostřednictvím příkladů
description: Tento článek vysvětluje a ukazuje cenový model Azure Data Factory s podrobnými příklady
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414946"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Principy cen z datové továrny prostřednictvím příkladů

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek vysvětluje a ukazuje cenový model Azure Data Factory s podrobnými příklady.

> [!NOTE]
> Ceny použité v těchto níže uvedených příkladech jsou hypotetické a nejsou určeny k tomu, aby naznačovaly skutečné ceny.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopírování dat z AWS S3 do úložiště objektů blob Azure každou hodinu

V tomto scénáři chcete zkopírovat data z AWS S3 do úložiště objektů blob Azure podle hodinového plánu.

Chcete-li provést scénář, je třeba vytvořit kanál s následujícími položkami:

1. Aktivita kopírování se vstupní datovou sadou pro data, která mají být zkopírována z AWS S3.

2. Výstupní datová sada pro data ve službě Azure Storage.

3. Aktivační událost plánu pro spuštění kanálu každou hodinu.

   ![Scénář1](media/pricing-concepts/scenario1.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 Entita pro čtení a zápis  |
| Vytvořit datové sady | 4 Entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazy na propojené služby) |
| Vytvořit kanál | 3 Entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získat kanál | 1 Entita pro čtení a zápis |
| Spustit kanál | 2 Spuštění aktivity (1 pro spuštění aktivační události, 1 pro spuštění aktivity) |
| Kopírovat data Předpoklad: doba spuštění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIU = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování naleznete v [tomto článku](copy-activity-performance.md) |
| Předpoklad kanálu monitorování: Došlo pouze k 1 spuštění | 2 Sledování spuštění záznamů opakování (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Ceny celkového scénáře: $0.16811**

- Operace datové továrny = **$0.0001**
  - Čtení a zápis\*= 10 00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorování =\*2 000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- Spuštění orchestrace kanálu &amp; = **$0.168**
  - Spuštění aktivity =\*001 2 = 0,002 [1 běh = $1/1000 = 0.001]
  - Aktivity přesunu dat = $0.166 (poměrně 10 minut spuštění. $0.25/hour v době spuštění integrace Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopírování dat a transformace pomocí Azure Databricks každou hodinu

V tomto scénáři chcete zkopírovat data z AWS S3 do úložiště objektů blob Azure a transformovat data pomocí Azure Databricks podle hodinového plánu.

Chcete-li provést scénář, je třeba vytvořit kanál s následujícími položkami:

1. Jedna aktivita kopírování se vstupní datovou sadou pro data, která se mají kopírovat z AWS S3, a výstupní datovou sadou pro data v úložišti Azure.
2. Jedna aktivita Azure Databricks pro transformaci dat.
3. Jeden plán aktivační událost pro spuštění kanálu každou hodinu.

![Scénář2](media/pricing-concepts/scenario2.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 Entita pro čtení a zápis  |
| Vytvořit datové sady | 4 Entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazy na propojené služby) |
| Vytvořit kanál | 3 Entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získat kanál | 1 Entita pro čtení a zápis |
| Spustit kanál | 3 Spuštění aktivity (1 pro spuštění aktivační události, 2 pro spuštění aktivity) |
| Kopírovat data Předpoklad: doba spuštění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIU = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování naleznete v [tomto článku](copy-activity-performance.md) |
| Předpoklad kanálu monitorování: Došlo pouze k 1 spuštění | 3 Sledování spuštění záznamů opakování (1 pro spuštění kanálu, 2 pro spuštění aktivity) |
| Spustit aktivitu Databricks Předpoklad: doba spuštění = 10 min | 10 min externí potrubí provádění aktivity |

**Ceny celkového scénáře: $0.16916**

- Operace datové továrny = **$0.00012**
  - Čtení/zápis =\*11 00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorování =\*3 000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- Spuštění orchestrace kanálu &amp; = **$0.16904**
  - Spuštění aktivity =\*001 3 = 0,003 [1 běh = $1/1000 = 0.001]
  - Aktivity přesunu dat = $0.166 (poměrně 10 minut spuštění. $0.25/hour v době spuštění integrace Azure)
  - Externí aktivita kanálu = $0.000041 (poměrně 10 minut spuštění. $0.00025/hod v době spuštění integrace Azure)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopírování dat a transformace s dynamickými parametry každou hodinu

V tomto scénáři chcete zkopírovat data z AWS S3 do úložiště objektů blob Azure a transformovat s Azure Databricks (s dynamickými parametry ve skriptu) na hodinovém plánu.

Chcete-li provést scénář, je třeba vytvořit kanál s následujícími položkami:

1. Jedna aktivita kopírování se vstupní datovou sadou pro data, která se mají kopírovat z AWS S3, výstupní datové sady pro data v úložišti Azure.
2. Jedna vyhledávací aktivita pro dynamické předávání parametrů do transformačního skriptu.
3. Jedna aktivita Azure Databricks pro transformaci dat.
4. Jeden plán aktivační událost pro spuštění kanálu každou hodinu.

![Scénář3](media/pricing-concepts/scenario3.png)

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 3 Entita pro čtení a zápis  |
| Vytvořit datové sady | 4 Entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazy na propojené služby) |
| Vytvořit kanál | 3 Entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získat kanál | 1 Entita pro čtení a zápis |
| Spustit kanál | 4 Spuštění aktivity (1 pro spuštění aktivační události, 3 pro spuštění aktivity) |
| Kopírovat data Předpoklad: doba spuštění = 10 min | 10 \* 4 Azure Integration Runtime (výchozí nastavení DIU = 4) Další informace o jednotkách integrace dat a optimalizaci výkonu kopírování naleznete v [tomto článku](copy-activity-performance.md) |
| Předpoklad kanálu monitorování: Došlo pouze k 1 spuštění | 4 Sledování spuštění záznamů opakování (1 pro spuštění kanálu, 3 pro spuštění aktivity) |
| Spustit vyhledávací aktivitu Předpoklad: doba spuštění = 1 min | 1 min Spuštění aktivity potrubí |
| Spustit aktivitu Databricks Předpoklad: doba spuštění = 10 min | 10 min externí potrubí činnost provádění |

**Ceny celkového scénáře: $0.17020**

- Operace datové továrny = **$0.00013**
  - Čtení/zápis =\*11 00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorování =\*4 000005 = $0.00002 [1 Monitoring = $0.25/50000 = 0.000005]
- Spuštění orchestrace kanálu &amp; = **$0.17007**
  - Spuštění aktivity =\*001 4 = 0,004 [1 běh = $1/1000 = 0.001]
  - Aktivity přesunu dat = $0.166 (poměrně 10 minut spuštění. $0.25/hour v době spuštění integrace Azure)
  - Aktivita kanálu = $0.00003 (poměrně pro 1 minutu provádění. $0.002/hod v době spuštění integrace Azure)
  - Externí aktivita kanálu = $0.000041 (poměrně 10 minut spuštění. $0.00025/hod v době spuštění integrace Azure)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Použití ladění toku dat mapování pro normální pracovní den

Jako datový inženýr jste zodpovědní za navrhování, vytváření a testování toků mapování dat každý den. Přihlášení do usvého uj.a. Výchozí hodnota TTL pro relace ladění je 60 minut. Pracujete po celý den po dobu 8 hodin, takže vaše relace ladění nikdy nevyprší. Proto bude váš poplatek za den bude:

**8 (hodiny) x 8 (výpočetní optimalizovaná jádra) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformace dat v úložišti objektů blob s mapováním toků dat

V tomto scénáři chcete transformovat data v úložišti objektů blob vizuálně v toku dat mapování ADF na hodinovém plánu.

Chcete-li provést scénář, je třeba vytvořit kanál s následujícími položkami:

1. Aktivita toku dat s logikou transformace.

2. Vstupní datová sada pro data ve službě Azure Storage.

3. Výstupní datová sada pro data ve službě Azure Storage.

4. Aktivační událost plánu pro spuštění kanálu každou hodinu.

| **Provoz** | **Typy a jednotky** |
| --- | --- |
| Vytvořit propojenou službu | 2 Entita pro čtení a zápis  |
| Vytvořit datové sady | 4 Entity pro čtení a zápis (2 pro vytvoření datové sady, 2 pro odkazy na propojené služby) |
| Vytvořit kanál | 3 Entity pro čtení a zápis (1 pro vytvoření kanálu, 2 pro odkazy na datové sady) |
| Získat kanál | 1 Entita pro čtení a zápis |
| Spustit kanál | 2 Spuštění aktivity (1 pro spuštění aktivační události, 1 pro spuštění aktivity) |
| Předpoklady toku dat: doba provádění = 10 min + 10 min TTL | 10 \* 16 jader general compute u TTL 10 |
| Předpoklad kanálu monitorování: Došlo pouze k 1 spuštění | 2 Sledování spuštění záznamů opakování (1 pro spuštění kanálu, 1 pro spuštění aktivity) |

**Celkové ceny scénářů: $1.4631**

- Operace datové továrny = **$0.0001**
  - Čtení a zápis\*= 10 00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorování =\*2 000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- Spuštění orchestrace kanálu &amp; = **$1.463**
  - Spuštění aktivity =\*001 2 = 0,002 [1 běh = $1/1000 = 0.001]
  - Aktivity toku dat = $1.461 poměrně 20 minut (10 minut doba spuštění + 10 minut TTL). $0.274/hour v prostředí Azure Integration Runtime s obecným výpočetním výkonem 16 jader

## <a name="next-steps"></a>Další kroky

Teď, když rozumíte cenám pro Azure Data Factory, můžete začít!

- [Vytvoření datové továrny pomocí uznatého nastavení Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Seznámení se službou Azure Data Factory](introduction.md)

- [Vizuální vytváření ve Službě Azure Data Factory](author-visually.md)
