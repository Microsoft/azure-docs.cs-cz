---
title: Co je Apache Spark
description: Tento článek poskytuje Úvod do Apache Spark ve službě Azure synapse Analytics a v různých scénářích, ve kterých můžete použít Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450433"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark v Azure Synapse Analytics

Apache Spark je paralelní procesor pro zpracování, který podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Apache Spark ve službě Azure synapse Analytics je jedním z implementací Apache Spark v cloudu od Microsoftu. Azure synapse umožňuje snadno vytvořit a nakonfigurovat fond Apache Spark bez serveru v Azure. Fondy Spark ve službě Azure synapse jsou kompatibilní s úložištěm Azure Storage a Azure Data Lake generace 2. Takže můžete použít fondy Spark ke zpracování dat uložených v Azure.

![Spark: jednotné rozhraní](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>Co je Apache Spark

Apache Spark poskytuje primitivní prvky pro výpočetní clustery v paměti. Úloha Sparku dokáže data načíst a uložit je do mezipaměti a opakovaně se na ně dotazovat. Výpočty v paměti jsou mnohem rychlejší než aplikace založené na disku. Spark se taky integruje s několika programovacími jazyky, které vám umožní manipulovat s distribuovanými datovými sadami, jako jsou místní kolekce. Není nutné strukturovat všechno jako mapovací a redukční operace.

![Tradiční MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Fondy Spark v Azure synapse nabízejí plně spravovanou službu Spark. Tady jsou uvedené výhody vytvoření fondu Spark ve službě Azure synapse Analytics.

| Funkce | Popis |
| --- | --- |
| Rychlost a efektivita |Instance Spark začínají přibližně 2 minuty pro méně než 60 uzlů a přibližně 5 minut pro více než 60 uzlů. Instance se ukončí, ve výchozím nastavení 5 minut po poslední provedené úloze, pokud není udržována připojením poznámkového bloku. |
| Snadné vytvoření |Během několika minut můžete vytvořit nový fond Spark v Azure synapse pomocí Azure Portal, Azure PowerShell nebo sady synapse Analytics .NET SDK. Přečtěte si téma Začínáme [se fondy Spark ve službě Azure synapse Analytics](../quickstart-create-apache-spark-pool-studio.md). |
| Jednoduché používání |Synapse Analytics zahrnuje vlastní Poznámkový blok odvozený z [Nteract](https://nteract.io/). Tyto poznámkové bloky můžete použít pro interaktivní zpracování dat a vizualizaci.|
| Rozhraní REST API |Spark ve službě Azure synapse Analytics zahrnuje [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), Server úloh Spark založený na REST API pro vzdálené odesílání a monitorování úloh. |
| Podpora pro Azure Data Lake Storage generace 2| Fondy Spark ve službě Azure synapse můžou používat Azure Data Lake Storage generace 2 i úložiště objektů BLOB. Další informace o Data Lake Storage najdete v tématu [přehled Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrace v prostředí IDE třetích stran | Azure synapse poskytuje modul plug-in IDE pro [INTELLIJ nápad JetBrains](https://www.jetbrains.com/idea/) , který je užitečný pro vytváření a odesílání aplikací do fondu Spark. |
| Předem zavedené knihovny Anaconda |Fondy Spark ve službě Azure synapse se dodávají s předinstalovanými knihovnami Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) poskytuje téměř 200 knihoven pro machine learning, analýzy dat, vizualizace atd. |
| Škálovatelnost | Apache Spark ve fondech Azure synapse můžou mít povolené automatické škálování, takže fondy se podle potřeby přidávají nebo odstraňují podle potřeby uzlů. Fondy Sparku se také dají vypnout bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage. |

Fondy Spark v Azure synapse obsahují následující komponenty, které jsou ve výchozím nastavení k dispozici ve fondech.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Obsahuje Spark Core, Spark SQL, GraphX a MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Poznámkový blok Nteract](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architektura pro fond Spark

Je snadné pochopit komponenty Sparku tím, že pochopíte, jak Spark běží na Azure synapse Analytics.

Aplikace Spark běží jako nezávislé sady procesů ve fondu, které jsou koordinovány objektem SparkContext v hlavním programu (nazývaný program ovladače).

SparkContext se může připojit ke Správci clusteru, který přiděluje prostředky napříč aplikacemi. Správce clusteru je [Apache HADOOP příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Po připojení Spark získá vykonavatele na uzlech ve fondu, což jsou procesy, které spouštějí výpočty a ukládají data pro vaši aplikaci. Pak do exekutorů odešle kód vaší aplikace (definovaný v souborech JAR nebo Pythonu předaných do objektu SparkContext). Nakonec SparkContext odešle do exekutorů úlohy ke spuštění.

SparkContext spustí hlavní funkci uživatele a provede různé paralelní operace na uzlech. Potom SparkContext shromažďuje výsledky těchto operací. Uzly čtou a zapisují data z a do systému souborů. Uzly také v mezipaměti transformují data v paměti jako odolné distribuované datové sady (RDD).

SparkContext se připojuje ke fondu Spark a zodpovídá za převod aplikace do orientovaného grafu acyklického (DAG). Graf se skládá z jednotlivých úloh, které se spustí v rámci procesu prováděče na uzlech. Každá aplikace získá vlastní procesy exekutora, které zůstávají v provozu po dobu trvání celé aplikace a spouští úlohy ve více vláknech.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Apache Spark ve službě Azure synapse Analytics – případy použití

Fondy Spark ve službě Azure synapse Analytics umožňují následující klíčové scénáře:

### <a name="data-engineeringdata-preparation"></a>Data a Příprava dat

Apache Spark obsahuje mnoho jazykových funkcí pro podporu přípravy a zpracování velkých objemů dat, aby je bylo možné lépe využít a následně využívat jiné služby ve službě Azure synapse Analytics. Tato možnost je povolená přes více jazyků (C#, Scala, PySpark, Spark SQL) a poskytnuté knihovny pro zpracování a připojení.

### <a name="machine-learning"></a>Machine Learning

Apache Spark se dodává s [MLlib](https://spark.apache.org/mllib/), knihovny strojového učení postavené na Sparku, kterou můžete použít z fondu Spark ve službě Azure synapse Analytics. Fondy Spark ve službě Azure synapse Analytics zahrnují také Anaconda, distribuci Pythonu s nejrůznějšími balíčky pro datové vědy, včetně strojového učení. V kombinaci s integrovanou podporou poznámkových bloků máte prostředí pro vytváření aplikací strojového učení.

## <a name="where-do-i-start"></a>Kde mám začít

Další informace o Apache Spark ve službě Azure synapse Analytics najdete v následujících článcích:

- [Rychlý Start: Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Rychlý Start: vytvoření poznámkového bloku Apache Spark](../quickstart-apache-spark-notebook.md)
- [Kurz: Machine Learning pomocí Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> Některá z oficiální dokumentace Apache Spark se spoléhá na použití konzoly Spark, což není v Azure synapse Spark k dispozici, místo toho použijte Poznámkový blok nebo IntelliJ.

## <a name="next-steps"></a>Další kroky

V tomto přehledu získáte základní informace o Apache Spark ve službě Azure synapse Analytics. V dalším článku se dozvíte, jak ve službě Azure synapse Analytics vytvořit fond Spark:

- [Vytvoření fondu Spark v Azure synapse](../quickstart-create-apache-spark-pool-portal.md)
