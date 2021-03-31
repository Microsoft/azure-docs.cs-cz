---
title: Platformy a nástroje pro datové vědy projekty – vědecké zpracování týmových dat
description: Rozepisuje a popisuje zdroje dat a analýzy, které jsou k dispozici pro podniky, které se budou standardizovat při vědeckém zpracování týmových dat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96453854"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy a nástroje pro projekty pro datové vědy

Microsoft poskytuje kompletní spektrum analytických prostředků pro cloudové i místní platformy. Můžou být nasazené, aby bylo možné provádět efektivní a škálovatelné zpracování vašich projektů pro datové vědy. Pokyny pro týmy, které implementují projekty pro datové vědy v rámci sledování, správy verzí a způsobu spolupráce, zajišťuje [vědecký proces týmového zpracování dat](overview.md) (TDSP).  Pro osnovu rolí personálu a jejich přidružených úloh, které jsou zpracovávány týmem pro datové vědy, které se týkají tohoto procesu, najdete informace v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

K dispozici jsou analytické prostředky pro datové vědy týmu, které používají TDSP:

- Virtual Machines pro datové vědy (Windows a Linux CentOS)
- Clustery HDInsight Spark
- Azure Synapse Analytics
- Azure Data Lake
- Clustery pro podregistr HDInsight
- Azure File Storage
- Služby SQL Server 2019 R a Python
- Azure Databricks

V tomto dokumentu stručně popíšeme prostředky a získáte odkazy na kurzy a návody, které publikovali TDSP týmy. Můžou vám pomoci naučit se, jak je používat krok za krokem, a začít je používat k vytváření inteligentních aplikací. Další informace o těchto zdrojích jsou k dispozici na stránkách produktu. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

Virtuální počítač pro datové vědy nabízený v systému Windows i Linux od Microsoftu obsahuje oblíbené nástroje pro vývoj a vývojové aktivity pro datové vědy. Obsahuje nástroje, jako například:

- Microsoft R Server Developer Edition 
- Distribuce Anaconda Python
- Jupyter poznámkové bloky pro Python a R 
- Visual Studio Community Edition s nástroji Python a R na Windows nebo zatmění v systému Linux
- Power BI Desktop pro Windows
- Edice SQL Server 2016 Developer Edition ve Windows/Postgres v systému Linux

Zahrnuje také **nástroje ml a AI** , jako jsou xgboost, Mxnet a pro dostupné.

V současné době je DSVM k dispozici v operačních systémech **Windows** a **Linux CentOS** . Vyberte velikost DSVM (počet jader procesoru a množství paměti) na základě potřeb projektů pro datové vědy, na kterých chcete provádět plánování. 

Další informace o edici Windows DSVM najdete v části [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) na Azure Marketplace. Informace o edici DSVM pro Linux najdete v tématu [linux Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Další informace o tom, jak efektivně provádět některé běžné úkoly pro datové vědy na DSVM, najdete v tématu [10 věcí, které můžete dělat na virtuálním počítači pro datové vědy](../data-science-virtual-machine/vm-do-ten-things.md) .


## <a name="azure-hdinsight-spark-clusters"></a>Clustery Azure HDInsight Spark

Apache Spark je opensource paralelní framework pro zpracování, který podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul pro zpracování Spark je založený na rychlosti, snadném použití a propracované analýze. Možnosti výpočtů v paměti Sparku jsou vhodnou volbou pro iterativní algoritmy v strojovém učení a pro výpočty grafů. Spark je také kompatibilní s úložištěm Azure Blob Storage (WASB), takže existující data uložená v Azure lze snadno zpracovat pomocí Sparku.

Když vytvoříte cluster Spark v HDInsight, vytvoříte výpočetní prostředky Azure s nainstalovaným a nakonfigurovaným Spark. Vytvoření clusteru Spark v HDInsight trvá přibližně 10 minut. Ukládat data, která se mají zpracovat v úložišti objektů BLOB v Azure Informace o používání služby Azure Blob Storage s clusterem najdete v tématu [použití úložiště objektů blob Azure kompatibilního se systémem Hadoop v HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP tým od Microsoftu publikoval dva ucelené návody k tomu, jak pomocí Azure HDInsight Spark clusterů sestavovat řešení pro datové vědy, jednu pomocí Pythonu a druhý Scala. Další informace o **clusterech** Azure HDInsight Spark najdete v tématu [Přehled: Apache Spark v prostředí HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Pythonu** na Azure HDInsight Sparkm clusteru, najdete v tématu [Přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md). Informace o tom, jak vytvořit řešení pro datové vědy pomocí **Scala** v clusteru Azure HDInsight Spark, najdete v tématu věnovaném [datové vědy pomocí Scala a Sparku v Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure synapse Analytics umožňuje snadno a v sekundách škálovat výpočetní prostředky, aniž by došlo k nadměrnému zřizování nebo nadměrnému placení. Nabízí také jedinečnou možnost pozastavit používání výpočetních prostředků, což vám dává volnost v tom, že vám umožní lépe spravovat náklady na Cloud. Možnost nasazení škálovatelných výpočetních prostředků umožňuje přenést všechna vaše data do služby Azure synapse Analytics. Náklady na úložiště jsou minimální a můžete spouštět výpočetní prostředky jenom na částech datových sad, které chcete analyzovat. 

Další informace o službě Azure synapse Analytics najdete na webu [Azure synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse) . Další informace o tom, jak sestavit komplexní řešení pro pokročilou analýzu pomocí Azure synapse Analytics, najdete [v článku vědecké zpracování týmových dat v akci: použití Azure synapse Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake je úložiště všech typů dat shromážděných v rámci jednoho podniku před všemi formálními požadavky nebo podle toho, jaké schéma se ukládá. Tato flexibilita umožňuje uchovávání každého typu dat v Data Lake bez ohledu na jeho velikost nebo strukturu a rychlost ingestování. Organizace pak můžou pomocí rozšíření Hadoop nebo Advanced Analytics vyhledat vzory těchto datových laků. Data laků můžou sloužit také jako úložiště pro přípravu dat s nižšími náklady před tím, než se data dopustí a přesunou do datového skladu.

Další informace o Azure Data Lake najdete v tématu [Úvod do Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Další informace o tom, jak pomocí Azure Data Lake vytvořit škálovatelné řešení pro datové vědy, najdete v článku [škálovatelné vědecké zpracování dat v Azure Data Lake: ucelený návod](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clustery Azure HDInsight (Hadoop)

Apache Hive je systém datového skladu pro Hadoop, který umožňuje sumarizaci dat, dotazování a analýzu dat pomocí HiveQL, dotazovací jazyk podobný SQL. Podregistr lze použít k interaktivnímu prozkoumávání dat nebo k vytvoření opakovaně použitelných úloh dávkového zpracování.

Podregistr umožňuje strukturování struktury na základě nestrukturovaných dat. Po definování struktury můžete použít podregistr k dotazování těchto dat v clusteru Hadoop bez nutnosti používat nebo dokonce znát jazyk Java nebo MapReduce. HiveQL (dotazovací jazyk podregistru) umožňuje zapisovat dotazy s příkazy, které jsou podobné T-SQL.

Pro odborníky přes data může podregistr spustit Python User-Defined Functions (UDF) v dotazech podregistru pro zpracování záznamů. Tato možnost výrazně rozšiřuje možnosti dotazů na podregistry v analýze dat. Konkrétně umožňuje, aby odborníci na data prováděli škálovatelné řízení funkcí v jazycích, které jsou většinou obeznámené s: SQL jako HiveQL a Python. 

Další informace o clusterech podregistrů Azure HDInsight najdete v tématu [použití podregistru a HiveQL se systémem Hadoop ve službě HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Další informace o tom, jak vytvořit škálovatelné řešení pro datové vědy s clustery Azure HDInsight, najdete v článku [vědecké zpracování týmových dat v akci: používání clusterů HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage je služba, která nabízí sdílené složky v cloudu pomocí standardního protokolu SMB (Server Message Block). Podporují se SMB 2.1 i SMB 3.0. S Azure File Storage můžete rychle a bez nákladných přepisů migrovat starší aplikace, které spoléhají na sdílené složky, do Azure. Aplikace běžící v cloudových službách nebo virtuálních počítačích Azure nebo spuštěné z lokálních klientů můžou připojit sdílenou složku v cloudu stejným způsobem, jako desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Zvlášť užitečné pro projekty pro datové vědy je možnost vytvořit úložiště souborů Azure jako místo pro sdílení dat projektu se členy týmu projektu. Každý z nich pak má přístup ke stejné kopii dat ve službě Azure File Storage. Tyto souborové úložiště můžou také používat ke sdílení sad funkcí generovaných během provádění projektu. Pokud je projekt zapojení klienta, můžou klienti vytvořit službu Azure File Storage v rámci vlastního předplatného Azure, aby mohli sdílet data a funkce projektu. Tímto způsobem má klient úplnou kontrolu nad datovými assety projektu. Další informace o Azure File Storage najdete v tématu Začínáme [se službou Azure File Storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md) a [používání Azure File Storage se systémem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Služby SQL Server 2019 R a Python

Služba R (v databázi) poskytuje platformu pro vývoj a nasazování inteligentních aplikací, které můžou odhalit nové poznatky. Můžete použít bohatou a výkonnou jazyk R, včetně mnoha balíčků poskytovaných komunitou R, k vytváření modelů a generování předpovědi z dat SQL Server. Vzhledem k tomu, že služba R (v databázi) integruje jazyk R s SQL Server, jsou analýzy udržovány blízko dat, což eliminuje náklady a rizika zabezpečení související s přesunem dat.

Služba r Services (v databázi) podporuje open source jazyk R s komplexní sadou SQL Serverch nástrojů a technologií. Nabízí špičkový výkon, zabezpečení, spolehlivost a možnosti správy. Řešení jazyka R můžete nasadit pomocí pohodlných a známých nástrojů. Vaše produkční aplikace mohou volat modul runtime R a načíst předpovědi a vizuály pomocí jazyka Transact-SQL. K vylepšení škálování a výkonu řešení jazyka R můžete také použít knihovny pro škálování. Další informace najdete v tématu [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services).

Tým TDSP od Microsoftu publikoval dva komplexní návody, které ukazují, jak vytvářet řešení pro datové vědy v SQL Server 2016 R Services: jeden pro programátory R a jeden pro vývojáře v jazyce SQL. **Programátory R** najdete v článku [komplexní návody pro datové vědy](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Pro **vývojáře v SQL** najdete informace [v článku rozšířené analýzy pro vývojáře SQL (kurz)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Příloha: nástroje pro nastavení projektů pro datové vědy

### <a name="install-git-credential-manager-on-windows"></a>Instalace správce přihlašovacích údajů Git ve Windows

Pokud používáte TDSP ve **Windows**, musíte si nainstalovat **Git Credential Manager (GCM)** , abyste mohli komunikovat s úložišti Git. Abyste mohli nainstalovat GCM, musíte nejdřív nainstalovat **Chocolaty**. Pokud chcete nainstalovat Chocolaty a GCM, spusťte v prostředí Windows PowerShell následující příkazy jako **správce**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Instalace počítačů s Git na Linux (CentOS)

Spusťte následující příkaz bash k instalaci počítačů Git on Linux (CentOS):

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generovat veřejný klíč SSH na počítačích se systémem Linux (CentOS)

Pokud používáte počítače se systémem Linux (CentOS) ke spuštění příkazů Git, musíte do svého Azure DevOps Services přidat veřejný klíč SSH vašeho počítače, aby tento počítač rozpoznal Azure DevOps Services. Nejdřív je potřeba vygenerovat veřejný klíč SSH a přidat klíč k veřejným klíčům SSH na stránce nastavení zabezpečení Azure DevOps Services. 

1. Chcete-li vygenerovat klíč SSH, spusťte následující dva příkazy: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Příkazy pro vygenerování klíče SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Zkopírujte celý klíč SSH včetně *SSH-RSA*. 
1. Přihlaste se ke svému Azure DevOps Services. 
1. V pravém horním rohu stránky klikněte na **<jméno \>** a pak klikněte na **zabezpečení**. 
    
   ![Klikněte na své jméno a pak klikněte na zabezpečení.](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klikněte na **veřejné klíče SSH** a pak klikněte na **+ Přidat**. 

   ![Klikněte na veřejné klíče SSH a pak klikněte na + Přidat.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Vložte do textového pole zkopírovaný klíč SSH a uložte ho.


## <a name="next-steps"></a>Další kroky

K dispozici jsou také kompletní podrobné návody, které ukazují všechny kroky v procesu pro **konkrétní scénáře** . Jsou uvedeny a propojeny s popisy miniatur v tématu [Příklady návodů](walkthroughs.md) . Ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, které ukazují, jak provést kroky v rámci vědeckého zpracování týmových dat pomocí Azure Machine Learning Studio (Classic), najdete v části s výukovou cestou [Azure ml](./index.yml) .