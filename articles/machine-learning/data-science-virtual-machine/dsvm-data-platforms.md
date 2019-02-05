---
title: Datové platformy pro virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Další informace o datové platformy a nástrojů podporovaných na virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 27e0deae9c35ad8fa00659e3e3e505cace6e9014
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733429"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Datové platformy podporované na virtuální počítač pro datové vědy

Na Data virtuálního počítače VĚDY umožňuje sestavovat analýzy pro širokou škálu datových platformách. Kromě rozhraní pro platformy vzdálených dat datové VĚDY poskytuje místní instanci pro rychlý vývoj a vytváření prototypů. 

Následují nástroji datové platformy podporované na datové VĚDY. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Co je to?   | Instance místní relační databáze      |
| Podporované DSVM edice      | Windows      |
| Typické použití      | Rychlý vývoj místně s menší datové sady <br/> Využívejte jazyk R v databázi   |
| Odkazy na ukázky      |    Malý vzorek New York City datové sady je načten do SQL database `nyctaxi`. <br/> Ukázka Jupyter s Microsoft R a analýza v databázi lze najít na:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Související nástroje na datové VĚDY       | SQL Server Management Studio <br/> Ovladače ODBC/JDBC<br/> modul pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> Edice SQL Server 2016 developer jde použít jenom pro vývojové a testovací účely. Musíte mít licenci nebo jeden z virtuálních počítačů SQL serveru ke spuštění v produkčním prostředí. 


### <a name="setup"></a>Nastavení

Databázový server je už nakonfigurovaná a souvisejících služeb Windows na SQL Server (například `SQL Server (MSSQLSERVER)`) jsou nastaveny na automatické spuštění. Pouze manuální krok běžet je umožnit analýza v databázi pomocí Microsoft R. Můžete to provést spuštěním následujícího příkazu jako jeden čas akce v SQL Server Management Studio (SSMS) po přihlásíte jako správce počítači otevřete "Nový dotaz" v aplikaci SSMS, ujistěte se, vybraná databáze není `master` a potom spusťte: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Chcete-li spustit SQL Server Management Studio, můžete vyhledat "SQL Server Management Studio" v seznamu programů nebo pomocí Windows Search můžete najít a spustit ho. Po zobrazení výzvy k zadání pověření, zvolte možnost "Windows Authentication" a použijte název počítače nebo ```localhost``` v názvu SQL serveru. 

### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?  

Databázový server pomocí výchozí instance databáze je ve výchozím nastavení automaticky spuštěn. Nástroje, jako je SQL Server Management Studio na virtuálním počítači můžete použít pro přístup k databázi systému SQL Server místně. Účet místní správci mají přístup správce v databázi. 

Také obsahuje datové VĚDY s ovladači ovladače rozhraní ODBC a JDBC ke komunikaci s SQL Server, Azure SQL Database a Azure SQL Data Warehouse z aplikace napsané v různých jazycích, včetně Python, r 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak ho nakonfigurovat či nainstalovaná na datové VĚDY? 

SQL Server je nainstalován ve standardním způsobem. Je možné najít v `C:\Program Files\Microsoft SQL Server`. Instance R v databázi se nachází v umístění `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Datové VĚDY také má nainstalovaný na instanci R Server samostatného `C:\Program Files\Microsoft\R Server\R_SERVER`. Tyto instance dvou R Nesdílejte knihoven.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (samostatně)

| | |
| ------------- | ------------- |
| Co je to?   | (Jeden uzel v procesu) samostatná Oblíbené platformy Apache Spark, systém pro rychlé rozsáhlé zpracování dat a strojové učení     |
| Podporované DSVM edice      | Linux <br /> Windows (experimentální)      |
| Typické použití      | * Rychlý vývoj aplikací Spark/PySpark místně s menší datové sady a později ji nasadit na velkých clusterech Spark, jako je například Azure HDInsight<br/> * Testování kontextu Sparku Microsoft R serveru <br />* Používat ve SparkML nebo společnosti Microsoft open source [MMLSpark](https://github.com/Azure/mmlspark) knihovny k sestavení aplikací ML  |
| Odkazy na ukázky      |    Jupyter ukázka: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (kontextu Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Související nástroje na datové VĚDY       | PySpark, Scala<br/>Jupyter (Spark/PySpark jader)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Jak jej používat
Spark můžete spustit odesláním Sparkových úloh v příkazovém řádku s `spark-submit` nebo `pyspark` příkazy. Poznámkový blok Jupyter můžete vytvořit také tak, že vytvoříte nový poznámkový blok s jádra Spark. 

Můžete použít Spark z R pomocí knihovny jako SparkR, Sparklyr nebo Microsoft R serveru, které jsou k dispozici na datové VĚDY. Zobrazit odkazy na ukázky v předchozí tabulce. 

> [!NOTE]
> Microsoft R serveru spuštěn v kontextu Spark DSVM je podporována pouze v edici Ubuntu Linux DSVM. 



### <a name="setup"></a>Nastavení
Před spuštěním v kontextu Sparku Microsoft R serveru na Ubuntu Linuxu DSVM edition, je potřeba jeden čas instalace krok umožňující místní Hadoop HDFS a Yarn instance jednoho uzlu. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Chcete-li ji povolit, je třeba spustit následující příkazy jako uživatel root při prvním:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Hadoop můžete zastavit a souvisejících služeb, když už nejsou potřeba spuštěním ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` ukázka ukazuje, jak vyvíjet a testovat paní ve vzdálené kontextu Spark (což je samostatné instanci Spark na datové VĚDY), je poskytovaná a k dispozici v `/dsvm/samples/MRS` adresář. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak ho nakonfigurovat či nainstalovaná na datové VĚDY? 
|Platforma|Nainstalujte umístění ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Knihovny pro přístup k datům z objektů Blob v Azure nebo Azure Data Lake storage (ADLS) a použití knihovny MMLSpark strojového učení od Microsoftu je předinstalován v $SPARK_HOME/JAR. Tyto kromě souborů JAR jsou automaticky načteny při spuštění Sparku. Ve výchozím nastavení používá Spark data na místním disku. Pro instanci Spark na přístup k datům uloženým na objektů blob v Azure nebo ADLS datové VĚDY je nutné vytvořit a nakonfigurovat `core-site.xml` soubor založený na šabloně v $SPARK_HOME/conf/core-site.xml.template (Pokud je zástupných symbolů pro objekt Blob a ADLS Konfigurace) se správnými přihlašovacími údaji do objektů blob v Azure a Azure Data Lake Storage. Najít další podrobný postup k vytvoření přihlašovacích údajů služby ADLS [tady](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Jakmile přihlašovací údaje pro Azure blob nebo ADLS byly zadány v souboru core-site.xml, odkazovat lze data uložená v těch zdrojích se identifikátor URI předponu wasb: / / nebo adl: / /. 

