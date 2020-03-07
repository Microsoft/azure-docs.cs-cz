---
title: Platformy a nástroje pro projekty datových věd - vědecké zpracování týmových dat
description: Najdete výčet a popisuje prostředky dat a analýz, které jsou dostupné pro podniky standardizují používáním vědecké zpracování týmových dat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391288"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy a nástroje pro projekty datových věd

Microsoft poskytuje kompletní spektrum analytických prostředků pro cloudové i místní platformy. Je možné nasadit efektivního a škálovatelného provést spuštění vaše projekty datových věd. Pokyny pro týmy, které implementují projekty pro datové vědy v rámci sledování, správy verzí a způsobu spolupráce, zajišťuje [vědecký proces týmového zpracování dat](overview.md) (TDSP).  Pro osnovu rolí personálu a jejich přidružených úloh, které jsou zpracovávány týmem pro datové vědy, které se týkají tohoto procesu, najdete informace v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

K dispozici jsou analytické prostředky pro datové vědy týmu, které používají TDSP:

- Virtuální počítače pro datové vědy (Windows i Linux CentOS)
- Clustery HDInsight Spark
- Synapse Analytics
- Azure Data Lake
- Clustery HDInsight Hive
- Azure File Storage
- Služby SQL Server 2019 R a Python
- Azure Databricks

V tomto dokumentu jsme stručně popisují prostředky a poskytují odkazy na kurzy a názorné postupy publikování TDSP týmy. Že vám můžou pomoct při tom, jak využít krok za krokem a začít používat k sestavování inteligentních aplikací. Další informace o těchto prostředků je k dispozici na stránkách produktu. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuální počítač pro datové vědy (DSVM)

Virtuální počítač pro datové vědy nabízíme na Windows i Linuxem microsoftem, obsahuje oblíbené nástroje pro datové vědy modelování a vývojové aktivity. Obsahuje nástroje, jako:

- Microsoft R Server Developer Edition 
- Anacondu, distribuci jazyka Python
- Poznámkové bloky Jupyter pro Python a r. 
- Visual Studio Community Edition pomocí Pythonu a nástroje jazyka R na Windows / Eclipse v Linuxu
- Power BI desktopu pro Windows
- SQL Server 2016 Developer Edition na Windows / Postgres v Linuxu

Zahrnuje také **nástroje ml a AI** , jako jsou xgboost, Mxnet a pro dostupné.

V současné době je DSVM k dispozici v operačních systémech **Windows** a **Linux CentOS** . Vyberte velikost vašeho DSVM (počet jader procesoru) a velikost paměti na základě potřeb projekty datových věd, které máte v úmyslu spustit na ní. 

Další informace o edici Windows DSVM najdete v části [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) na Azure Marketplace. Informace o edici DSVM pro Linux najdete v tématu [linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Další informace o tom, jak efektivně provádět některé běžné úkoly pro datové vědy na DSVM, najdete v tématu [10 věcí, které můžete dělat na virtuálním počítači pro datové vědy](../data-science-virtual-machine/vm-do-ten-things.md) .


## <a name="azure-hdinsight-spark-clusters"></a>Clustery Azure HDInsight Spark

Apache Spark je open source paralelní zpracování architektura, která podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Možnosti výpočtů v paměti Spark usnadňují dobrou volbou pro iterativní algoritmy ve službě machine learning a grafů výpočty. Spark je také kompatibilní s Azure Blob storage (WASB), takže svá stávající data uložená v Azure lze snadno zpracovat pomocí Sparku.

Když vytvoříte cluster Spark v HDInsight, vytvoříte výpočetní prostředky Azure s nainstalovaným a nakonfigurovaným Spark. Vytvoření clusteru Spark v HDInsight trvá asi 10 minut. Store data ke zpracování ve službě Azure Blob storage. Informace o používání služby Azure Blob Storage s clusterem najdete v tématu [použití úložiště objektů blob Azure kompatibilního se systémem Hadoop v HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP týmu společnosti Microsoft publikovala dvě začátku do konce návody, jak používat clustery Azure HDInsight Spark a vytvářet tak řešení datové vědy, jeden pomocí Pythonu a dalších jazyků Scala. Další informace o **clusterech**Azure HDInsight Spark najdete v tématu [Přehled: Apache Spark v prostředí HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Pythonu** na Azure HDInsight Sparkm clusteru, najdete v tématu [Přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Scala** v clusteru Azure HDInsight Spark, najdete v tématu věnovaném [datové vědy pomocí Scala a Sparku v Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse umožňuje škálovat výpočetní prostředky jednoduše a během několika sekund, aniž by bylo potřeba zřizovat nebo platit cokoli navíc. Také nabízí jedinečnou možnost pozastavit využívání výpočetních prostředků, vám dá volnost, aby lépe spravovaly nákladů na cloud. Možnost nasadit škálovatelnou výpočetní prostředky, umožňuje mít všechna data do Azure SQL Data Warehouse. Jsou minimální náklady na úložiště a výpočetní prostředky lze použít pouze v části datové sady, které chcete analyzovat. 

Další informace o Azure SQL Data Warehouse najdete na webu [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) . Další informace o tom, jak pomocí SQL Data Warehouse sestavit ucelená řešení pro pokročilé analýzy, najdete v článku [vědecké zpracování týmových dat v akci: použití SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake je úložiště všech typů dat shromážděných v rámci jednoho podniku před všemi formálními požadavky nebo podle toho, jaké schéma se ukládá. Díky této flexibilitě umožňuje všechny typy dat, které budou uloženy v data lake, bez ohledu na její velikost nebo strukturu nebo rychlost zpracování. Organizace pak může použít Hadoop nebo pokročilou analýzu k vyhledání vzory v těchto datech jezera. Datová jezera současně může sloužit jako úložiště pro přípravu dat s nižšími náklady než Správa dat a jeho přesunutím do datového skladu.

Další informace o Azure Data Lake najdete v tématu [Úvod do Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Další informace o tom, jak pomocí Azure Data Lake vytvořit škálovatelné řešení pro datové vědy, najdete v článku [škálovatelné vědecké zpracování dat v Azure Data Lake: ucelený návod](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clustery Azure HDInsight Hive (Hadoop)

Apache Hive se systémem datového skladu pro Hadoop, která umožňuje souhrnů dat, dotazování a analýze dat pomocí HiveQL dotazovací jazyk podobný SQL. Hive je možné pro interaktivní prozkoumávání dat a vytvářet opakovaně použitelné dávkové zpracování úlohy.

Struktura projektu do značné míry nestrukturovaných dat vám umožní Hive. Potom co definujete strukturu, můžete bez nutnosti použít, nebo ani vědět, Java nebo MapReduce dotazování těchto dat v clusteru Hadoop Hive. HiveQL (jazyk dotazu Hive) umožňuje psát dotazy s příkazy, které jsou podobné T-SQL.

Pro odborníky přes data Hive v dotazů Hive ke zpracování záznamů spouštět Python User-Defined funkce (UDF). Tato schopnost rozšiřuje možnosti dotazů Hive v data analysis výrazně. Konkrétně umožňuje datovým vědcům provádět vytváření škálovatelných funkcí v jazyce jsou většinou znáte: HiveQL podobném SQL a Pythonu. 

Další informace o clusterech podregistrů Azure HDInsight najdete v tématu [použití podregistru a HiveQL se systémem Hadoop ve službě HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Další informace o tom, jak vytvořit škálovatelné řešení pro datové vědy s clustery Azure HDInsight, najdete v článku [vědecké zpracování týmových dat v akci: používání clusterů HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage je služba, která nabízí sdílené složky v cloudu přes standardní protokol zprávy bloku SMB (Server). Podporují se SMB 2.1 i SMB 3.0. S Azure File Storage můžete rychle a bez nákladných přepisů migrovat starší aplikace, které spoléhají na sdílené složky, do Azure. Aplikace běžící v cloudových službách nebo virtuálních počítačích Azure nebo spuštěné z lokálních klientů můžou připojit sdílenou složku v cloudu stejným způsobem, jako desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Užitečné zejména pro projekty datových věd je schopnost vytvářet úložiště služby Azure file jako místem, kde můžete sdílet data projektu s členy svého týmu projektu. Každý z nich pak má přístup k stejnou kopii dat ve službě Azure file storage. Tento soubor úložiště lze také použít ke sdílení sady funkcí, které jsou generovány během spuštění projektu. Pokud je projekt engagement klienta, můžete vytvořit vaši klienti služby Azure file storage v rámci svého vlastního předplatného Azure sdílet data projektu a funkcí s vámi. Tímto způsobem klient má plnou kontrolu nad projektu datové assety. Další informace o Azure File Storage najdete v tématu Začínáme [se službou Azure File Storage ve Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) a [používání Azure File Storage se systémem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Služby SQL Server 2019 R a Python

Služba R (v databázi) poskytuje platformu pro vývoj a nasazování inteligentních aplikací, které můžou odhalit nové poznatky. Bohaté a výkonné jazyka R, včetně mnoha balíčků poskytovaných komunitou R, můžete vytvářet modely a generovat předpovědi ze svých dat serveru SQL Server. Vzhledem k tomu, že služba R (v databázi) integruje jazyk R s SQL Server, jsou analýzy udržovány blízko dat, což eliminuje náklady a rizika zabezpečení související s přesunem dat.

Služba r Services (v databázi) podporuje open source jazyk R s komplexní sadou SQL Serverch nástrojů a technologií. Nabízejí špičkový výkon, zabezpečení, spolehlivost a možnosti správy. Můžete nasadit řešení R s využitím praktické a známé nástroje. Vaše produkční aplikace můžete volat modulem runtime r. a načíst předpovědi a vizuály s použitím příkazů jazyka Transact-SQL. Pomocí knihoven ScaleR také zvýšit výkon svých řešení R a škálování. Další informace najdete v tématu [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Tým TDSP od Microsoftu publikoval dvě začátku do konce návody, které ukazují, jak vytvářet řešení pro vědu dat ve službě SQL Server 2016 R Services: jeden pro programátorům v R a jeden pro vývojáře SQL. **Programátory R**najdete v článku [komplexní návody pro datové vědy](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Pro **vývojáře v SQL**najdete informace [v článku rozšířené analýzy pro vývojáře SQL (kurz)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Příloha: nástroje pro nastavení projektů pro datové vědy

### <a name="install-git-credential-manager-on-windows"></a>Nainstalovat Git Credential Manageru na Windows

Pokud používáte TDSP ve **Windows**, musíte si nainstalovat **Git Credential Manager (GCM)** , abyste mohli komunikovat s úložišti Git. Abyste mohli nainstalovat GCM, musíte nejdřív nainstalovat **Chocolaty**. Pokud chcete nainstalovat Chocolaty a GCM, spusťte v prostředí Windows PowerShell následující příkazy jako **správce**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Nainstalovat Git na počítačích s Linuxem (CentOS)

Spusťte následující příkaz prostředí bash instalace Git na počítačích s Linuxem (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generovat veřejný klíč SSH na počítačích s Linuxem (CentOS)

Pokud používáte počítače s Linuxem (CentOS) ke spuštění příkazů git, musíte přidat veřejný klíč SSH z vašeho počítače do služby Azure DevOps tak, aby tento počítač je rozpoznáván DevOps služby Azure. Nejprve musíte vygenerovat veřejný klíč SSH a přidejte klíč k veřejné klíče SSH na stránce nastavení zabezpečení služby Azure DevOps. 

1. Vygenerujte klíč SSH, spusťte následující dva příkazy: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Příkazy ke generování klíče SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Zkopírujte celý klíč SSH včetně *SSH-RSA*. 
1. Přihlaste se k vašim službám Azure DevOps. 
1. V pravém horním rohu stránky klikněte na **< název\>** a klikněte na **zabezpečení**. 
    
   ![Klikněte na své jméno a potom klikněte na tlačítko zabezpečení](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klikněte na **veřejné klíče SSH**a pak klikněte na **+ Přidat**. 

   ![Klikněte na veřejné klíče SSH a pak klikněte na + Přidat](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Vložte do textového pole zkopírovaný klíč SSH a uložte ho.


## <a name="next-steps"></a>Další kroky

K dispozici jsou také kompletní podrobné návody, které ukazují všechny kroky v procesu pro **konkrétní scénáře** . Jsou uvedeny a propojeny s popisy miniatur v tématu [Příklady návodů](walkthroughs.md) . Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady, které ukazují, jak provést kroky v rámci vědeckého zpracování týmových dat pomocí Azure Machine Learning Studio (Classic), najdete v části s výukovou cestou [Azure ml](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) .
