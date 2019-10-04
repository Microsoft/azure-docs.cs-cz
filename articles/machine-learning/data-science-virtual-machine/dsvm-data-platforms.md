---
title: Podporované datové platformy
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si o podporovaných datových platformách a nástrojích pro Azure Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, nástroje pro datové vědy, datové vědy pro Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: df112889fd7cd8ad1574147072b6e13137945462
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947546"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Datové platformy podporované v Data Science Virtual Machine

Pomocí Data Science Virtual Machine (DSVM) můžete vytvářet analýzy pro nejrůznější datové platformy. Kromě rozhraní se vzdálenými datovými platformami poskytuje DSVM místní instanci pro rychlý vývoj a vytváření prototypů.

V DSVM jsou podporovány následující nástroje datové platformy.

## <a name="sql-server-2017-developer-edition"></a>Edice SQL Server 2017 Developer Edition

| | |
| ------------- | ------------- |
| Co to je?   | Instance místní relační databáze      |
| Podporované edice DSVM      | Windows      |
| Typická použití      | Rychlý vývoj místně s menší datovou sadou <br/> Spustit v-databázi R   |
| Odkazy na ukázky      |    Do databáze SQL se načte malý vzorek datové sady měst v New Yorku:<br/>  `nyctaxi` <br/> Ukázka Jupyter znázorňující Microsoft Machine Learning Server a analýzy v databázi najdete na adrese:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Související nástroje na DSVM       | SQL Server Management Studio <br/> Ovladače ODBC/JDBC<br/> pyodbc, RODBC<br />Podrobnosti o Apache      |

> [!NOTE]
> Edice SQL Server 2016 Developer Edition lze použít pouze pro účely vývoje a testování. K jeho spuštění v produkčním prostředí potřebujete licenci nebo jeden z SQL Server virtuálních počítačů.


### <a name="setup"></a>Instalace

Databázový server je již předem nakonfigurován a služby systému Windows související s SQL Server (jako `SQL Server (MSSQLSERVER)`) jsou nastaveny na automatické spouštění. Jediný krok ruční krok zahrnuje povolení analýzy v databázi pomocí Microsoft Machine Learning Server. To můžete provést tak, že spustíte následující příkaz jako jednorázovou akci v SQL Server Management Studio (SSMS). Spusťte tento příkaz po přihlášení jako správce počítače, otevřete nový dotaz v SSMS a ujistěte se, že vybraná databáze je `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Pokud chcete spustit SQL Server Management Studio, můžete v seznamu programů vyhledat "SQL Server Management Studio" nebo ho vyhledat a spustit pomocí služby Windows Search. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte **ověřování systému Windows** a v poli **SQL Server název** zadejte název počítače nebo ```localhost```.

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit

Ve výchozím nastavení se databázový server s výchozí instancí databáze spustí automaticky. K místnímu přístupu k databázi SQL Server můžete použít nástroje, jako je SQL Server Management Studio na virtuálním počítači. Účty místních správců mají v databázi přístup správce.

Součástí DSVM jsou taky ovladače ODBC a JDBC, které vám pomůžou komunikovat s SQL Server, databázemi SQL Azure a Azure SQL Data Warehouse z aplikací napsaných v několika jazycích, včetně Pythonu a Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je nakonfigurovaná a nainstalovaná na DSVM? 

 SQL Server se instaluje standardním způsobem. Dá se najít na adrese `C:\Program Files\Microsoft SQL Server`. Instance Machine Learning Server v databázi se nachází na `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM má také samostatnou samostatnou instanci Machine Learning Server, která je nainstalovaná na `C:\Program Files\Microsoft\R Server\R_SERVER`. Tyto dvě instance Machine Learning Server nesdílejí knihovny.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (samostatně)

| | |
| ------------- | ------------- |
| Co to je?   | Samostatná instance pro samostatnou Apache Spark platformu (jeden uzel v procesu); systém pro rychlé a rozsáhlé zpracování dat a strojové učení     |
| Podporované edice DSVM      | Linux <br /> Windows (experimentální)      |
| Typická použití      | * Rychlý vývoj aplikací Spark/PySpark v místním prostředí s menší datovou sadou a novějším nasazením ve velkých clusterech Spark, jako je Azure HDInsight<br/> * Test Microsoft Machine Learning Server – kontext Sparku <br />* K sestavování aplikací ML použijte knihovnu [MMLSpark](https://github.com/Azure/mmlspark) open source v SparkML nebo Microsoftu. |
| Odkazy na ukázky      |    Ukázka Jupyter: <br />&nbsp; @ no__t-1 * ~/notebooks/SparkML/pySpark <br /> &nbsp; @ no__t-1 * ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (kontext Spark):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Související nástroje na DSVM       | PySpark, Scala<br/>Jupyter (jádra Spark/PySpark)<br/>Microsoft Machine Learning Server, SPARKER, Sparklyr <br />Podrobnosti o Apache      |

### <a name="how-to-use-it"></a>Jak ji použít
Na příkazovém řádku můžete odeslat úlohy Spark spuštěním příkazu `spark-submit` nebo `pyspark`. Můžete také vytvořit Poznámkový blok Jupyter vytvořením nového poznámkového bloku pomocí jádra Spark.

Spark z jazyka R lze použít pomocí knihoven jako Spark, Sparklyr a Microsoft Machine Learning Server, které jsou k dispozici na DSVM. Viz odkazy na ukázky v předchozí tabulce.

> [!NOTE]
> Spuštění Microsoft Machine Learning Server v kontextu Sparku DSVM je podporováno pouze v edici Ubuntu Linux DSVM.



### <a name="setup"></a>Instalace
Před spuštěním v kontextu Sparku v Microsoft Machine Learning Server na edici Ubuntu Linux DSVM je nutné provést jednorázový krok nastavení, který povolí místní jeden uzel Hadoop HDFS a instanci příz. Ve výchozím nastavení jsou na DSVM nainstalovány služby Hadoop, ale jsou zakázané. Pokud je chcete povolit, spusťte následující příkazy jako první při prvním:

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
|Platforma|Umístění instalace ($SPARK _HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Knihovny pro přístup k datům ze služby Azure Blob Storage nebo Azure Data Lake Storage pomocí knihoven strojového učení Microsoft MMLSpark jsou předinstalované v $SPARK _HOME/jar. Tyto JAR se automaticky načtou při spuštění Sparku. Ve výchozím nastavení používá Spark data na místním disku. 

Aby instance Spark na DSVM mohla získat přístup k datům uloženým ve službě BLOB Storage nebo Azure Data Lake Storage, musíte vytvořit a nakonfigurovat soubor `core-site.xml` na základě šablony, kterou najdete v $SPARK _HOME/conf/Core-site. XML. template. Musíte mít také příslušné přihlašovací údaje pro přístup k úložišti objektů BLOB a Azure Data Lake Storage. (Všimněte si, že soubory šablon používají zástupné symboly pro úložiště objektů BLOB a Azure Data Lake Storage konfigurace.)

Podrobnější informace o vytváření přihlašovacích údajů služby Azure Data Lake Storage najdete v tématu [ověřování pomocí Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po zadání přihlašovacích údajů pro úložiště objektů BLOB nebo Azure Data Lake Storage do souboru Core-site. XML můžete odkazovat na data uložená v těchto zdrojích prostřednictvím předpony identifikátoru URI wasb://nebo adl://.

