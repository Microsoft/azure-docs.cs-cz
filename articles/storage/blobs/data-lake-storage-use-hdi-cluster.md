---
title: Použití Azure Data Lake Storage Gen2 Preview s využitím clusterů Azure HDInsight
description: Zjistěte, jak k dotazování dat ze služby Azure Data Lake Storage Gen2 ve verzi Preview a uložte výsledky analýzy.
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: b9f7a1144be21b425ff0bed9e2e6cb47315c13a2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974896"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Použití Azure Data Lake Storage Gen2 Preview s využitím clusterů Azure HDInsight

Pokud chcete analyzovat data v clusteru služby HDInsight, můžete ukládat data v libovolnou kombinaci služby Azure Blob Storage, Azure Blob Storage s Azure Data Lake Storage Gen2 povolenou verzí Preview nebo Azure Data Lake Storage Gen1. Všechny možnosti ukládání umožňují bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete zadat kontejner objektů blob v Azure Storage nebo hierarchického oboru názvů, které nabízí Data Lake Storage Gen2 jako výchozí systém souborů. Můžete také u služby HDInsight 3.5 můžete vybrat kontejner nebo hierarchického oboru názvů jako výchozí systém souborů s několika výjimkami.

V tomto článku se dozvíte, jak služba Data Lake Storage Gen2 pracuje s clustery HDInsight. Další informace o vytvoření clusteru HDInsight najdete v tématu [nastavení HDInsight clustery pomocí Hadoop, Spark, Kafka a další služby Azure Data Lake Storage](data-lake-storage-quickstart-create-connect-hdi-cluster.md).

Azure Storage je robustní řešení úložiště pro obecné účely, které se jednoduše integruje se službou HDInsight. HDInsight můžete jako výchozí systém souborů pro cluster použít Azure Data Lake Storage. Pomocí rozhraní Hadoop distributed file system (HDFS) může celá sada komponent v HDInsight pracovat přímo se soubory ve službě Azure Data Lake Storage.

Nedoporučujeme používat výchozí systém souborů pro ukládání firemních dat. Výchozí systém souborů odstranění po každém použití snížit náklady na úložiště je dobrým zvykem. Všimněte si, že výchozí kontejner obsahuje aplikace a systém protokoly. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho systému souborů pro několik clusterů se nepodporuje.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá službu Azure Storage:

![Clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných i nestrukturovaných dat do služby Blob Storage.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<NAME_NODE_HOST>/<PATH>

HDInsight navíc umožňuje přístup k datům uloženým ve službě Azure Data Lake Storage. Syntaxe je:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Tady jsou některé aspekty při použití účtu služby Azure Storage s clustery HDInsight.

* **Soubory v účtech úložiště, které jsou připojené ke clusteru** mají název účtu a klíč přidružený k během vytváření clusteru. Tato konfigurace poskytuje úplný přístup k souborům v systému souborů.

* **Veřejné soubory v účtech úložiště, které nejsou připojené ke clusteru** vystavení oprávnění jen pro čtení k souborům v systému souborů.
  
  > [!NOTE]
  > Systémy veřejných souborů umožňují získat seznam všech souborů, které jsou k dispozici v systému souborů a získat přístup k metadatům. Systémy veřejných souborů umožňují přístup k souborům, pouze v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [omezení přístupu ke kontejnerům a objektům blob](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (pravidla pro kontejnery a objekty BLOB fungovat stejným popředí soubory a systém souborů).
 
* **Privátní souborové systémy v účtech úložiště, které nejsou připojené ke clusteru** neumožňují přístup k souborům v systému souborů, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. Důvody pro toto omezení jsou vysvětleny dále v tomto článku.

Účty úložiště, které jsou definovány v procesu vytváření a jejich klíče jsou uložené v *%HADOOP_HOME%/conf/core-site.xml* na uzlech clusteru. Výchozí chování HDInsight je používání účtů úložiště, které jsou definovány v *core-site.xml* souboru. Toto nastavení můžete upravit pomocí [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Hive, MapReduce, streamování Hadoop a Pig, může obsahovat popis účtů úložiště a spojených metadat. (Tento přístup aktuálně funguje pro Pig s účty úložiště, ale ne pro metadata.) Více informací najdete v části [Použití clusteru HDInsight s alternativními účty úložiště a metaúložišti](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Výhody služby Azure Storage

Předpokládaná výkonová náročnost společně umístěných výpočetních clusterů a prostředků úložiště je zmírněna tím, že výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť umožňuje efektivní přístup výpočetních uzlů k datům ve službě Azure Storage.

S ukládáním dat ve službě Azure Storage namísto HDFS je spojeno několik výhod:

* **Opakované použití dat a sdílení:** data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure Storage jsou přístupná prostřednictvím rozhraní API HDFS nebo prostřednictvím [rozhraní REST API služby Blob Storage][blob-storage-restAPI]. Proto s větším počtem aplikací (včetně jiných clusterů HDInsight) a nástrojů  se dají vytvářet a využívat data.

* **Archivace dat:** Ukládání dat ve službě Azure Storage umožňuje bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

* **Náklady na úložiště dat:** ukládání dat v nativní HDFS je z dlouhodobého hlediska dražší než ukládání dat ve službě Azure storage, protože náklady na výpočetní cluster jsou vyšší než náklady na úložiště Azure. Navíc se data nemusí nahrávat znovu pro každou generaci výpočetních clusterů, náklady na nahrávání dat jsou tak nižší.

* **Elastické škálování:** I když HDFS poskytuje škálovaný systém souborů, škála se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky k dispozici ve službě Azure Storage.

* **Geografická replikace:** dat úložiště Azure může být geograficky replikovaný. I když se tato možnost poskytuje geografické obnovení a redundanci dat, podpora převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může způsobit dodatečné náklady. Proto zvolte geografické replikace opatrně a pouze pokud je hodnota dat. Další vyplatí.

* **Správa životního cyklu dat:** všechna data v libovolném systému souborů, prochází jeho vlastního životního cyklu. Data často začíná jsou velmi cenné a často používaná, přejde do právě méně užitečné a které vyžadují přístup pro méně a nakonec vyžaduje archivu nebo odstranění. Azure Storage poskytuje ovládání datových vrstev na data a životního cyklu zásad správy, které vrstvení dat odpovídajícím způsobem pro fáze životního cyklu.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage ve skutečnosti uložit nechcete. V takovém případě můžete zvolit k uložení dat do místní HDFS. Ve skutečnosti HDInsight používá nativní implementaci HDFS (což se označuje jako DFS) pro některé z těchto mezilehlých výsledků v úlohách Hive a jiných procesů.

> [!NOTE]
> Většina příkazů HDFS (například `ls`, `copyFromLocal` a `mkdir`) i nadále fungovat podle očekávání. Jenom příkazy, které jsou specifické pro systému souborů DFS, jako je například `fschk` a `dfsadmin`, zobrazit různé chování ve službě Azure storage.

## <a name="create-a-data-lake-storage-file-system"></a>Vytvořit systém souborů Data Lake Storage

Chcete-li použít systém souborů, je třeba nejprve vytvořit [účtu služby Azure Storage][azure-storage-create]. Jako součást tohoto procesu zadáte oblast Azure, které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze serveru SQL metaúložiště Hive a databáze serveru SQL metaúložiště Oozie musí být také umístěny ve stejné oblasti.

Ať jsou umístěná kdekoli, každý objekt blob, který vytvoříte patří do systému souborů ve vašem účtu úložiště.

Výchozí systém souborů Data Lake Storage Gen2 ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí systém souborů Data Lake Storage Gen2 nesdílejte s více clusterů HDInsight. Může dojít k poškození historie úlohy. Doporučujeme použít jiný systém souborů pro každý cluster a umístit sdílená data na propojený účet úložiště zadaný v nasazení všech příslušných clusterů, nikoli výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Tvorba clusterů HDInsight][hdinsight-creation]. Ale můžete znovu použít výchozí systém souborů úložiště po odstranění původního clusteru HDInsight. Pro clustery HBase můžete zachovat schéma tabulky HBase a data vytvořením nového clusteru HBase pomocí výchozího kontejneru objektů blob, který používá odstraněné cluster HBase, který je odstraněný.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Při vytváření clusteru HDInsight z portálu, máte k dispozici možnosti (jak je znázorněno na následujícím snímku obrazovky) zadat podrobnosti účtu úložiště. Můžete také určit, zda chcete ještě účet úložiště přidružené ke clusteru a pokud ano, zvolit z některého z úložiště k dispozici možnosti pro další úložiště.

![Zdroj dat pro vytvoření hadoopu HDInsight](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

### <a name="use-azure-powershell"></a>Použití Azure Powershell

Pokud jste [nainstalovali a nakonfigurovali Azure PowerShell][powershell-install], můžete použít následující kód z příkazového řádku Azure PowerShell k vytvoření účtu úložiště a kontejneru:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> Vytvoření kontejneru je synonymum pro vytváření systému souborů v Data Lake Storage Gen2.

### <a name="use-azure-cli"></a>Použití Azure CLI

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Pokud máte [nainstalováno a nakonfigurováno rozhraní příkazového řádku Azure CLI](../../cli-install-nodejs.md), následující příkaz lze použít k účtu úložiště a kontejneru.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Ve verzi public preview služby Data Lake Storage Gen2 pouze `--sku Standard_LRS` je podporována.

Budete vyzváni k zadání geografické oblasti, ve které se vytvoří účet úložiště. Vytvořte účet úložiště ve stejné oblasti, kterou chcete použít k vytvoření clusteru HDInsight.

Po vytvoření účtu úložiště použijte následující příkaz k načtení klíčů účtu úložiště:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Když chcete vytvořit kontejner, použijte následující příkaz:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Vytvoření kontejneru je synonymum pro vytváření systému souborů v Data Lake Storage Gen2.

## <a name="address-files-in-azure-storage"></a>Adresování souborů ve službě Azure Storage

Schéma identifikátoru URI pro přístup k souborům ve službě Azure Storage ze služby HDInsight je:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

Schéma identifikátoru URI poskytuje nezašifrovaný přístup (s *abfs:* předpony) a zašifrovaný přístup SSL (s *abfss*). Doporučujeme používat *abfss* kdykoli je to možné, i v případě, že přístup k datům, umístěným uvnitř stejné oblasti v Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifikuje cesta systému souborů Data Lake Storage Gen2.
* &lt;Název účtu&gt; identifikuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

    Pokud hodnoty &lt;FILE_SYSTEM_NAME&gt; ani &lt;ACCOUNT_NAME&gt; jste zadali, se používá výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například *hadoop-mapreduce-examples.jar* soubor, který se dodává s clustery HDInsight lze odkazovat pomocí jedné z následujících cest:
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> V clusterech HDInsight verze 2.1 a 1.6. je název souboru *hadoop-examples.jar*

* &lt;Cesta&gt; je název cesty HDFS souboru nebo adresáře.

> [!NOTE]
> Při práci se soubory mimo HDInsight Většina nástrojů ABFS formátování a místo toho očekávají základní formát cesty, jako například nebyla rozpoznána `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Vytváření clusterů HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. Tento přístup umožňuje vytvářet řešení pro získávání škálovatelné, dlouhodobé archivace dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Ovladač systému souborů Hadoop ABFS pro Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
* [Úvod do služby Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Nastavení clusterů HDInsight pomocí Azure Data Lake Storage Gen2 s Hadoop, Spark, Kafka a další](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
* [Ingestovat data do Azure Data Lake Storage Gen2 pomocí distcp](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
