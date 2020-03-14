---
title: Podporované datové platformy
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si o podporovaných datových platformách a nástrojích pro Azure Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270105"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Datové platformy podporované na virtuální počítač pro datové vědy

Pomocí Data Science Virtual Machine (DSVM) můžete vytvářet analýzy pro nejrůznější datové platformy. Kromě rozhraní pro platformy vzdálených dat datové VĚDY poskytuje místní instanci pro rychlý vývoj a vytváření prototypů.

V DSVM jsou podporovány následující nástroje datové platformy.

## <a name="sql-server-developer-edition"></a>Edice SQL Server Developer

| | |
| ------------- | ------------- |
| Co je to?   | Instance místní relační databáze      |
| Podporované edice DSVM      | Windows: SQL Server 2017, Windows 2019 (Preview): SQL Server 2019      |
| Typické použití      | Rychlý vývoj místně s menší datové sady <br/> Využívejte jazyk R v databázi   |
| Odkazy na ukázky      |    Do databáze SQL se načte malý vzorek datové sady měst v New Yorku:<br/>  `nyctaxi` <br/> Ukázka Jupyter znázorňující Microsoft Machine Learning Server a analýzy v databázi najdete na adrese:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Související nástroje na DSVM       | SQL Server Management Studio <br/> Ovladače ODBC/JDBC<br/> modul pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> Edici SQL Server Developer lze použít pouze pro účely vývoje a testování. Musíte mít licenci nebo jeden z virtuálních počítačů SQL serveru ke spuštění v produkčním prostředí.


### <a name="setup"></a>Nastavení

Databázový server je již předem nakonfigurován a služby systému Windows, které souvisejí s SQL Server (například `SQL Server (MSSQLSERVER)`), jsou nastaveny na automatické spouštění. Jediný krok ruční krok zahrnuje povolení analýzy v databázi pomocí Microsoft Machine Learning Server. Analýzu můžete povolit spuštěním následujícího příkazu jako jednorázové akce v SQL Server Management Studio (SSMS). Spusťte tento příkaz po přihlášení jako správce počítače, otevřete nový dotaz v SSMS a ujistěte se, že vybraná databáze je `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Pokud chcete spustit SQL Server Management Studio, můžete v seznamu programů vyhledat "SQL Server Management Studio" nebo ho vyhledat a spustit pomocí služby Windows Search. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte **ověřování systému Windows** a do pole **SQL Server název** zadejte název počítače nebo ```localhost```.

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit

Ve výchozím nastavení se databázový server s výchozí instancí databáze spustí automaticky. Nástroje, jako je SQL Server Management Studio na virtuálním počítači můžete použít pro přístup k databázi systému SQL Server místně. Účty místních správců mají v databázi přístup správce.

Součástí DSVM jsou taky ovladače ODBC a JDBC, které vám pomůžou komunikovat s SQL Server, databázemi SQL Azure a Azure SQL Data Warehouse z aplikací napsaných v několika jazycích, včetně Pythonu a Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je nakonfigurovaná a nainstalovaná na DSVM? 

 SQL Server se instaluje standardním způsobem. Dá se najít na adrese `C:\Program Files\Microsoft SQL Server`. Instance Machine Learning Server v databázi se nachází na `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM má také samostatnou samostatnou instanci Machine Learning Server, která je nainstalovaná na `C:\Program Files\Microsoft\R Server\R_SERVER`. Tyto dvě instance Machine Learning Server nesdílejí knihovny.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (samostatně)

| | |
| ------------- | ------------- |
| Co je to?   | Samostatná instance pro samostatnou Apache Spark platformu (jeden uzel v procesu); systém pro rychlé a rozsáhlé zpracování dat a strojové učení     |
| Podporované edice DSVM      | Linux     |
| Typické použití      | * Rychlý vývoj aplikací Spark/PySpark v místním prostředí s menší datovou sadou a novějším nasazením ve velkých clusterech Spark, jako je Azure HDInsight<br/> * Test Microsoft Machine Learning Server – kontext Sparku <br />* K sestavování aplikací ML použijte knihovnu [MMLSpark](https://github.com/Azure/mmlspark) open source v SparkML nebo Microsoftu. |
| Odkazy na ukázky      |    Jupyter ukázka: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (kontext Spark):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Související nástroje na DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark jader)<br/>Microsoft Machine Learning Server, SPARKER, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Jak ji použít
Na příkazovém řádku můžete odeslat úlohy Spark spuštěním příkazu `spark-submit` nebo `pyspark`. Poznámkový blok Jupyter můžete vytvořit také tak, že vytvoříte nový poznámkový blok s jádra Spark.

Spark z jazyka R lze použít pomocí knihoven jako Spark, Sparklyr a Microsoft Machine Learning Server, které jsou k dispozici na DSVM. Zobrazit odkazy na ukázky v předchozí tabulce.

### <a name="setup"></a>Nastavení
Před spuštěním v kontextu Sparku v Microsoft Machine Learning Server na edici Ubuntu Linux DSVM je nutné provést jednorázový krok nastavení, který povolí místní jeden uzel Hadoop HDFS a instanci příz. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Pokud je chcete povolit, spusťte následující příkazy jako první při prvním:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Služby související se systémem Hadoop můžete zastavit, pokud je už nepotřebujete spuštěním ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Ukázka, která ukazuje, jak vyvíjet a testovat paní ve vzdáleném kontextu Spark (což je samostatná instance Spark na DSVM), je k dispozici v adresáři `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je nakonfigurovaná a nainstalovaná na DSVM? 
|Platforma|Nainstalujte umístění ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Knihovny pro přístup k datům ze služby Azure Blob Storage nebo Azure Data Lake Storage pomocí knihoven strojového učení Microsoft MMLSpark jsou předinstalované v $SPARK _HOME/Jars. Tyto kromě souborů JAR jsou automaticky načteny při spuštění Sparku. Ve výchozím nastavení používá Spark data na místním disku. 

Aby instance Spark na DSVM mohla získat přístup k datům uloženým ve službě BLOB Storage nebo Azure Data Lake Storage, musíte vytvořit a nakonfigurovat `core-site.xml` soubor na základě šablony nalezené v $SPARK _HOME/conf/Core-site.XML.template. Musíte mít také příslušné přihlašovací údaje pro přístup k úložišti objektů BLOB a Azure Data Lake Storage. (Všimněte si, že soubory šablon používají zástupné symboly pro úložiště objektů BLOB a Azure Data Lake Storage konfigurace.)

Podrobnější informace o vytváření přihlašovacích údajů služby Azure Data Lake Storage najdete v tématu [ověřování pomocí Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po zadání přihlašovacích údajů pro úložiště objektů BLOB nebo Azure Data Lake Storage do souboru Core-site. XML můžete odkazovat na data uložená v těchto zdrojích prostřednictvím předpony identifikátoru URI wasb://nebo adl://.

