---
title: Týmové projekty - Azure platformy a nástroje pro vědecké zpracování dat | Microsoft Docs
description: Rozepisuje a pojednává o prostředcích data a analýzy, které jsou k dispozici pro podniky standardizace v procesu Team dat vědecké účely.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: f447378816f137517a3d542a27841d0cab351473
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112676"
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Týmové projekty platformy a nástroje pro vědecké zpracování dat

Společnost Microsoft poskytuje úplné spektrum data a analýzy službám a prostředkům platforem cloudu nebo místně. Je lze nasadit, aby provádění projekty vědecké účely data efektivní a škálovatelné. Řídí pokyny pro implementaci datové vědy projekty v organizovaným týmy, verze, a poskytuje způsob spolupráce [proces vědecké účely dat Team](overview.md) (TDSP).  Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy team standardizace na tento proces, najdete v části [proces vědecké účely dat Team rolí a úloh](roles-tasks.md).

Služby data a analýzy, které jsou k dispozici týmů vědecké účely dat pomocí TDSP patří:

- Datové vědy virtuálních počítačů (Windows a Linux CentOS)
- Clustery HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clustery HDInsight Hive
- Azure File Storage
- SQL Server 2016 R Services

V tomto dokumentu jsme stručně popisují prostředky a obsahují odkazy na kurzy a návody týmy TDSP publikovali. Můžou vám Naučte se používat krok za krokem a začněte je používat k vytvoření aplikace inteligentního pomoct. Další informace na tyto prostředky jsou k dispozici na stránkách produktu. 

## <a name="data-science-virtual-machine-dsvm"></a>Datové vědy virtuálního počítače (DSVM)

Virtuální počítač datové vědy nabízí v systému Windows a Linux společností Microsoft, obsahuje oblíbených nástrojů pro vývoj a modelování aktivit vědecké účely dat. Obsahuje nástroje, jako:

- Microsoft R Server Developer Edition 
- Anaconda distribuci jazyka Python
- Poznámkové bloky Jupyter pro Python a R 
- Visual Studio Community Edition s Python a R nástrojů v systému Windows / Eclipse v systému Linux
- Power BI desktop pro Windows
- SQL Server 2016 Developer Edition v systému Windows nebo Postgres v systému Linux

Zahrnuje také **nástroje ML a AI** jako CNTK (nástrojů Microsoft otevřený zdroj hloubkové Learning), xgboost, mxnet a Vowpal k dispozici.

Aktuálně jsou k dispozici v DSVM **Windows** a **Linux CentOS** operační systémy. Zvolte velikost vašeho DSVM (počet jader procesoru) a velikost paměti na základě potřeb projektů vědecké účely dat, které máte v úmyslu provést na něm. 

Další informace o verzi systému Windows, DSVM najdete v tématu [virtuální počítač Microsoft Data vědecké účely](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) v Azure marketplace. Edice Linux DSVM, najdete v části [Linux datové vědy virtuální počítač](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Zjistěte, jak provést některé běžné úlohy, vědecké účely data DSVM efektivně, najdete v tématu [deset způsobů, jak na vědecké zpracování dat virtuálního počítače](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark clusterů

Apache Spark je open-source paralelní zpracování rozhraní, které podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je vytvořené pro rychlost, snadné použití a sofistikované analytics. Možnosti výpočtů v paměti Spark díky správnou volbu pro iterativní algoritmy v machine learning a grafů výpočty. Spark je také kompatibilní s Azure Blob storage (WASB), takže existující data uložená v Azure lze snadno zpracovat pomocí Spark.

Když vytvoříte cluster Spark v HDInsight, vytvoříte výpočetní prostředky Azure s nainstalovaným a nakonfigurovaným Spark. Vytvořte Spark cluster v HDInsight trvá přibližně 10 minut. Uložte data, která mají být zpracovány v Azure Blob storage. Informace o používání Azure Blob Storage s clusterem, najdete v tématu [použití HDFS kompatibilní úložiště Azure Blob s Hadoop v HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Tým TDSP od společnosti Microsoft byla publikována dvě začátku do konce návody o tom, jak použít clustery Spark HDInsight Azure k vytvoření řešení vědecké účely data, jeden používáním Pythonu a dalších Scala. Další informace o Azure HDInsight **clustery Spark**, najdete v části [přehled: Apache Spark v HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Další informace o sestavení pomocí řešení datové vědy **Python** v clusteru Azure HDInsight Spark, najdete v části [přehled o vědecké zpracování dat pomocí Spark v Azure HDInsight](spark-overview.md). Další informace o sestavení pomocí řešení datové vědy **Scala** v clusteru Azure HDInsight Spark, najdete v části [vědecké zpracování dat pomocí Scala a Spark v Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse umožňuje škálovat výpočetní prostředky bez předimenzování nebo přepsání platební snadno a v sekundách. Nabízí také jedinečný možnost pozastavit použití výpočetní prostředky, dává volnost lépe spravovat náklady na cloudu. Možnost nasazení škálovatelných výpočetních prostředků umožňuje uvést vaše data do Azure SQL Data Warehouse. Jsou minimální, náklady na úložiště a výpočetní lze spustit pouze na části datové sady, které chcete analyzovat. 

Další informace o Azure SQL Data Warehouse, najdete v článku [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webu. Naučte se vytvářet řešení pokročilou analýzu začátku do konce s SQL Data Warehouse, najdete v tématu [Team datové vědy procesu v akci: pomocí SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake je jako úložiště celopodnikové každý typ dat shromažďovaných na jednom místě, před žádné formální požadavky nebo schéma uložené. Tato možnost umožňuje každý typ dat, která se mají uchovat v data lake, bez ohledu na jeho velikost nebo struktura nebo jak rychle je konzumována. Organizace můžou pak pomocí Hadoop nebo vzory pokročilou analýzu najít v těchto datového jezera. Datového jezera může taky sloužit jako úložiště pro přípravu dat nižší náklady než Správa data a přesun do datového skladu.

Další informace o Azure Data Lake najdete v tématu [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Další postupy k vytvoření řešení vědecké účely škálovatelné začátku do konce dat s Azure Data Lake najdete v tématu [škálovatelné vědecké zpracování dat v Azure Data Lake: návod začátku do konce](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clustery HDInsight Hive Azure (Hadoop)

Apache Hive je systém datového skladu pro Hadoop, která umožňuje shrnutí dat, dotazy a analýzy dat pomocí HiveQL, podobně jako SQL dotazovací jazyk. Hive lze interaktivně zkoumat data nebo vytvořte znovu použitelné dávkové úlohy zpracování.

Hive umožňuje strukturu projektu na do značné míry Nestrukturovaná data. Po definování strukturu, můžete použít Hive k dotazování dat v clusteru Hadoop bez nutnosti použít, nebo i vědět, Java nebo MapReduce. HiveQL (jazyk dotazu Hive) umožňuje psát dotazy s příkazy, které jsou podobné T-SQL.

Pro datových vědců Hive v dotazů Hive ke zpracování záznamů spouštět Python User-Defined funkcí (UDF). Tato schopnost rozšiřuje možnosti dotazů Hive v analýzu dat podstatně. Konkrétně umožňuje datových vědců umožňuje provádět inženýrství škálovatelné funkce v jazycích, většinou se seznámíte s: HiveQL podobné jazyku SQL a Python. 

Další informace o Azure HDInsight Hive clustery najdete v tématu [použití Hive a HiveQL s Hadoop v HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Další postupy k vytvoření řešení vědecké účely škálovatelné začátku do konce dat s clustery HDInsight Hive Azure najdete v tématu [Team datové vědy procesu v akci: pomocí clusterů systému HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Úložiště Azure File je služba, která nabízí sdílené složky v cloudu přes standardní protokol Server Message Block (SMB). Podporují se SMB 2.1 i SMB 3.0. S Azure File Storage můžete rychle a bez nákladných přepisů migrovat starší aplikace, které spoléhají na sdílené složky, do Azure. Aplikace běžící v cloudových službách nebo virtuálních počítačích Azure nebo spuštěné z lokálních klientů můžou připojit sdílenou složku v cloudu stejným způsobem, jako desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Zvláště užitečná pro projekty vědecké účely dat je možnost vytvořit úložiště Azure file jako umístění pro sdílení dat projektu se členové týmu projektu. Každý z nich pak má přístup k stejnou kopii dat v úložišti Azure file. Může také používat úložiště tento soubor sdílet vygenerována během provádění projektu sady funkcí. Pokud je projekt engagement klienta, můžete vytvořit vaši klienti Azure file storage v rámci své vlastní předplatné sdílet data projektu a funkce s vámi. Tímto způsobem klient má oprávnění k úplnému řízení projektu datových prostředků. Další informace o Azure File Storage najdete v tématu [Začínáme s Azure File storage ve Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) a [postup používání Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R služby (v databázi) poskytují platformu pro vývoj a nasazení inteligentního aplikací, které můžete odhalovat nové souvislosti. Bohatý a výkonné R jazyk, včetně mnoha balíčků R Společenství, můžete použít k vytváření modelů a vytvářet předpovědi z dat systému SQL Server. Protože R služby (v databázi) integrované jazyka R se systémem SQL Server, jsou blízko data, která eliminuje náklady a bezpečnostních rizicích spojených s přesouvání dat v analytics.

R služby (v databázi) podporují jazyk R s otevřeným zdrojem s komplexní sadou nástrojů SQL serveru a technologie. Nabízí vyšší výkon, zabezpečení, spolehlivost a možnosti správy. Můžete nasadit řešení R pomocí pohodlný a známých nástrojů. Výrobní aplikace můžete volat R runtime a načtení předpovědi a vizuální prvky používající Transact-SQL. Je také použít ke zlepšení škálování a výkon řešení R ScaleR knihovny. Další informace najdete v tématu [R služby serveru SQL Server](https://msdn.microsoft.com/library/mt604845.aspx)

Týmem TDSP od společnosti Microsoft byla publikována dvě návody začátku do konce, které ukazují, jak sestavit řešení vědecké účely data ve službě SQL Server 2016 R: jeden pro programátory v jazyce R a jeden pro vývojáře SQL. Pro **programátory v jazyce R**, najdete v části [vědecké zpracování dat pro kompletní návod](https://msdn.microsoft.com/library/mt612857.aspx). Pro **SQL vývojáři**, najdete v části [v databázi pokročilé analýzy pro vývojáře SQL (kurz)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Dodatek: Nástroje pro nastavení dat vědecké účely projekty

### <a name="install-git-credential-manager-on-windows"></a>Instalace správce přihlašovacích údajů Git v systému Windows

Použijete-li TDSP **Windows**, je potřeba nainstalovat **Git přihlašovacích údajů správce (GCM)** ke komunikaci s úložiště Git. Pokud chcete nainstalovat GCM, musíte nejprve nainstalovat **Chocolaty**. Chcete-li nainstalovat Chocolaty a GCM, spusťte následující příkazy v prostředí Windows PowerShell jako **správce**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Nainstalovat Git na počítače se systémem Linux (CentOS)

Spusťte následující příkaz bash nainstalovat Git na počítače se systémem Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generování veřejného klíče SSH na počítače se systémem Linux (CentOS)

Pokud používáte počítače se systémem Linux (CentOS) ke spuštění příkazů git, budete muset přidat veřejný klíč SSH počítače na server služby VSTS, tak, aby tento počítač je serverem služby VSTS rozpoznán. Nejdřív musíte vygenerovat veřejný klíč SSH a přidejte klíč k veřejné klíče SSH na stránce nastavení zabezpečení služby VSTS. 

- Chcete-li vygenerovat klíč SSH, spusťte následující dva příkazy: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Zkopírujte celou ssh klíče včetně *ssh-rsa*. 
- Přihlaste se k serveru služby VSTS. 
- Klikněte na tlačítko **< název vašeho\>**  v pravém horním rohu stránky a klikněte na tlačítko **zabezpečení**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Klikněte na tlačítko **veřejné klíče SSH**a klikněte na tlačítko **+ přidat**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Vložení ssh klíč právě zkopírovali do textového pole a uložte.


## <a name="next-steps"></a>Další postup

Úplné návody začátku do konce, které ukazují všechny kroky v procesu pro **konkrétních scénářů** jsou také uvedeny. Jsou uvedena v seznamu a propojené s miniatur popisy v [příklad návody](walkthroughs.md) tématu. Se ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady provedením kroků v procesu Team dat. vědecké účely, které používají Azure Machine Learning Studio najdete v tématu [s Azure ML](http://aka.ms/datascienceprocess) kurzů.