---
title: Podporované datové platformy
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si o podporovaných datových platformách a nástrojích pro Virtuální počítač Azure Data Science.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282320"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Datové platformy podporované ve virtuálním počítači pro datové vědy

S virtuálním počítačem pro datové vědy (DSVM) můžete vytvářet své analýzy na široké škále datových platforem. Kromě rozhraní pro vzdálené datové platformy poskytuje DSVM místní instanci pro rychlý vývoj a prototypování.

Následující nástroje datové platformy jsou podporovány na DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Co je to?   | Instance místní relační databáze      |
| Podporované edice DSVM      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typická použití      | Rychlý vývoj lokálně s menším datovým souborem <br/> Spustit v databázi R   |
| Odkazy na ukázky      |    Do databáze SQL se načte malá ukázka datové sady V New Yorku:<br/>  `nyctaxi` <br/> Ukázku jupyteru zobrazující Microsoft Machine Learning Server a analýzu v databázi naleznete na adrese:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Související nástroje na DSVM       | SQL Server Management Studio <br/> Ovladače ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition lze použít pouze pro účely vývoje a testování. Ke spuštění v produkčním prostředí potřebujete licenci nebo jeden z virtuálních modulů SQL Server.


### <a name="setup"></a>Nastavení

Databázový server je již předkonfigurován a služby `SQL Server (MSSQLSERVER)`systému Windows související se serverem SQL (například) jsou nastaveny na automatické spuštění. Jediný ruční krok zahrnuje povolení analýzy v databázi pomocí Serveru Microsoft Machine Learning Server. Analýzy můžete povolit spuštěním následujícího příkazu jako jednorázové akce v aplikaci SQL Server Management Studio (SSMS). Spusťte tento příkaz po přihlášení jako správce počítače, otevřete nový dotaz v `master`SSMS a ujistěte se, že vybraná databáze je :

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Chcete-li spustit aplikaci SQL Server Management Studio, můžete v seznamu programů vyhledat "SQL Server Management Studio" nebo ji vyhledat a spustit pomocí služby Windows Search. Po zobrazení výzvy k zadání pověření vyberte **možnost Ověřování systému Windows** a použijte název počítače nebo ```localhost``` v poli Název serveru **SQL** Server.

### <a name="how-to-use-and-run-it"></a>Jak ji používat a spouštět

Ve výchozím nastavení se databázový server s výchozí instancí databáze spustí automaticky. Můžete použít nástroje, jako je SQL Server Management Studio na virtuálním počítači pro přístup k databázi SERVERU SQL Server místně. Účty místních správců mají přístup správce k databázi.

DSVM je také dodáván s ovladači ODBC a JDBC pro rozhovor s SQL Server, Azure SQL databáze a Azure SQL Data Warehouse z aplikací napsaných ve více jazycích, včetně Pythonu a Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je konfigurován a nainstalován na DSVM? 

 SQL Server je nainstalován standardním způsobem. To lze nalézt `C:\Program Files\Microsoft SQL Server`na . Instanci Machine Learning Server v `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`databázi najdete na adrese . DSVM má také samostatnou samostatnou instanci serveru `C:\Program Files\Microsoft\R Server\R_SERVER`Machine Learning Server, která je nainstalována na adrese . Tyto dvě instance machine learningového serveru nesdílejí knihovny.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (samostatný)

| | |
| ------------- | ------------- |
| Co je to?   | Samostatná instance (jeden uzel v procesu) populární platformy Apache Spark; systém pro rychlé a rozsáhlé zpracování dat a strojové učení     |
| Podporované edice DSVM      | Linux     |
| Typická použití      | * Rychlý vývoj aplikací Spark/PySpark lokálně s menší datovou sadou a pozdějšínasazení na velkých clusterech Spark, jako je Azure HDInsight<br/> * Test Microsoft Machine Learning Server Spark kontextu <br />* Použijte SparkML nebo Microsoft open-source [Knihovna MMLSpark](https://github.com/Azure/mmlspark) k vytváření ML aplikací |
| Odkazy na ukázky      |    Jupytervzorek: <br />&nbsp;&nbsp;* ~ / notebooky / SparkML / pySpark <br /> &nbsp;&nbsp;* ~/notebooky/MMLSpark <br /> Microsoft Machine Learning Server (kontext Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Související nástroje na DSVM       | PySpark, Scala<br/>Jupyter (Jádra Spark/PySpark)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Jak ji použít
Úlohy Spark můžete odeslat na `spark-submit` příkazovém řádku spuštěním příkazu nebo. `pyspark` Poznámkový blok Jupyter můžete také vytvořit vytvořením nového poznámkového bloku s jádrem Spark.

Spark z R můžete použít pomocí knihoven, jako je SparkR, Sparklyr a Microsoft Machine Learning Server, které jsou k dispozici na DSVM. Viz ukazatele na ukázky v předchozí tabulce.

### <a name="setup"></a>Nastavení
Před spuštěním v kontextu Spark v Microsoft Machine Learning Server na Ubuntu Linux DSVM vydání, musíte dokončit jednorázový krok nastavení povolit místní jeden uzel Hadoop HDFS a příze instance. Ve výchozím nastavení jsou služby Hadoop nainstalovány, ale zakázány v dsvm. Chcete-li je povolit, spusťte poprvé jako kořen následující příkazy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Služby související s Hadoopem můžete zastavit, když ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```je již nepotřebujete, spuštěním .

Ukázka, která ukazuje, jak vyvíjet a testovat MRS ve vzdáleném kontextu Spark (což je samostatná instance `/dsvm/samples/MRS` Spark na DSVM) je k dispozici a k dispozici v adresáři.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak je konfigurován a nainstalován na DSVM? 
|Platforma|Umístění instalace ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/jiskra-X.X.X-bin-hadoopX.X|


Knihovny pro přístup k datům z úložiště objektů blob Azure nebo Azure Data Lake Storage pomocí knihoven microsoftm mmlsparku pro výuku strojových učení jsou předinstalované v $SPARK_HOME/sklenics. Tyto JARs se automaticky načítají při spuštění Spark. Ve výchozím nastavení spark používá data na místním disku. 

Pro instanci Spark na DSVM pro přístup k datům uloženým v úložišti objektů Blob nebo Azure Data Lake Storage je nutné vytvořit a nakonfigurovat `core-site.xml` soubor na základě šablony nalezené v $SPARK_HOME/conf/core-site.xml.template. Musíte mít také příslušná pověření pro přístup k úložišti objektů Blob a Azure Data Lake Storage. (Všimněte si, že soubory šablon používají zástupné symboly pro konfigurace úložiště objektů Blob a Azure Data Lake Storage.)

Podrobnější informace o vytváření přihlašovacích údajů služby Azure Data Lake Storage najdete v tématu Ověřování pomocí Azure [Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po zadání přihlašovacích údajů pro úložiště objektů Blob nebo Azure Data Lake Storage do souboru core-site.xml můžete odkazovat na data uložená v těchto zdrojích prostřednictvím předpony URI wasb:// nebo adl://.

