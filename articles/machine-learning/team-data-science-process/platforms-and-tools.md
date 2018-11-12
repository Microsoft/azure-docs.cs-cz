---
title: Platformy a nástroje pro datové vědy týmové projekty – Azure | Dokumentace Microsoftu
description: Najdete výčet a popisuje prostředky dat a analýz, které jsou dostupné pro podniky standardizují používáním vědecké zpracování týmových dat.
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
ms.openlocfilehash: 5cfe86414473f352673f39850a34f8ef265c91f5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258897"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy a nástroje pro projekty datových věd

Společnost Microsoft poskytuje komplexní opensourcová data a analýzy služby a prostředky pro na místní i cloudové platformy. Je možné nasadit efektivního a škálovatelného provést spuštění vaše projekty datových věd. Pokyny pro týmy, které implementují projekty datových věd v organizovaným verzi řízen a je poskytována spolupráce [vědecké zpracování týmových dat](overview.md) (TDSP).  Přehled rolí pracovníky a jejich přidružených úloh, které jsou zpracovávány standardizací data science týmu o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md).

Data a analýzy služby k dispozici pro datové vědy týmy použití TDSP patří:

- Virtuální počítače pro datové vědy (Windows i Linux CentOS)
- Clustery HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clustery HDInsight Hive
- Azure File Storage
- SQL Server 2016 R Services

V tomto dokumentu jsme stručně popisují prostředky a poskytují odkazy na kurzy a názorné postupy publikování TDSP týmy. Že vám můžou pomoct při tom, jak využít krok za krokem a začít používat k sestavování inteligentních aplikací. Další informace o těchto prostředků je k dispozici na stránkách produktu. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuální počítač pro datové vědy (DSVM)

Virtuální počítač pro datové vědy nabízíme na Windows i Linuxem microsoftem, obsahuje oblíbené nástroje pro datové vědy modelování a vývojové aktivity. Obsahuje nástroje, jako:

- Microsoft R Server Developer Edition 
- Anacondu, distribuci jazyka Python
- Poznámkové bloky Jupyter pro Python a r. 
- Visual Studio Community Edition pomocí Pythonu a nástroje jazyka R na Windows / Eclipse v Linuxu
- Power BI desktopu pro Windows
- SQL Server 2016 Developer Edition na Windows / Postgres v Linuxu

Zahrnuje také **nástroje ML a AI** jako CNTK (otevřený zdroj obsáhlého learningu sada nástrojů od Microsoftu), xgboost, mxnet a Vowpal Wabbit.

Aktuálně je k dispozici v DSVM **Windows** a **Linux CentOS** operačních systémů. Vyberte velikost vašeho DSVM (počet jader procesoru) a velikost paměti na základě potřeb projekty datových věd, které máte v úmyslu spustit na ní. 

Další informace o edici Windows DSVM najdete v tématu [virtuálního počítače Microsoft Data Science](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) na Azure marketplace. Edice systému Linux datové VĚDY, naleznete v tématu [virtuální počítač s Linuxem Data Science](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Zjistěte, jak efektivně některé z běžných úloh datových věd zpracování pro datové VĚDY, najdete v článku [deset věcí, které můžete provést na virtuální počítač pro datovou vědu](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clustery Azure HDInsight Spark

Apache Spark je open source paralelní zpracování architektura, která podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Možnosti výpočtů v paměti Spark usnadňují dobrou volbou pro iterativní algoritmy ve službě machine learning a grafů výpočty. Spark je také kompatibilní s Azure Blob storage (WASB), takže svá stávající data uložená v Azure lze snadno zpracovat pomocí Sparku.

Když vytvoříte cluster Spark v HDInsight, vytvoříte výpočetní prostředky Azure s nainstalovaným a nakonfigurovaným Spark. Vytvoření clusteru Spark v HDInsight trvá asi 10 minut. Store data ke zpracování ve službě Azure Blob storage. Informace o používání služby Azure Blob Storage s clusterem, naleznete v tématu [použití HDFS kompatibilního úložiště Azure Blob s Hadoop v HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP týmu společnosti Microsoft publikovala dvě začátku do konce návody, jak používat clustery Azure HDInsight Spark a vytvářet tak řešení datové vědy, jeden pomocí Pythonu a dalších jazyků Scala. Další informace o Azure HDInsight **clustery Spark**, naleznete v tématu [přehled: Apache Spark v HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Další informace o sestavení pomocí řešení datové vědy **Python** na Azure HDInsight Spark Cluster, naleznete v tématu [přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md). Další informace o sestavení pomocí řešení datové vědy **Scala** na Azure HDInsight Spark Cluster, naleznete v tématu [vědecké zkoumání dat pomocí Scala a Spark v Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse umožňuje škálovat výpočetní prostředky jednoduše a během několika sekund, aniž by bylo potřeba zřizovat nebo platit cokoli navíc. Také nabízí jedinečnou možnost pozastavit využívání výpočetních prostředků, vám dá volnost, aby lépe spravovaly nákladů na cloud. Možnost nasadit škálovatelnou výpočetní prostředky, umožňuje mít všechna data do Azure SQL Data Warehouse. Jsou minimální náklady na úložiště a výpočetní prostředky lze použít pouze v části datové sady, které chcete analyzovat. 

Další informace o Azure SQL Data Warehouse, najdete v článku [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webu. Zjistěte, jak sestavit začátku do konce Pokročilá analytická řešení s využitím SQL Data Warehouse, najdete v článku [vědecké zpracování týmových dat v akci: použití služby SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake je jako je úložiště na podnikové úrovni všechny typy dat soustředěné na jednom místě, před žádné formální požadavků nebo schématu se uložila. Díky této flexibilitě umožňuje všechny typy dat, které budou uloženy v data lake, bez ohledu na její velikost nebo strukturu nebo rychlost zpracování. Organizace pak může použít Hadoop nebo pokročilou analýzu k vyhledání vzory v těchto datech jezera. Datová jezera současně může sloužit jako úložiště pro přípravu dat s nižšími náklady než Správa dat a jeho přesunutím do datového skladu.

Další informace o Azure Data Lake najdete v tématu [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Zjistěte, jak sestavit řešení škálovatelné začátku do konce datové vědy s Azure Data Lake, najdete v článku [škálovatelné vědecké zkoumání dat ve službě Azure Data Lake: návod začátku do konce](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clustery Azure HDInsight Hive (Hadoop)

Apache Hive se systémem datového skladu pro Hadoop, která umožňuje souhrnů dat, dotazování a analýze dat pomocí HiveQL dotazovací jazyk podobný SQL. Hive je možné pro interaktivní prozkoumávání dat a vytvářet opakovaně použitelné dávkové zpracování úlohy.

Struktura projektu do značné míry nestrukturovaných dat vám umožní Hive. Potom co definujete strukturu, můžete bez nutnosti použít, nebo ani vědět, Java nebo MapReduce dotazování těchto dat v clusteru Hadoop Hive. HiveQL (jazyk dotazu Hive) umožňuje psát dotazy s příkazy, které jsou podobné T-SQL.

Pro odborníky přes data Hive v dotazů Hive ke zpracování záznamů spouštět Python User-Defined funkce (UDF). Tato schopnost rozšiřuje možnosti dotazů Hive v data analysis výrazně. Konkrétně umožňuje datovým vědcům provádět vytváření škálovatelných funkcí v jazyce jsou většinou znáte: HiveQL podobném SQL a Pythonu. 

Další informace o clusterech Azure HDInsight Hive, najdete v části [použití Hive a HiveQL s Hadoop v HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Zjistěte, jak sestavit řešení škálovatelné začátku do konce datové vědy s Azure HDInsight Hive clustery, najdete v článku [vědecké zpracování týmových dat v akci: clusterů systému HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage je služba, která nabízí sdílené složky v cloudu přes standardní protokol zprávy bloku SMB (Server). Podporují se SMB 2.1 i SMB 3.0. S Azure File Storage můžete rychle a bez nákladných přepisů migrovat starší aplikace, které spoléhají na sdílené složky, do Azure. Aplikace běžící v cloudových službách nebo virtuálních počítačích Azure nebo spuštěné z lokálních klientů můžou připojit sdílenou složku v cloudu stejným způsobem, jako desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Užitečné zejména pro projekty datových věd je schopnost vytvářet úložiště služby Azure file jako místem, kde můžete sdílet data projektu s členy svého týmu projektu. Každý z nich pak má přístup k stejnou kopii dat ve službě Azure file storage. Tento soubor úložiště lze také použít ke sdílení sady funkcí, které jsou generovány během spuštění projektu. Pokud je projekt engagement klienta, můžete vytvořit vaši klienti služby Azure file storage v rámci svého vlastního předplatného Azure sdílet data projektu a funkcí s vámi. Tímto způsobem klient má plnou kontrolu nad projektu datové assety. Další informace o Azure File Storage najdete v tématu [Začínáme s Azure File storage ve Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) a [postupy používání Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

Služby R (v databázi) poskytují model platforma pro vývoj a nasazování inteligentních aplikací, které můžete odhalit nové poznatky. Bohaté a výkonné jazyka R, včetně mnoha balíčků poskytovaných komunitou R, můžete vytvářet modely a generovat předpovědi ze svých dat serveru SQL Server. Protože služby R (v databázi) integrace jazyka R s SQL serverem, analytics zachovány blízko data, která se eliminují náklady a rizika zabezpečení, které jsou spojené s přesunem dat.

Služby R (v databázi) podporují jazyk opensourcového jazyka R s komplexní sadu nástrojů SQL serveru a technologie. Nabízejí špičkový výkon, zabezpečení, spolehlivost a možnosti správy. Můžete nasadit řešení R s využitím praktické a známé nástroje. Vaše produkční aplikace můžete volat modulem runtime r. a načíst předpovědi a vizuály s použitím příkazů jazyka Transact-SQL. Pomocí knihoven ScaleR také zvýšit výkon svých řešení R a škálování. Další informace najdete v tématu [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Tým TDSP od Microsoftu publikoval dvě začátku do konce návody, které ukazují, jak vytvářet řešení pro vědu dat ve službě SQL Server 2016 R Services: jeden pro programátorům v R a jeden pro vývojáře SQL. Pro **programátorům v R**, naleznete v tématu [návod začátku do konce pro datovou vědu](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Pro **vývojáři SQL**, naleznete v tématu [v databázi pokročilé analýzy pro vývojáře SQL (kurz)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Dodatek: Nástroje pro nastavení projekty datových věd

### <a name="install-git-credential-manager-on-windows"></a>Nainstalovat Git Credential Manageru na Windows

Pokud postupujete TDSP **Windows**, je potřeba nainstalovat **Git Credential Manageru (GCM)** ke komunikaci s úložišti Git. Pokud chcete nainstalovat GCM, musíte nejprve nainstalovat **Chocolaty**. Pokud chcete nainstalovat Chocolaty a služby GCM, spusťte následující příkazy v prostředí Windows PowerShell jako **správce**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Nainstalovat Git na počítačích s Linuxem (CentOS)

Spusťte následující příkaz prostředí bash instalace Git na počítačích s Linuxem (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generovat veřejný klíč SSH na počítačích s Linuxem (CentOS)

Pokud používáte počítače s Linuxem (CentOS) ke spuštění příkazů git, musíte přidat veřejný klíč SSH z vašeho počítače do služby Azure DevOps tak, aby tento počítač je rozpoznáván DevOps služby Azure. Nejprve musíte vygenerovat veřejný klíč SSH a přidejte klíč k veřejné klíče SSH na stránce nastavení zabezpečení služby Azure DevOps. 

- Vygenerujte klíč SSH, spusťte následující dva příkazy: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Zkopírujte celý ssh včetně klíče *ssh-rsa*. 
- Přihlaste se k vašim službám Azure DevOps. 
- Klikněte na tlačítko **< jméno\>**  v pravém horním rohu stránky a klikněte na tlačítko **zabezpečení**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Klikněte na tlačítko **veřejné klíče SSH**a klikněte na tlačítko **+ přidat**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Vložit ssh klíč zkopírovali do textového pole a uložení.


## <a name="next-steps"></a>Další postup

Úplné začátku do konce návody, které ukazují všechny kroky v procesu pro **konkrétních scénářů** jsou také k dispozici. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) tématu. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady provádět kroky v vědecké zpracování týmových dat, které používají Azure Machine Learning Studio, najdete v článku [pomocí Azure ML](https://aka.ms/datascienceprocess) výuky.