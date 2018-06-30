---
title: Pomocí Azure Data Lake Storage Gen2 Preview s clustery Azure HDInsight
description: Naučte se načítat data z Azure Data Lake Storage Gen2 Preview a uložte výsledky analýzy.
keywords: hdfs, strukturovaných dat, nestrukturovaných dat, data lake store, Hadoop vstupní, výstupní Hadoop, hadoop úložiště, hdfs vstup, hdfs výstup, hdfs úložiště, wasb azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: d5b67d971c2261084857d6b512c8d011173884af
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113251"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Pomocí Azure Data Lake Storage Gen2 Preview s clustery Azure HDInsight

Chcete-li analyzovat data v clusteru HDInsight, můžete ukládat data buď v libovolnou kombinaci Azure Storage, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2 Preview. Povolit všechny možnosti úložiště vám umožní bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete určit kontejner objektů blob v Azure Storage nebo Azure Data Lake Storage jako výchozí systém souborů. Případně s HDInsight 3.5, můžete vybrat Azure Storage nebo Azure Data Lake Storage jako výchozí systém souborů s několika výjimkami.

V tomto článku se dozvíte, jak funguje Azure Data Lake Storage Gen2 s clustery HDInsight. Další informace o vytváření clusteru služby HDInsight najdete v tématu [nastavení HDInsight clusterů pomocí Azure Data Lake Storage s Hadoop, Spark, Kafka a další](quickstart-create-connect-hdi-cluster.md).

Azure Storage je robustní řešení úložiště pro obecné účely, které se jednoduše integruje se službou HDInsight. HDInsight můžete použít Azure Data Lake Storage jako výchozí systém souborů pro cluster. Pomocí rozhraní Hadoop system (HDFS) souborů DFS kompletní součásti v HDInsight mohou pracovat přímo na soubory v Azure Data Lake Storage.

Nedoporučujeme použijete k ukládání firemních dat na výchozí systém souborů. Po každém použití snížit náklady na úložiště je vhodné se odstraňuje na výchozí systém souborů. Všimněte si, že výchozí kontejner obsahuje aplikace a systému protokoly. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho systému souborů pro víc clusterů se nepodporuje.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá službu Azure Storage:

![Clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných i nestrukturovaných dat do služby Blob Storage.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Kromě toho HDInsight umožňuje přístup k datům, která je uložená v Azure Data Lake Storage. Syntaxe je:

    abfs[s]://<file_system>@<accountname>.dfs.core.windows.net/<path>

Tady jsou některé aspekty při použití účtu služby Azure Storage s clustery HDInsight.

* **Soubory v účtech úložiště, které jsou připojené ke clusteru** mít název účtu a klíč přidružený k během vytváření clusteru. Tato konfigurace umožňuje plný přístup k souborům v systému souborů.

* **Veřejné soubory v účtech úložiště, které nejsou připojené ke clusteru s podporou** vystavit oprávnění jen pro čtení souborů v systému souborů.
  
  > [!NOTE]
  > Systémy veřejných souborů umožňují získat seznam všech souborů, které jsou k dispozici v systému souborů a získat přístup k metadatům. Systémy veřejných souborů umožňují přístup k souborům, pouze v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [omezení přístupu ke kontejnerům a objektům blob](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (pravidla pro kontejnery a objekty BLOB fungovat stejným popředí soubory a systému souborů).
 
* **Soubor privátního systémy v účtech úložiště, které nejsou připojené ke clusteru s podporou** neumožňují přístup k souborům v systému souborů, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. Později v tomto článku jsou vysvětleny příčiny toto omezení.

Účty úložiště, které jsou definovány v procesu vytváření a jejich klíče jsou uložené v *%HADOOP_HOME%/conf/core-site.xml* na uzlech clusteru. Výchozím chováním služby HDInsight je používání účtů úložiště, které jsou definované v *core-site.xml* souboru. Toto nastavení můžete upravit pomocí [Ambari](/hdinsight/hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Hive, MapReduce, streamování Hadoop a Pig, může obsahovat popis účtů úložiště a spojených metadat. (Tento přístup aktuálně funguje pro Pig s účty úložiště, ale ne pro metadata.) Více informací najdete v části [Použití clusteru HDInsight s alternativními účty úložiště a metaúložišti](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Výhody služby Azure Storage

Předpokládaná výkonová náročnost společně umístěných výpočetních clusterů a prostředků úložiště je zmírněna tím, že výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť umožňuje efektivní přístup výpočetních uzlů k datům ve službě Azure Storage.

S ukládáním dat ve službě Azure Storage namísto HDFS je spojeno několik výhod:

* **Opakované použití dat a sdílení:** data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure Storage jsou přístupná prostřednictvím rozhraní API HDFS nebo prostřednictvím [rozhraní REST API služby Blob Storage][blob-storage-restAPI]. Proto s větším počtem aplikací (včetně jiných clusterů HDInsight) a nástrojů  se dají vytvářet a využívat data.

* **Archivace dat:** Ukládání dat ve službě Azure Storage umožňuje bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

* **Náklady na úložiště dat:** ukládání dat v nativní HDFS je z dlouhodobého hlediska dražší než ukládání dat v úložišti Azure, protože náklady na výpočetním clusteru je vyšší než náklady na úložiště Azure. Navíc se data nemusí nahrávat znovu pro každou generaci výpočetních clusterů, náklady na nahrávání dat jsou tak nižší.

* **Elastické škálování:** I když HDFS poskytuje škálovaný systém souborů, škála se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky k dispozici ve službě Azure Storage.

* **Geografická replikace:** data úložiště Azure může být geograficky replikované. I když tato funkce vám dává geografické obnovení a redundanci dat, podporu převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může způsobit dodatečné náklady. Proto zvolte geografická replikace pečlivě a pouze pokud hodnota data je vhodné dalších poplatků.

* **Správa životního cyklu dat:** všechna data v libovolném systému souborů prochází vlastní životního cyklu. Data se často spustí vypnout se velmi cenné a často používaná, přechází do je méně cenné a vyžadují menší přístup a nakonec vyžaduje archivace nebo odstraňování. Úložiště Azure poskytuje dat vrstvení a životního cyklu zásad správy, které správně vrstvy data pro jeho fáze životního cyklu.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage ve skutečnosti uložit nechcete. V takovém případě můžete zvolit k uložení dat do místní HDFS. Ve skutečnosti služba HDInsight používá nativní implementaci HDFS (což se označuje jako DFS) pro některé z těchto mezilehlých výsledků v úlohách Hive a jinými procesy.

> [!NOTE]
> Většina příkazů HDFS (například `ls`, `copyFromLocal` a `mkdir`) i nadále fungovat podle očekávání. Příkazy, které jsou specifické pro systému souborů DFS, jako například `fschk` a `dfsadmin`, zobrazit různé chování v úložišti Azure.

## <a name="create-an-data-lake-storage-file-system"></a>Vytvořit systém souborů Data Lake Storage

Pomocí systému souborů, je třeba nejprve vytvořit [účet úložiště Azure][azure-storage-create]. V rámci tohoto procesu zadejte oblast Azure, kde se má vytvořit účet úložiště. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze serveru SQL metaúložiště Hive a databáze serveru SQL metaúložiště Oozie musí být také umístěny ve stejné oblasti.

Bez ohledu na jeho žije, do systému souborů v účtu Azure Data Lake Storage patří každý objekt blob, které vytvoříte. 

Výchozí systém souborů Data Lake Storage ukládá informace o specifických pro cluster například historie úlohy a protokoly. Výchozí systém souborů Data Lake Storage nesdílejte s více clustery služby HDInsight. Může dojít k poškození historie úlohy. Doporučujeme použít jiný systém souborů pro každý cluster a umístit sdílená data na propojený účet úložiště zadaný v nasazení všech příslušných clusterů, nikoli výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Tvorba clusterů HDInsight][hdinsight-creation]. Však můžete znovu použít výchozí systém souborů úložiště po odstranění původního clusteru HDInsight. Pro clustery HBase můžete zachovat schéma tabulky HBase a data vytvořením nového clusteru HBase pomocí výchozího kontejneru blob, který je používán odstraněné cluster HBase, který byl odstraněn.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Při vytváření clusteru služby HDInsight z portálu, budete muset možnosti (jak je znázorněno na následujícím snímku obrazovky) zadejte podrobnosti o účtu úložiště. Můžete také zadat, zda má účet další úložiště přidružen ke clusteru a pokud ano, vyberte z některé z možností úložiště k dispozici další úložiště.

![Zdroj dat pro vytvoření hadoopu HDInsight](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

### <a name="use-azure-powershell"></a>Použití Azure Powershell

Pokud jste [instalace a konfigurace prostředí Azure PowerShell][powershell-install], můžete použít následující kód z příkazového řádku Azure PowerShell k vytvoření účtu úložiště a kontejneru:

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
> Vytvoření kontejneru je totožná s vytváření systém souborů v Azure Data Lake Storage.

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
> Během verzi public preview služby Data Lake Storage Gen2 pouze `--sku Standard_LRS` je podporována.

Budete vyzváni k zadání geografické oblasti, ve které se vytvoří účet úložiště. Vytvořte účet úložiště ve stejné oblasti, kterou chcete použít k vytvoření clusteru služby HDInsight.

Po vytvoření účtu úložiště použijte následující příkaz k načtení klíčů účtu úložiště:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Když chcete vytvořit kontejner, použijte následující příkaz:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Vytvoření kontejneru je totožná s vytváření systém souborů v Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Adresování souborů ve službě Azure Storage

Schéma identifikátoru URI pro přístup k souborům ve službě Azure Storage ze služby HDInsight je:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

Schéma identifikátoru URI poskytuje nezašifrovaný přístup (s *abfs:* předponu) a zašifrovaný přístup SSL (s *abfss*). Doporučujeme používat *abfss* kdykoli je to možné, i v případě, že přístup k datům, umístěným uvnitř stejné oblasti v Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifikuje cesta systému souborů Azure Data Lake Storage.
* &lt;ACCOUNT_NAME&gt; identifikuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

    Pokud hodnoty pro &lt;FILE_SYSTEM_NAME&gt; ani &lt;ACCOUNT_NAME&gt; byl zadán, se používá výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například *hadoop-mapreduce-examples.jar* soubor, který se dodává s clustery HDInsight lze odkazovat pomocí jedné z následujících cestách:
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> V clusterech HDInsight verze 2.1 a 1.6. je název souboru *hadoop-examples.jar*

* &lt;Cesta&gt; je název cesty HDFS souboru nebo adresáře.

> [!NOTE]
> Při práci se soubory mimo HDInsight, většina nástrojů nelze rozpoznat ABFS formátování a místo toho očekávají základní formát cesty, jako například `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Vytváření clusterů HDInsight](/hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. Tento přístup umožňuje vytvářet řešení pro získávání škálovatelné, dlouhodobé archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Ovladač systému souborů Hadoop ABFS pro Azure Data Lake Storage Gen2](abfs-driver.md)
* [Úvod do Azure Data Lake Storage](introduction.md)
* [Clustery prostředí HDInsight pomocí Azure Data Lake Storage s Hadoop, Spark, Kafka a další nastavení](quickstart-create-connect-hdi-cluster.md)
* [Ingestovat data do Azure Data Lake Storage použití distcp](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: /hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: /storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
