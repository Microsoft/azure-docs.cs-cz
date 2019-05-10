---
title: Co je Azure Databricks?
description: Přečtěte si o Azure Databricks a jak přináší Spark v Databricks v Azure. Azure Databricks je analytická platforma založená na Apache Spark a optimalizovaná pro platformu cloudových služeb Microsoft Azure.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 05/08/2019
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: 0864db682141b1d7f8cb0c9ff14c9897fb02fe1b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412756"
---
# <a name="what-is-azure-databricks"></a>Co je Azure Databricks?

Azure Databricks je analytická platforma založená na Apache Spark a optimalizovaná pro platformu cloudových služeb Microsoft Azure. Platforma Databricks je navržená ve spolupráci se zakladateli Apache Spark a díky integraci s Azure poskytuje nastavení jedním kliknutím, zjednodušené pracovní postupy a interaktivní pracovní prostor, který umožňuje spolupráci mezi datovými vědci, odborníky přes data a obchodními analytiky.

![Co je Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "Co je Azure Databricks?")

Azure Databricks je rychlá, snadná a spolupráci založená na Apache Sparku analytická služba. Pro velké objemy dat profilace, data (raw nebo strukturovaná) je ingestovat v Azure pomocí Azure Data Factory v dávkách nebo streamuje téměř v reálném čase pomocí Kafka, Centrum událostí nebo službu IoT Hub. Tato data území ve službě data lake pro dlouhodobé trvalé úložiště v Azure Blob Storage nebo Azure Data Lake Storage. Jako součást pracovního postupu analytics pomocí Azure Databricks se číst data z různých zdrojů dat, jako [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure Data Lake Storage](../data-lake-store/index.md), [služby Azure Cosmos DB](../cosmos-db/index.yml) , nebo [Azure SQL Data Warehouse](../sql-data-warehouse/index.md) a znovu je zapnout na Převratné přehledy s využitím Sparku.

![Databricks kanálu](./media/what-is-azure-databricks/databricks-pipeline.png)

## <a name="apache-spark-based-analytics-platform"></a>Analytická platforma založená na Apache Sparku

Azure Databricks zahrnuje kompletní opensourcové možnosti a technologie clusteru Apache Spark. Spark v Azure Databricks zahrnuje následující komponenty:

![Apache Spark v Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark v Azure Databricks")

* **Spark SQL a DataFrames**: Spark SQL je modul Sparku pro práci se strukturovanými daty. DataFrame je distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.

* **Streamování**: Zpracování dat v reálném čase a analýzy pro analytické a interaktivní aplikace. Integruje se s HDFS, Flume a Kafka.

* **MLib**: Knihovna pro Machine Learning skládající se z běžných algoritmů a nástrojů, včetně klasifikace, regrese, clusteringu, filtrování založeného na spolupráci, snížení, také základních primitiv optimalizace.

* **GraphX**: Grafy a grafické výpočetní funkce pro široký rozsah případů použití od kognitivních analýz až po zkoumání dat.

* **Spark Core API**: Zahrnuje podporu pro R, SQL, Python, Scala a Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark na platformě Azure Databricks

Azure Databricks staví na možnostech Sparku a poskytuje cloudovou platformu s nulovou správou, která zahrnuje:

- Plně spravované clustery Spark
- Interaktivní pracovní prostor pro zkoumání a vizualizace
- Platformu pro vaše oblíbené aplikace založené na Sparku

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Plně spravované clustery Apache Spark v cloudu

Azure Databricks má zabezpečené a spolehlivé produkční prostředí v cloudu, spravované a podporované odborníky na Spark. Můžete:

* Vytvářet clustery během několik sekund.
* Dynamicky automaticky vertikálně navyšovat nebo snižovat kapacitu clusterů, včetně clusterů bez serveru, a sdílet je napříč týmy. 
* Využívat clustery v kódu programu pomocí rozhraní REST API. 
* Využívat možnosti zabezpečené integrace dat postavené na Sparku, které umožňují unifikovat vaše data bez nutnosti centralizace. 
* Prostřednictvím jednotlivých verzí získat okamžitý přístup k nejnovějším funkcím Apache Sparku.

### <a name="databricks-runtime"></a>Modul runtime Databricks
Modul runtime Databricks je postavený na Apache Sparku a nativně sestavený pro cloud Azure. 

Prostřednictvím možnosti **bez serveru** Azure Databricks při nastavení a konfiguraci infrastruktury dat kompletně abstrahuje od složitosti infrastruktury a potřeby specializovaných odborností. Možnost bez serveru pomáhá odborníkům přes data rychle iterovat jako tým.

Datovým technikům, pro které je důležitý výkon produkčních úloh, Azure Databricks poskytuje sparkový modul, který je rychlejší a výkonnější při zpracování různých optimalizací na vstupně-výstupní vrstvě a vrstvě zpracování (vstupně-výstupní operace Databricks).

### <a name="workspace-for-collaboration"></a>Pracovní prostor pro spolupráci

Prostřednictvím integrovaného prostředí založeného na spolupráci Azure Databricks zjednodušuje proces zkoumání dat, vytváření prototypů a spouštění datových aplikací ve Sparku.

* Snadné zkoumání dat umožňuje určit způsob jejich využití.
* K dokumentování průběhu můžete využít poznámkové bloky v R, Python, Scale nebo SQL.
* K vizualizaci dat stačí několik kliknutí a je možné využít oblíbené nástroje, jako je Matplotlib, ggplot nebo d3.
* Pomocí interaktivních řídicích panelů můžete vytvářet dynamické sestavy.
* Můžete využít Spark a interagovat s daty současně.

## <a name="enterprise-security"></a>Podnikové zabezpečení

Azure Databricks poskytuje zabezpečení Azure na podnikové úrovni, včetně integrace služby Azure Active Directory, řízení na základě rolí a smluv SLA, které chrání vaše data a vaši firmu.

* Integrace s Azure Active Directory umožňuje provozovat kompletní řešení založená na Azure s využitím Azure Databricks.
* Přístup na základě rolí v Azure Databricks umožňuje využívat jemně odstupňovaná uživatelská oprávnění pro poznámkové bloky, clustery, úlohy a data.
* Smlouvy SLA na podnikové úrovni. 

## <a name="integration-with-azure-services"></a>Integrace se službami Azure

Azure Databricks se úzce integruje s úložišti a databázemi Azure: SQL Data Warehouse, Cosmos DB, Data Lake Store a Blob Storage. 

## <a name="integration-with-power-bi"></a>Integrace s Power BI
Díky bohaté integraci s Power BI vám Azure Databricks umožňuje rychle a snadno sdílet důležité nové poznatky. Prostřednictvím koncových bodů clusteru JDBC/ODBC můžete také využívat další nástroje BI, jako je třeba Tableau Software.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Spuštění úlohy Spark job v Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Práce s clustery Spark](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Práce s poznámkovými bloky](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Vytvoření sparkových úloh](https://docs.azuredatabricks.net/user-guide/jobs.html)

 









