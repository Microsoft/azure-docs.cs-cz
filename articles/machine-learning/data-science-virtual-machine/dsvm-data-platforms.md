---
title: Platformy dat pro datové vědy virtuálního počítače – Azure | Microsoft Docs
description: Data platformy pro virtuální počítač vědecké účely Data.
keywords: datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: c9c85ebed6382a4188db028fbfb35675751bce76
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="data-platforms"></a>Datové platformy

Virtuální počítač vědecké účely dat (DSVM) umožňuje vytvoření analytické údaje proti širokou škálu data platformy. Kromě rozhraní na vzdálených dat platformy DSVM poskytuje místní instance rychlý vývoj a při vytváření prototypu. 

Níže jsou nástroje platformy data na DSVM podporována. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Co je to?   | Instance místní relační databáze      |
| Podporované DSVM edice      | Windows      |
| Typické použití      | Rychlý vývoj místně s menší datové sady <br/> Spustit v databázi R   |
| Odkazy na ukázky      |    Malé ukázkové sady dat New Yorku je načten do databáze SQL `nyctaxi`. <br/> Ukázka Jupyter zobrazující Microsoft R a v databázi analýzy najdete tady:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Na DSVM souvisejících nástrojích       | SQL Server Management Studio <br/> Rozhraní ODBC/JDBC ovladače<br/> pyodbc RODBC<br />Apache procházení      |

> [!NOTE]
> Edice SQL serveru 2016 vývojáře slouží pouze pro testovací účely vývoje a. Potřebujete licence nebo jednoho z virtuálních počítačů SQL serveru ke spuštění v produkčním prostředí. 


### <a name="setup"></a>Nastavení

Databázový server je už předem nakonfigurované a související služby systému Windows k systému SQL Server (například `SQL Server (MSSQLSERVER)`) jsou nastaveny na automatické spuštění. Pouze manuální krok ke spuštění je umožnit analytics v databázi pomocí Microsoft R. Můžete to udělat tak, že spustíte následující příkaz jednou akce v serveru SQL Server Management Studio (SSMS) po přihlásíte jako správce počítače Open "Nový dotaz" v aplikaci SSMS, ujistěte se, vybraná databáze není `master` a spusťte: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Aby bylo možné spustit SQL Server Management Studio, můžete hledat "SQL Server Management Studio" v seznamu programů nebo pomocí Windows Search můžete najít a spustit ho. Po zobrazení výzvy k zadání pověření, vyberte "Ověřování systému Windows" a použijte název počítače nebo ```localhost``` v názvu SQL serveru. 

### <a name="how-to-use--run-it"></a>Jak se použít nebo ji spustit?  

Databázový server pomocí výchozí instance databáze je ve výchozím nastavení automaticky spuštěna. Nástroje, například SQL Server Management Studio můžete ve virtuálním počítači pro přístup k místní databázi systému SQL Server. Účet místní správci mají přístup správce v databázi. 

Také DSVM součástí ovladače ODBC – ovladače a JDBC, aby komunikoval s SQL Server, databáze Azure SQL a Azure SQL Data Warehouse z aplikace napsané v různých jazycích včetně Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak je ho nakonfigurovaná a nainstalovaná na DSVM? 

SQL Server je nainstalován ve standardním způsobem. Se nachází v `C:\Program Files\Microsoft SQL Server`. Instance R v databázi se nachází zde `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM má také samostatné samostatnou instanci serveru R nainstalované v `C:\Program Files\Microsoft\R Server\R_SERVER`. Tyto dvě R instance nesdílejí knihovny.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (samostatně)

| | |
| ------------- | ------------- |
| Co je to?   | Samostatné (jeden uzel v procesu) instance Oblíbené platformy Apache Spark, systém pro rychlé rozsáhlé zpracování dat a strojové učení     |
| Podporované DSVM edice      | Linux <br /> Windows (Experimental)      |
| Typické použití      | * Rychlý vývoj aplikací Spark/PySpark místně s menší datové sady a později ho nasadit na velkých clusterech Spark, jako je například Azure HDInsight<br/> * Kontextu Spark serveru Microsoft R testu <br />* Použití SparkML nebo společnosti Microsoft otevřený zdroj [MMLSpark](https://github.com/Azure/mmlspark) knihovny sestavovat aplikace ML  |
| Odkazy na ukázky      |    Ukázka Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Server Microsoft R (Spark kontextu): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Na DSVM souvisejících nástrojích       | PySpark, Scala<br/>Jupyter (Spark/PySpark jádra)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache procházení      |

### <a name="how-to-use-it"></a>Způsobu jeho použití
Můžete spouštět Spark a odesílání úloh Spark na příkazovém řádku s `spark-submit` nebo `pyspark` příkazy. Můžete také vytvořit poznámkového bloku Jupyter tak, že vytvoříte nový poznámkový blok s jádrem Spark. 

Můžete použít Spark z R pomocí knihovny jako SparkR, Sparklyr nebo R Server společnosti Microsoft, které jsou dostupné na DSVM. Najdete ukazatele na ukázky v předchozí tabulce. 

> [!NOTE]
> Systémem Microsoft R Server v kontextu Spark DSVM je podporována pouze v edici Ubuntu Linux DSVM. 



### <a name="setup"></a>Nastavení
Před spuštěním v kontextu Spark Microsoft R Server na verzi Ubuntu Linux DSVM, budete muset jednou instalační program tak, aby Povolit místní Hadoop HDFS a Yarn instance jednoho uzlu. Ve výchozím nastavení jsou služby Hadoop nainstalován, ale na DSVM zakázáno. Chcete-li ji povolit, budete muset spustit následující příkazy jako kořenová při prvním:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Můžete zastavit Hadoop související služby, pokud není nutné spuštěním ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` vzorku, který ukazuje, jak pro vývoj a testování paní v vzdálené kontextu Spark (což je samostatnou instanci Spark na DSVM) je zadaná a k dispozici v `/dsvm/samples/MRS` adresář. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak je ho nakonfigurovaná a nainstalovaná na DSVM? 
|Platforma|Nainstalujte umístění ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Knihovny přístup k datům z Azure Blob nebo úložiště Azure Data Lake (ADLS) a použití knihoven MMLSpark strojové učení společnosti Microsoft jsou předinstalován v $SPARK_HOME/JAR. Tyto JAR se načítají automaticky při spuštění Spark. Ve výchozím nastavení používá Spark data na místním disku. V pořadí pro instanci Spark na DSVM přístup k datům uloženým v objektech blob v Azure nebo ADLS, budete muset vytvořit nebo konfigurovat `core-site.xml` souboru založený na šabloně najít v $SPARK_HOME/conf/core-site.xml.template (kde je zástupné symboly pro objekt Blob a ADLS Konfigurace) se správnými přihlašovacími údaji do objektů blob v Azure a Azure Data Lake Storage. Najít další podrobné kroky k vytvoření pověření služby ADLS [zde](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Po zadání přihlašovacích údajů pro objektů blob v Azure nebo ADLS v souboru core-site.xml, můžete odkazovat data uložená v těchto zdrojích s URI předponu wasb: / / nebo adl: / /. 

