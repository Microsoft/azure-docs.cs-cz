---
title: Platformy a nástroje pro projekty datové vědy - Team Data Science Process
description: Rozepisuje a popisuje datové a analytické prostředky, které jsou k dispozici podnikům standardizujícím proces vědecké analýzy týmových dat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3f3f06bd5a9b6a78b45a63de0684b580e662075b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256514"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy a nástroje pro projekty datové vědy

Společnost Microsoft poskytuje celé spektrum analytických prostředků pro cloudové i místní platformy. Mohou být nasazeny tak, aby provádění projektů datové vědy efektivní a škálovatelné. Pokyny pro týmy implementující projekty datové vědy sledovatelným, řízeným verzí a způsobem spolupráce poskytuje [proces vědecké vědy o týmových datech](overview.md) (TDSP).  Přehled rolí personálu a jejich přidružených úkolů, které jsou zpracovány týmem pro datové vědy, který tento proces standardizuje, naleznete v [tématu Role a úkoly procesu vědeckého zpracování týmových dat](roles-tasks.md).

Analytické prostředky, které jsou k dispozici pro týmy datových věd pomocí TDSP patří:

- Virtuální počítače pro datové vědy (Windows i Linux CentOS)
- Clustery HDInsight Spark
- Synapse Analytics
- Azure Data Lake
- Hive clustery HDInsight
- Azure File Storage
- Služby SQL Server 2019 R a Python
- Azure Databricks

V tomto dokumentu stručně popisujeme prostředky a poskytujeme odkazy na kurzy a návody, které týmy TDSP publikovaly. Mohou vám pomoci naučit se je používat krok za krokem a začít je používat k vytváření inteligentních aplikací. Více informací o těchto zdrojích naleznete na jejich produktových stránkách. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuální počítač pro datové vědy (DSVM)

Virtuální stroj pro datové vědy nabízený na Windows i Linux u microsoftu obsahuje oblíbené nástroje pro modelování a vývoj datových věd. Obsahuje nástroje, jako jsou:

- Microsoft R Server Developer Edition 
- Distribuce Anaconda Python
- Jupyter notebooky pro Python a R 
- Visual Studio Community Edition s pythonovými a r nástroji v systému Windows / Eclipse na Linuxu
- Desktop Power BI pro Windows
- SQL Server 2016 Developer Edition v systému Windows / Postgres na Linuxu

Obsahuje také **nástroje ML a AI** jako xgboost, mxnet a Vowpal Wabbit.

V současné době je DSVM k dispozici v operačních systémech **Windows** a **Linux CentOS.** Zvolte velikost vašeho DSVM (počet jader procesoru a velikost paměti) na základě potřeb projektů datové vědy, které plánujete provést na něm. 

Další informace o edici Windows dsvm najdete v [tématu Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) na Azure Marketplace. Linuxová edice DSVM najdete v tématu [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Informace o tom, jak efektivně provádět některé běžné úlohy datové vědy na dsvm, najdete v [tématu 10 věcí, které můžete dělat na virtuálním počítači pro datové vědy.](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clustery Azure HDInsight Spark

Apache Spark je opensource paralelní framework pro zpracování, který podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul pro zpracování Spark je navržen pro rychlost, snadné použití a sofistikované analýzy. Možnosti výpočtu v paměti Spark z něj dělají dobrou volbu pro iterativní algoritmy ve strojovém učení a pro výpočty grafů. Spark je taky kompatibilní s úložištěm objektů blob Azure (WASB), takže vaše stávající data uložená v Azure se dá snadno zpracovat pomocí Sparku.

Když vytvoříte cluster Spark v HDInsight, vytvoříte výpočetní prostředky Azure s nainstalovaným a nakonfigurovaným Spark. Vytvoření clusteru Spark ve službě HDInsight trvá přibližně 10 minut. Uložte data, která se mají zpracovávat, v úložišti objektů blob Azure. Informace o používání Azure Blob Storage s clusterem najdete v [tématu Použití úložiště objektů blob Azure kompatibilního s HDFS s Hadoopem v HDInsightu](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Tým TDSP od Microsoftu publikoval dva komplexní návody, jak používat clustery Azure HDInsight Spark k vytváření řešení pro datové vědy, z toho jedno pomocí Pythonu a druhé Scala. Další informace o clusterech Azure HDInsight **Spark najdete**v [tématu Přehled: Apache Spark na HDInsight Linuxu](../../hdinsight/spark/apache-spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Pythonu** v clusteru Azure HDInsight Spark, najdete v [tématu Přehled datových věd pomocí Spark na Azure HDInsight](spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Scaly** v clusteru Azure HDInsight Spark Cluster, najdete v [tématu Data Science using Scala and Spark on Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse umožňuje škálovat výpočetní prostředky snadno a během několika sekund, bez nadměrného zřizování nebo přeplácení. Nabízí také jedinečnou možnost pozastavit používání výpočetních prostředků, což vám dává svobodu lépe spravovat náklady na cloud. Možnost nasazení škálovatelných výpočetních prostředků umožňuje přenést všechna vaše data do Azure SQL Data Warehouse. Náklady na úložiště jsou minimální a výpočetní prostředky můžete spouštět pouze na částech datových sad, které chcete analyzovat. 

Další informace o Azure SQL Data Warehouse najdete na webu [SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse) Informace o tom, jak vytvořit komplexní pokročilá analytická řešení s datovým skladem SQL, najdete [v článku Proces vědecké analýzy týmových dat v akci: pomocí datového skladu SQL](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake je jako celopodnikové úložiště všech typů dat shromážděných na jednom místě před uložením jakýchkoli formálních požadavků nebo schématu. Tato flexibilita umožňuje uchovávat každý typ dat v datovém jezeře bez ohledu na jeho velikost nebo strukturu nebo rychlost jejich požití. Organizace pak mohou pomocí Hadoopu nebo pokročilé analýzy najít vzory v těchto datových jezerech. Datová jezera mohou také sloužit jako úložiště pro přípravu dat s nižšími náklady před curating data a jejich přesunutí do datového skladu.

Další informace o Azure Data Lake najdete [v tématu Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Informace o tom, jak vytvořit škálovatelné komplexní řešení pro datové vědy s Azure Data Lake, najdete [v tématu Scalable Data Science v Azure Data Lake: Komplexní návod](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clustery Azure HDInsight Hive (Hadoop)

Apache Hive je systém datového skladu pro Hadoop, který umožňuje sumarizování dat, dotazování a analýzu dat pomocí HiveQL, dotazovacího jazyka podobného SQL. Hive lze interaktivně prozkoumat vaše data nebo vytvořit opakovaně použitelné úlohy dávkového zpracování.

Hive umožňuje promítat strukturu na převážně nestrukturovaná data. Po definování struktury můžete použít Hive k dotazování na tato data v clusteru Hadoop bez nutnosti použití nebo dokonce vědět, Java nebo MapReduce. HiveQL (dotazovací jazyk Hive) umožňuje psát dotazy s příkazy, které jsou podobné T-SQL.

Pro datové vědce může Hive spouštět uživatelem definované funkce Pythonu (UD) v dotazech Hive ke zpracování záznamů. Tato možnost značně rozšiřuje možnosti dotazů Hive v analýze dat. Konkrétně umožňuje datovým vědcům provádět škálovatelné funkce inženýrství v jazycích, které jsou většinou obeznámeni s: SQL jako HiveQL a Python. 

Další informace o clusterech Hive Azure HDInsight najdete [v tématu Použití Hive a HiveQL s Hadoopem v HDInsightu](../../hdinsight/hadoop/hdinsight-use-hive.md). Informace o tom, jak vytvořit škálovatelné komplexní řešení pro datové vědy pomocí clusterů Azure HDInsight Hive, najdete [v tématu Proces vědecké analýzy týmových dat v akci: pomocí clusterů HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage je služba, která nabízí sdílené složky v cloudu pomocí standardního protokolu SMB (Server Message Block). Podporují se SMB 2.1 i SMB 3.0. S Azure File Storage můžete rychle a bez nákladných přepisů migrovat starší aplikace, které spoléhají na sdílené složky, do Azure. Aplikace běžící v cloudových službách nebo virtuálních počítačích Azure nebo spuštěné z lokálních klientů můžou připojit sdílenou složku v cloudu stejným způsobem, jako desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Zvláště užitečné pro projekty datové vědy je možnost vytvořit úložiště souborů Azure jako místo pro sdílení dat projektu se členy projektového týmu. Každý z nich pak má přístup ke stejné kopii dat v úložišti souborů Azure. Mohou také použít toto úložiště souborů ke sdílení sad funkcí generovaných během provádění projektu. Pokud je projekt klientem, vaši klienti můžou vytvořit úložiště souborů Azure v rámci vlastního předplatného Azure a sdílet s vámi data a funkce projektu. Tímto způsobem má klient plnou kontrolu nad datovými prostředky projektu. Další informace o Azure File Storage najdete [v tématu Začínáme s úložištěm souborů Azure ve Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) a jak používat Azure File Storage s [Linuxem](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Služby SQL Server 2019 R a Python

R Services (In-database) poskytuje platformu pro vývoj a nasazování inteligentních aplikací, které mohou odhalit nové poznatky. Můžete použít bohaté a výkonné jazyka R, včetně mnoha balíčků poskytovaných komunitou R, k vytvoření modelů a generování předpovědi z dat serveru SQL Server. Vzhledem k tomu, že R Services (In-database) integruje jazyk R se serverem SQL Server, analýzy jsou udržovány v blízkosti dat, což eliminuje náklady a bezpečnostní rizika spojená s přesunutím dat.

R Services (In-database) podporuje open source jazyk R s komplexní sadou nástrojů a technologií SQL Serveru. Nabízejí vynikající výkon, zabezpečení, spolehlivost a ovladatelnost. Řešení R můžete nasadit pomocí pohodlných a známých nástrojů. Produkční aplikace můžete volat runtime R a načíst předpovědi a vizuály pomocí Transact-SQL. Můžete také použít knihovny ScaleR ke zlepšení škálování a výkonu řešení R. Další informace naleznete v tématu [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Tým TDSP od společnosti Microsoft publikoval dva komplexní návody, které ukazují, jak vytvářet řešení pro datové vědy ve službách SQL Server 2016 R: jeden pro programátory R a jeden pro vývojáře SQL. Programátoři **R**naleznete [v tématu Data Science End-to-End Walkthrough](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Informace **o vývojářích SQL**naleznete v [tématu In-Database Advanced Analytics for SQL Developers (tutorial)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Dodatek: Nástroje pro nastavení projektů datové vědy

### <a name="install-git-credential-manager-on-windows"></a>Instalace Správce přihlašovacích údajů Gitu ve Windows

Pokud sledujete tdsp v **systému Windows**, je třeba nainstalovat **Git Credential Manager (GCM)** pro komunikaci s úložišti Git. Chcete-li nainstalovat GCM, musíte nejprve nainstalovat **Chocolaty**. Chcete-li nainstalovat program Chocolaty a gcm, spusťte v prostředí Windows PowerShell následující příkazy jako **správce**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalace gitu na linuxové počítače (CentOS)

Spusťte následující příkaz bash pro instalaci gitu na linuxové počítače (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generování veřejného SSH klíče na linuxových počítačích (CentOS)

Pokud používáte počítače S IP (CentOS) ke spuštění příkazů git, musíte přidat veřejný klíč SSH vašeho počítače do služby Azure DevOps, aby tento počítač byl rozpoznán službami Azure DevOps Services. Nejprve je třeba vygenerovat veřejný klíč SSH a přidat klíč do veřejných klíčů SSH na stránce nastavení zabezpečení služby Azure DevOps Services. 

1. Chcete-li generovat klíč SSH, spusťte následující dva příkazy: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Příkazy pro generování klíče SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Zkopírujte celý klíč ssh včetně *ssh-rsa*. 
1. Přihlaste se ke službám Azure DevOps. 
1. V pravém horním rohu stránky klikněte na **<vaše jméno\> ** a klikněte na **zabezpečení**. 
    
   ![Klikněte na své jméno a potom na zabezpečení.](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klepněte na **položku Veřejné klíče SSH**a klepněte na tlačítko **+Přidat**. 

   ![Klikněte na veřejné klíče SSH a potom na +Přidat.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Vložte klávesu ssh zkopírovanou do textového pole a uložte ji.


## <a name="next-steps"></a>Další kroky

K dispozici jsou také úplné komplexní návody, které ukazují všechny kroky v procesu pro **konkrétní scénáře.** Jsou uvedeny a propojeny s popisy miniatur v tématu [Příklad návody.](walkthroughs.md) Ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, které ukazují, jak provést kroky v procesu vědecké ho týmových dat pomocí Azure Machine Learning Studio (klasické), najdete v tématu [s Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) studijní ho.
