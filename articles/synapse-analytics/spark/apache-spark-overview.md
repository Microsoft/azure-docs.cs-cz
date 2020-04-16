---
title: Co je Apache Spark v Azure Synapse Analytics?
description: Tento článek obsahuje úvod do Apache Spark v Azure Synapse Analytics a různé scénáře, ve kterých můžete použít Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4155e419bb03613abad808ca09d84bcb6583291f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423646"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Co je Apache Spark v Azure Synapse Analytics

Apache Spark je paralelní architektura zpracování, která podporuje zpracování v paměti pro zvýšení výkonu analytických aplikací pro velká data. Apache Spark v Azure Synapse Analytics je jednou z implementací Apache Spark společnosti Microsoft v cloudu. Azure Synapse usnadňuje vytváření a konfiguraci fondu Spark (preview) v Azure. Fondy Spark v Azure Synapse jsou kompatibilní s Azure Storage a Azure Data Lake Generation 2 Storage. Takže můžete použít fondy Azure Spark ke zpracování dat uložených v Azure.

![Spark: jednotné rozhraní](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Co je Apache Spark

Apache Spark poskytuje primitiva pro výpočetní výpočetní prostředí clusteru v paměti. Úloha Spark může načítat data, ukládat je do paměti a opakovaně je dotazovat. V paměti je výpočetní technika mnohem rychlejší než aplikace založené na disku. Spark se také integruje s více programovacími jazyky, abyste s nimi manipulovali s distribuovanými datovými sadami, jako jsou místní kolekce. Není nutné strukturovat všechno jako mapovací a redukční operace.

![Tradiční MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Fondy Spark v Azure Synapse nabízejí plně spravovanou službu Spark. Výhody vytvoření fondu Spark v Synapse Analytics jsou uvedeny zde.

| Funkce | Popis |
| --- | --- |
| Rychlost a efektivita |Instance Spark začínají přibližně za 2 minuty pro méně než 60 uzlů a přibližně 5 minut pro více než 60 uzlů. Instance se ve výchozím nastavení vypne 5 minut po poslední úloze, pokud není udržována naživu připojením poznámkového bloku. |
| Snadné vytvoření |Nový fond Spark v Azure Synapse můžete vytvořit během několika minut pomocí portálu Azure, Azure PowerShellu nebo sady Synapse Analytics .NET SDK. Viz [Začínáme s fondy Spark v Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md). |
| Snadné používání |Synapse Analytics obsahuje vlastní notebook odvozený od [Společnosti Nteract](https://nteract.io/). Tyto poznámkové bloky můžete použít pro interaktivní zpracování dat a vizualizaci.|
| Rozhraní REST API |Spark v Synapse Analytics zahrnuje [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), server úloh Spark založený na rozhraní REST API pro vzdálené odesílání a sledování úloh. |
| Podpora pro Azure Data Lake Storage Storage Generation 2| Fondy Spark v Azure Synapse můžou používat Azure Data Lake Storage Storage Generation 2 i úložiště BLOB. Další informace o úložišti datových jezer najdete [v tématu Přehled úložiště datových jezer Azure](../../data-lake-store/data-lake-store-overview.md). |
| Integrace v prostředí IDE třetích stran | Azure Synapse poskytuje ide plugin pro [Jetbrains 'IntelliJ IDEA,](https://www.jetbrains.com/idea/) který je užitečný pro vytváření a odesílání aplikací do fondu Spark. |
| Předem zavedené knihovny Anaconda |Fondy Spark v Azure Synapse jsou dodávány s předinstalovanými knihovnami Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) poskytuje téměř 200 knihoven pro machine learning, analýzy dat, vizualizace atd. |
| Škálovatelnost | Apache Spark ve fondech Azure Synapse může mít povolené automatické škálování, takže fondy se škálují nahoru a dolů podle potřeby. Fondy Spark lze také vypnout bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage. |

Fondy spark v Azure Synapse zahrnují následující součásti, které jsou ve fondech k dispozici ve výchozím nastavení.

- [Spark Core](https://spark.apache.org/docs/latest/). Zahrnuje Spark Core, Spark SQL, GraphX a MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apačská Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architektura spark poolu

Je snadné pochopit součásti Spark tím, že porozumíme tomu, jak Spark běží na Synapse Analytics.

Spark aplikace spustit jako nezávislé sady procesů ve fondu, koordinovány SparkContext objekt u hlavního programu (tzv. program ovladače).

SparkContext se může připojit ke správci clusteru, který přiděluje prostředky napříč aplikacemi. Správce clusteru je [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Po připojení Spark získá vykonavatele na uzlech ve fondu, což jsou procesy, které spouštějí výpočty a ukládají data pro vaši aplikaci. Pak do exekutorů odešle kód vaší aplikace (definovaný v souborech JAR nebo Pythonu předaných do objektu SparkContext). Nakonec SparkContext odešle do exekutorů úlohy ke spuštění.

SparkContext spustí hlavní funkci uživatele a provede různé paralelní operace na uzlech. Potom SparkContext shromažďuje výsledky těchto operací. Uzly číst a zapisovat data ze systému souborů a do systému souborů. Uzly také mezipaměti transformovaná data v paměti jako odolné distribuované datové sady (RDD).

SparkContext se připojí k fondu Spark a je zodpovědný za převod aplikace na orientovaný graf (DAG). Graf se skládá z jednotlivých úloh, které se spouštějí v rámci procesu vykonavatele na uzlech. Každá aplikace získá vlastní procesy exekutora, které zůstávají v provozu po dobu trvání celé aplikace a spouští úlohy ve více vláknech.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark v případech použití Synapse Analytics

Fondy spark v Synapse Analytics umožňují následující klíčové scénáře:

### <a name="data-engineeringdata-preparation"></a>Datové inženýrství/příprava dat

Apache Spark obsahuje mnoho jazykových funkcí pro podporu přípravy a zpracování velkých objemů dat, aby mohly být cennější a pak spotřebovány jinými službami v rámci Synapse Analytics. To je povoleno prostřednictvím více jazyků (C#, Scala, PySpark, Spark SQL) a dodávaných knihoven pro zpracování a připojení.

### <a name="machine-learning"></a>Machine Learning

Apache Spark je dodáván s [Knihovnou Strojového učení MLlib](https://spark.apache.org/mllib/)postavenou na Sparku, kterou můžete použít z fondu Spark v Synapse Analytics. Mezi fondy Spark v Synapse Analytics patří také Anaconda, distribuce Pythonu s různými balíčky pro datovou vědu včetně strojového učení. V kombinaci s integrovanou podporou pro poznámkové bloky máte prostředí pro vytváření aplikací strojového učení.

## <a name="where-do-i-start"></a>Kde mám začít

Další informace o Apache Spark v Synapse Analytics najdete v následujících článcích:

- [Úvodní příručka: Vytvoření fondu Spark v Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
- [Výuka: Strojové učení pomocí Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Oficiální dokumentace Apache Spark](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Některé z oficiálních Apache Spark dokumentace závisí na použití jiskry konzole, to není k dispozici na Azure Synapse Spark, použijte notebook nebo IntelliJ prostředí místo

## <a name="next-steps"></a>Další kroky

V tomto přehledu získáte základní znalosti apache spark v Azure Synapse Analytics. Přejdete k dalšímu článku, kde se dozvíte, jak vytvořit fond Spark v Azure Synapse Analytics:

- [Vytvoření fondu Spark ve Službě Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
