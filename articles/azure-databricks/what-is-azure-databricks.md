---
title: Co je Azure Databricks?
description: Přečtěte si o Azure Databricks a o tom, jak přináší Spark na datacihly do Azure. Azure Databricks je analytická platforma založená na Apache Spark optimalizovaná pro platformu Microsoft Azure cloudové služby.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 05/08/2019
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: 149b83b9aacf23cde06a2b35aed06c164afe9647
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243331"
---
# <a name="what-is-azure-databricks"></a>Co je Azure Databricks?

Azure Databricks je analytická platforma založená na Apache Spark optimalizovaná pro platformu Microsoft Azure cloudové služby. Technologie datacihly, které jsou navržené s využitím Apache Spark, jsou integrovány do Azure, aby poskytovaly nastavení jedním kliknutím, zjednodušené pracovní postupy a interaktivní pracovní prostor, který umožňuje spolupráci mezi odborníky přes data, datovými inženýry a obchodními analytiky.

![Co je Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "Co je Azure Databricks?")

Azure Databricks je rychlá a snadná analytická služba založená na Apache Spark a spolupráci. V případě kanálu pro velké objemy dat se data (hrubá nebo strukturovaná) ingestují do Azure prostřednictvím Azure Data Factory v dávkách nebo se streamují téměř v reálném čase s využitím Kafka, centra událostí nebo IoT Hub. Tato data se nacházejí v Data Lake pro dlouhodobé trvalé úložiště v Azure Blob Storage nebo Azure Data Lake Storage. Jako součást pracovního postupu analýzy použijte Azure Databricks ke čtení dat z více zdrojů dat, jako je [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure Data Lake Storage](../data-lake-store/index.md), [Azure Cosmos DB](../cosmos-db/index.yml)nebo [Azure SQL Data Warehouse](../sql-data-warehouse/index.yml) , a převeďte je na průlom. přehledy pomocí Sparku

![Kanál datacihly](./media/what-is-azure-databricks/databricks-pipeline.png)

## <a name="apache-spark-based-analytics-platform"></a>Analytická platforma založená na Apache Spark

Azure Databricks se skládá z kompletních Open Source Apache Spark a technologií clusteru. Spark v Azure Databricks zahrnuje tyto komponenty:

![Apache Spark v Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark v Azure Databricks")

* **Spark SQL a datové rámce**: Spark SQL je modul Spark pro práci se strukturovanými daty. Datový rámec je distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Je koncepčně ekvivalentní tabulce v relační databázi nebo datovému snímku v R/Pythonu.

* **Streamování**: zpracování a analýza dat v reálném čase pro analytické a interaktivní aplikace. Integruje se s HDFS, Flume a Kafka.

* **MLib**: knihovna Machine Learning skládající se ze společného výukového algoritmu a nástrojů, včetně klasifikace, regrese, clusteringu, filtrování spolupráce, snížení počtu dimenzí a také základních primitiv optimalizace.

* **GRAPHX**: grafy a výpočty grafů pro širokou škálu případů použití ze rozpoznávání analýz až po zkoumání dat.

* **Spark Core API**: zahrnuje podporu pro R, SQL, Python, Scala a Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark v Azure Databricks

Azure Databricks sestaví na možnostech Sparku tím, že poskytuje cloudovou platformu s nulovou správou, která zahrnuje:

- Plně spravované Clustery Spark
- Interaktivní pracovní prostor pro zkoumání a vizualizaci
- Platforma pro vaše oblíbené aplikace založené na Sparku

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Plně spravované clustery Apache Spark v cloudu

Azure Databricks má v cloudu zabezpečené a spolehlivé provozní prostředí, spravované a podporované odborníky na Spark. Můžete postupovat následovně:

* Vytvořte clustery během několika sekund.
* Dynamické automatické škálování clusterů směrem nahoru a dolů, včetně clusterů bez serveru a jejich sdílení napříč týmy. 
* Clustery můžete používat programově pomocí rozhraní REST API. 
* Využijte možnosti zabezpečené integrace dat postavené na Sparku, které vám umožní sjednotit data bez centralizace. 
* Získejte okamžitý přístup k nejnovějším funkcím Apache Spark s každou vydanou verzí.

### <a name="databricks-runtime"></a>Databricks Runtime
Databricks Runtime je postavená na Apache Spark a je nativně sestavená pro cloud Azure. 

Díky možnosti bez **serveru** Azure Databricks zcela abstrakci složitosti infrastruktury a potřebě specializovaných odborných znalostí o nastavení a konfiguraci infrastruktury dat. Možnost bez serveru pomáhá odborníkům přes data rychle iterovat v týmu.

U datových techniků, kteří se zajímají o výkon produkčních úloh, Azure Databricks poskytuje modul Spark, který je rychlejší a provede různými optimalizacemi ve vrstvě I/O a ve vrstvě zpracování (datacihly v/v).

### <a name="workspace-for-collaboration"></a>Pracovní prostor pro spolupráci

Díky spolupráci a integrovanému prostředí Azure Databricks zjednodušuje proces zkoumání dat, vytváření prototypů a spouštění aplikací řízených daty v Sparku.

* Určete, jak se mají používat data pomocí snadného zkoumání dat.
* Zdokumentujte svůj pokrok v poznámkových blocích v R, Pythonu, Scala nebo SQL.
* Vizualizujte data několika kliknutími a používejte známé nástroje, jako je matplotlib, ggplot nebo D3.
* K vytváření dynamických sestav použijte interaktivní řídicí panely.
* Pomocí Sparku a interakce s daty současně.

## <a name="enterprise-security"></a>Podnikové zabezpečení

Azure Databricks poskytuje zabezpečení Azure na podnikové úrovni, včetně Integrace Azure Active Directory, ovládacích prvků založených na rolích a SLA, které chrání vaše data a vaše podnikání.

* Integrace s Azure Active Directory umožňuje spouštět kompletní řešení založená na Azure pomocí Azure Databricks.
* Přístup na základě rolí Azure Databricks umožňuje jemně odstupňovaná uživatelská oprávnění pro poznámkové bloky, clustery, úlohy a data.
* SLA na podnikové úrovni. 

## <a name="integration-with-azure-services"></a>Integrace se službami Azure

Azure Databricks se integruje hluboko s databázemi a úložišti Azure: SQL Data Warehouse, Cosmos DB, Data Lake Store a Blob Storage. 

## <a name="integration-with-power-bi"></a>Integrace s Power BI
Díky obsáhlé integraci se Power BI Azure Databricks umožňuje rychle a snadno vyhledávat a sdílet vaše působivé poznatky. Můžete použít i další nástroje BI, jako je Tableau software prostřednictvím koncových bodů clusteru JDBC/ODBC.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: spuštění úlohy Sparku na Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Práce s clustery Spark](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Práce s poznámkovými bloky](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Vytváření úloh Spark](https://docs.azuredatabricks.net/user-guide/jobs.html)

 









