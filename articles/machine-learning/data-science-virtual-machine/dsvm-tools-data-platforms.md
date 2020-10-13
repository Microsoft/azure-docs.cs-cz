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
ms.openlocfilehash: 83c0fd796b7527c6f5e396a813def984b88ee9ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440350"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Datové platformy podporované v Data Science Virtual Machine

Pomocí Data Science Virtual Machine (DSVM) můžete vytvářet analýzy pro nejrůznější datové platformy. Kromě rozhraní se vzdálenými datovými platformami poskytuje DSVM místní instanci pro rychlý vývoj a vytváření prototypů.

V DSVM jsou podporovány následující nástroje datové platformy.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Instance místní relační databáze      |
| Podporované edice DSVM      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typická použití      | <ul><li>Rychlý vývoj místně s menší datovou sadou</li><li>Spustit v-databázi R</li></ul> |
| Odkazy na ukázky      | <ul><li>Do databáze SQL se načte malý vzorek datové sady měst v New Yorku:<br/>  `nyctaxi`</li><li>Ukázka Jupyter znázorňující Microsoft Machine Learning Server a analýzy v databázi najdete na adrese:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Související nástroje na DSVM       | <ul><li>SQL Server Management Studio</li><li>Ovladače ODBC/JDBC</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> Edici SQL Server Developer lze použít pouze pro účely vývoje a testování. K jeho spuštění v produkčním prostředí potřebujete licenci nebo jeden z SQL Server virtuálních počítačů.


### <a name="setup"></a>Nastavení

Databázový server je již předem nakonfigurován a služby systému Windows, které souvisejí s SQL Server (například `SQL Server (MSSQLSERVER)` ), jsou nastaveny na automatické spouštění. Jediný krok ruční krok zahrnuje povolení analýzy v databázi pomocí Microsoft Machine Learning Server. Analýzu můžete povolit spuštěním následujícího příkazu jako jednorázové akce v SQL Server Management Studio (SSMS). Spusťte tento příkaz po přihlášení jako správce počítače, otevřete nový dotaz v SSMS a ujistěte se, že vybraná databáze je `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Nahraďte% ComputerName% názvem virtuálního počítače.)

Pokud chcete spustit SQL Server Management Studio, můžete v seznamu programů vyhledat "SQL Server Management Studio" nebo ho vyhledat a spustit pomocí služby Windows Search. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte **ověřování systému Windows** a použijte název počítače nebo ```localhost``` v poli **SQL Server název** .

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit

Ve výchozím nastavení se databázový server s výchozí instancí databáze spustí automaticky. K místnímu přístupu k databázi SQL Server můžete použít nástroje, jako je SQL Server Management Studio na virtuálním počítači. Účty místních správců mají v databázi přístup správce.

Součástí DSVM jsou taky ovladače ODBC a JDBC, které vám pomůžou komunikovat s SQL Server, databázemi SQL Azure a Azure synapse Analytics z aplikací napsaných v několika jazycích, včetně Pythonu a Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je nakonfigurovaná a nainstalovaná na DSVM? 

 SQL Server se instaluje standardním způsobem. Najdete ho na adrese `C:\Program Files\Microsoft SQL Server` . Instance Machine Learning Server v databázi se nachází na adrese `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . DSVM má také samostatnou samostatnou instanci Machine Learning Server, která je nainstalována v `C:\Program Files\Microsoft\R Server\R_SERVER` . Tyto dvě instance Machine Learning Server nesdílejí knihovny.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (samostatně)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Samostatná instance pro samostatnou Apache Spark platformu (jeden uzel v procesu); systém pro rychlé a rozsáhlé zpracování dat a strojové učení     |
| Podporované edice DSVM      | Linux     |
| Typická použití      | <ul><li>Rychlý vývoj aplikací Spark/PySpark v místním prostředí s menší datovou sadou a novějším nasazením ve velkých clusterech Spark, jako je Azure HDInsight</li><li>Test Microsoft Machine Learning Server – kontext Sparku</li><li>Sestavování aplikací ML pomocí knihovny [MMLSpark](https://github.com/Azure/mmlspark) open source v SparkML nebo Microsoftu</li></ul> |
| Odkazy na ukázky      |    Ukázka Jupyter:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (kontext Spark):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Související nástroje na DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (jádra Spark/PySpark)</li><li>Microsoft Machine Learning Server, SPARKER, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Jak ji použít
Úlohy Spark můžete odeslat na příkazovém řádku spuštěním `spark-submit` `pyspark` příkazu nebo. Můžete také vytvořit Poznámkový blok Jupyter vytvořením nového poznámkového bloku pomocí jádra Spark.

Spark z jazyka R lze použít pomocí knihoven jako Spark, Sparklyr a Microsoft Machine Learning Server, které jsou k dispozici na DSVM. Viz odkazy na ukázky v předchozí tabulce.

### <a name="setup"></a>Nastavení
Před spuštěním v kontextu Sparku v Microsoft Machine Learning Server na edici Ubuntu Linux DSVM je nutné provést jednorázový krok nastavení, který povolí místní jeden uzel Hadoop HDFS a instanci příz. Ve výchozím nastavení jsou na DSVM nainstalovány služby Hadoop, ale jsou zakázané. Pokud je chcete povolit, spusťte následující příkazy jako první při prvním:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související se systémem Hadoop můžete zastavit, pokud je již nepotřebujete spuštěním nástroje ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Ukázka, která ukazuje, jak vyvíjet a testovat paní ve vzdáleném kontextu Spark (což je samostatná instance Spark na DSVM), je k dispozici v `/dsvm/samples/MRS` adresáři.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je nakonfigurovaná a nainstalovaná na DSVM? 
|Platforma|Umístění instalace ($SPARK _HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Knihovny pro přístup k datům ze služby Azure Blob Storage nebo Azure Data Lake Storage pomocí knihoven strojového učení Microsoft MMLSpark jsou předinstalované v $SPARK _HOME/Jars. Tyto JAR se automaticky načtou při spuštění Sparku. Ve výchozím nastavení používá Spark data na místním disku. 

Aby instance Spark na DSVM mohla získat přístup k datům uloženým ve službě BLOB Storage nebo Azure Data Lake Storage, musíte soubor vytvořit a nakonfigurovat na `core-site.xml` základě šablony nalezené v $SPARK _HOME/conf/core-site.xml. template. Musíte mít také příslušné přihlašovací údaje pro přístup k úložišti objektů BLOB a Azure Data Lake Storage. (Všimněte si, že soubory šablon používají zástupné symboly pro úložiště objektů BLOB a Azure Data Lake Storage konfigurace.)

Podrobnější informace o vytváření přihlašovacích údajů služby Azure Data Lake Storage najdete v tématu [ověřování pomocí Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po zadání přihlašovacích údajů pro úložiště objektů BLOB nebo Azure Data Lake Storage do souboru core-site.xml můžete odkazovat na data uložená v těchto zdrojích prostřednictvím předpony URI wasb://nebo adl://.

