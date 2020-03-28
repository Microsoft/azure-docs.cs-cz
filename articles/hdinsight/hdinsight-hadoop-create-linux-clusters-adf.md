---
title: 'Kurz: Clustery na vyžádání v Azure HDInsight s Továrně dat'
description: Kurz – Naučte se vytvářet clustery Apache Hadoop na vyžádání v HDInsightu pomocí Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130692"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Kurz: Vytvoření clusterů Apache Hadoop na vyžádání ve službě HDInsight pomocí Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto kurzu se dozvíte, jak vytvořit cluster [Apache Hadoop,](./hadoop/apache-hadoop-introduction.md) na vyžádání, v Azure HDInsight pomocí Azure Data Factory. Potom pomocí datových kanálů v Azure Data Factory spustit úlohy Hive a odstranit cluster. Na konci tohoto kurzu se dozvíte, jak zprovoznit úlohu velkých objemů dat, kde se provádí vytváření clusteru, spuštění úlohy a odstranění clusteru podle plánu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Principy aktivit Azure Data Factory
> * Vytvoření datové továrny pomocí portálu Azure
> * Vytvoření propojených služeb
> * Vytvoření kanálu
> * Aktivace kanálu
> * Monitorování kanálu
> * Ověření výstupu

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalován [modul Az prostředí](https://docs.microsoft.com/powershell/azure/overview) PowerShell.

* Zaregistrovaný objekt služby Azure Active Directory. Po vytvoření instančního objektu nezapomeňte načíst **ID aplikace** a **ověřovací klíč** podle pokynů v propojeném článku. Tyto hodnoty potřebujete dále v tomto kurzu. Také se ujistěte, že instanční objekt je členem role *přispěvatele* předplatného nebo skupiny prostředků, ve které je vytvořen cluster. Pokyny k načtení požadovaných hodnot a přiřazení správných rolí naleznete [v tématu Vytvoření objektu zabezpečení služby Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Vytvoření předběžných objektů Azure

V této části vytvoříte různé objekty, které budou použity pro cluster HDInsight, který vytvoříte na vyžádání. Vytvořený účet úložiště bude obsahovat ukázkový `partitionweblogs.hql`skript [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) , který použijete k simulaci ukázkové úlohy Apache Hive, která běží v clusteru.

Tato část používá skript Azure PowerShell k vytvoření účtu úložiště a kopírování přes požadované soubory v rámci účtu úložiště. Ukázkový skript Prostředí Azure PowerShell v této části provádí následující úkoly:

1. Připřipojte se k Azure.
2. Vytvoří skupinu prostředků Azure.
3. Vytvoří účet služby Azure Storage.
4. Vytvoří kontejner objektů blob v účtu úložiště.
5. Zkopíruje ukázkový skript HiveQL (**partitionweblogs.hql**) kontejner blob. Skript je k [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)dispozici na adrese . Ukázkový skript je již k dispozici v jiné veřejné kontejneru objektů Blob. Skript PowerShell udává kopii těchto souborů do účtu Azure Storage, který vytvoří.

### <a name="create-storage-account-and-copy-files"></a>Vytvoření účtu úložiště a kopírování souborů

> [!IMPORTANT]  
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který se vytvoří skriptem.
> Poznamenejte si **název skupiny prostředků**, **název účtu úložiště**a klíč **účtu úložiště,** který je výstupem skriptu. Potřebujete je v další části.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Ověření účtu úložiště

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Zleva přejděte do části **Všechny služby** > **Obecné** > **skupiny prostředků**.
1. Vyberte název skupiny prostředků, který jste vytvořili ve skriptu PowerShellu. Filtr použijte, pokud je v seznamu uvedeno příliš mnoho skupin prostředků.
1. V zobrazení **Přehled** se zobrazí jeden zdroj uvedený, pokud nesdílíte skupinu zdrojů s jinými projekty. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Vyberte název účtu úložiště.
1. Vyberte **dlaždici Kontejnery.**
1. Vyberte kontejner **adfgetstarted.** Zobrazí se složka s názvem **hivescripts**.
1. Otevřete složku a ujistěte se, že obsahuje ukázkový soubor skriptu, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Principy aktivity Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestruje a automatizuje pohyb a transformaci dat. Azure Data Factory můžete vytvořit hdinsight hadoop clusteru just-in-time ke zpracování řezu vstupních dat a odstranit clusteru po dokončení zpracování.

V Azure Data Factory může mít datová továrna jeden nebo více datových kanálů. Datový kanál má jednu nebo více aktivit. Existují dva typy činností:

* [Aktivity přesunu dat](../data-factory/copy-activity-overview.md). Aktivity přesunu dat slouží k přesunutí dat ze zdrojového úložiště dat do cílového úložiště dat.
* [Aktivity transformace dat](../data-factory/transform-data.md). Aktivity transformace dat se používají k transformaci nebo zpracování dat. HDInsight Hive Activity je jednou z transformačních aktivit podporovaných data factory. V tomto kurzu použijete transformační aktivitu Hive.

V tomto článku nakonfigurujete aktivitu Hive k vytvoření clusteru HDInsight Hadoop na vyžádání. Když se aktivita spustí ke zpracování dat, stane se následující:

1. Cluster HDInsight Hadoop se automaticky vytvoří, abyste mohl(a) zpracovat řez.

2. Vstupní data jsou zpracována spuštěním skriptu HiveQL v clusteru. V tomto kurzu skript HiveQL přidružený k aktivitě podregistru provádí následující akce:

    * Použije existující tabulku *(hivesampletable*) k vytvoření jiné tabulky **HiveSampleOut**.
    * Naplní tabulku **HiveSampleOut** pouze určitými sloupci z původní *vzorové tabulky .*

3. Cluster HDInsight Hadoop se po dokončení zpracování odstraní a cluster je nečinný po nakonfigurovanou dobu (nastavení timeToLive). Pokud další řez dat je k dispozici pro zpracování s v tomto timeToLive dobu nečinnosti, stejný cluster se používá ke zpracování řezu.  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. V levé nabídce přejděte na + Vytvořit**datovou továrnu****analytics** >  **zdrojů** > .

    ![Azure Data Factory na portálu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory na portálu")

3. Zadejte nebo vyberte následující hodnoty pro dlaždici **Nová továrna dat:**

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Name (Název) | Zadejte název datové továrny. Tento název musí být globálně jedinečný.|
    |Version | Odjet na **V2**. |
    |Předplatné | Vyberte své předplatné Azure. |
    |Skupina prostředků | Vyberte skupinu prostředků, kterou jste vytvořili pomocí skriptu PowerShellu. |
    |Umístění | Umístění je automaticky nastaveno na umístění, které jste zadali při dřívějším vytváření skupiny prostředků. Pro účely tohoto kurzu je umístění nastaveno na **východní USA**. |
    |Povolit GIT|Zrušit zaškrtnutí tohoto políčka|

    ![Vytvoření Azure Data Factory pomocí portálu Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Vytvoření Azure Data Factory pomocí portálu Azure")

4. Vyberte **Vytvořit**. Vytvoření datové továrny může trvat 2 až 4 minuty.

5. Po vytvoření datové továrny obdržíte oznámení **o úspěšném nasazení** pomocí tlačítka **Přejít na prostředek.**  Výběrem **možnosti Přejít na prostředek** otevřete výchozí zobrazení Data Factory.

6. Vyberte **Author & Monitor** a spusťte portál pro vytváření a monitorování Azure Data Factory.

    ![Přehled portálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory – přehled")

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvářete dvě propojené služby v rámci datové továrny.

* **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript Hive, který je spuštěn v clusteru.
* **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří cluster HDInsight a spustí skript Hive. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. V levém podokně stránky **Začínáme** vyberte ikonu **Autor.**

    ![Vytvoření propojené služby Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Vytvoření propojené služby Azure Data Factory")

2. V levém dolním rohu okna vyberte **Připojení** a pak vyberte **+Nový**.

    ![Vytváření připojení ve Službě Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Vytváření připojení ve Službě Azure Data Factory")

3. V dialogovém okně **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**.

    ![Vytvoření propojené služby Azure Storage pro Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Vytvoření propojené služby Azure Storage pro Data Factory")

4. Zadejte následující hodnoty pro propojené služby úložiště:

    |Vlastnost |Hodnota |
    |---|---|
    |Name (Název) |Zadejte `HDIStorageLinkedService`.|
    |Předplatné Azure |V rozevíracím seznamu vyberte předplatné.|
    |Název účtu úložiště |Vyberte účet Azure Storage, který jste vytvořili jako součást skriptu PowerShellu.|

    Vyberte **Testovat připojení** a pokud je úspěšná, pak vyberte **Vytvořit**.

    ![Zadejte název propojené služby Azure Storage](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Zadejte název propojené služby Azure Storage")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu.

2. V okně **Nová propojená služba** vyberte kartu **Výpočetní výkon.**

3. Vyberte **Azure HDInsight**a pak vyberte **Pokračovat**.

    ![Vytvoření propojené služby HDInsight pro Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Vytvoření propojené služby HDInsight pro Azure Data Factory")

4. V okně **Nová propojená služba** zadejte následující hodnoty a zbytek ponechejte jako výchozí:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Name (Název) | Zadejte `HDInsightLinkedService`.|
    | Typ | Vyberte **hdinsight na vyžádání**. |
    | Propojená služba Azure Storage | Vyberte `HDIStorageLinkedService`. |
    | Typ clusteru | Vybrat **hadoop** |
    | Hodnota TTL (Time to Live) | Poskytněte dobu, po kterou má být cluster HDInsight k dispozici, než bude automaticky odstraněn.|
    | ID instančního objektu | Zadejte ID aplikace jistiny služby Azure Active Directory, kterou jste vytvořili jako součást předpokladů. |
    | Klíč instančního objektu | Zadejte ověřovací klíč pro objekt zabezpečení služby Azure Active Directory. |
    | Předpona názvu clusteru | Zadejte hodnotu, která bude předponou pro všechny typy clusteru, které jsou vytvořeny datovou továrnou. |
    |Předplatné |V rozevíracím seznamu vyberte předplatné.|
    | Výběr skupiny prostředků | Vyberte skupinu prostředků, kterou jste vytvořili jako součást skriptu PowerShellu, který jste použili dříve.|
    | Uživatelské jméno Typu operačního systému/Clusteru SSH | Obvykle zadejte uživatelské jméno `sshuser`SSH . |
    | Heslo SSH typu operačního systému/clusteru | Zadání hesla pro uživatele SSH |
    | Typ/uživatelské jméno clusteru os | Zadejte uživatelské jméno clusteru, obvykle `admin`. |
    | Typ operačního systému/heslo clusteru | Zadejte heslo pro uživatele clusteru. |

    Pak vyberte **Vytvořit**.

    ![Poskytněte hodnoty pro propojené služby HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Poskytněte hodnoty pro propojené služby HDInsight")

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **+** tlačítko (plus) a pak vyberte **Pipeline**.

    ![Vytvoření kanálu v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Vytvoření kanálu v Azure Data Factory")

1. V panelu nástrojů **Aktivity** rozbalte **HDInsight**a přetáhněte aktivitu **Hive** na povrch návrháře kanálu. Na kartě **Obecné** zadejte název aktivity.

    ![Přidání aktivit do kanálu Datové továrny](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Přidání aktivit do kanálu Datové továrny")

1. Ujistěte se, že máte vybranou aktivitu Hive, vyberte kartu **Cluster HDI** a v rozevíracím seznamu **HDInsight Propojená služba** vyberte propojenou službu, kterou jste vytvořili dříve, **HDInsightLinkedService**, pro HDInsight.

    ![Poskytnutí podrobností o clusteru HDInsight pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Poskytnutí podrobností o clusteru HDInsight pro kanál")

1. Vyberte kartu **Skript** a proveďte následující kroky:

    1. V **rozevíracím**seznamu služby Script Linked Service vyberte **hdiStorageLinkedService.** Tato hodnota je služba propojená úložištěm, kterou jste vytvořili dříve.

    1. V **části Cesta k souboru**vyberte **Procházet úložiště** a přejděte do umístění, kde je k dispozici ukázkový skript Hive. Pokud jste dříve spustili skript prostředí `adfgetstarted/hivescripts/partitionweblogs.hql`PowerShell, mělo by být toto umístění .

        ![Zadejte podrobnosti o skriptu Hive pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Zadejte podrobnosti o skriptu Hive pro kanál")

    1. V části **Upřesnit** > **parametry**vyberte **možnost Automatické vyplňování ze skriptu**. Tato možnost vyhledá všechny parametry ve skriptu Hive, které vyžadují hodnoty za běhu.

    1. Do textového pole **s hodnotou** přidejte existující složku ve formátu `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`. V této cestě se rozlišují velká a malá písmena. Toto je cesta, kde bude uložen výstup skriptu. Schéma `wasbs` je nezbytné, protože účty úložiště mají nyní požadovaný zabezpečený převod povolen ve výchozím nastavení.

        ![Zadat parametry pro skript Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Zadat parametry pro skript Hive")

1. Chcete-li ověřit kanál, vyberte **možnost Ověřit.** Chcete-li zavřít ověřovací okno, **>>** vyberte tlačítko (šipka doprava).

    ![Ověření kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Ověření kanálu Azure Data Factory")

1. Nakonec vyberte **Publikovat vše** publikovat artefakty do Azure Data Factory.

    ![Publikování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publikování kanálu Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Aktivace kanálu

1. Na panelu nástrojů na povrchu návrháře vyberte **Přidat aktivační událost** > **.**

    ![Aktivace kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Aktivace kanálu Azure Data Factory")

2. V rozbalovacím panelu vyberte **OK.**

## <a name="monitor-a-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. Všimněte si stavu spuštění ve sloupci **Stav.**

    ![Monitorování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorování kanálu Azure Data Factory")

1. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

1. Můžete také vybrat ikonu **Zobrazit spuštění aktivity** a zobrazit spuštění aktivity přidružené k kanálu. Na následujícím snímku obrazovky se zobrazí pouze jedna aktivita spuštěná, protože v kanálu, který jste vytvořili, je pouze jedna aktivita. Pokud chcete přepnout zpět do předchozího zobrazení, vyberte **Potrubí** směrem k horní části stránky.

    ![Monitorování aktivity kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorování aktivity kanálu Azure Data Factory")

## <a name="verify-the-output"></a>Ověření výstupu

1. Chcete-li ověřit výstup, na portálu Azure přejděte na účet úložiště, který jste použili pro tento kurz. Měli byste vidět následující složky nebo kontejnery:

    * Zobrazí **se adfgerstarted/outputfolder,** který obsahuje výstup skriptu Hive, který byl spuštěn jako součást kanálu.

    * Zobrazí **se adfhdidatafactory-\<linked-service-name\<>- časový razítko>** kontejneru. Tento kontejner je výchozí umístění úložiště clusteru HDInsight, který byl vytvořen jako součást spuštění kanálu.

    * Zobrazí se kontejner **adfjobs,** který má protokoly úloh Azure Data Factory.  

        ![Ověření výstupu kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Ověření výstupu kanálu Azure Data Factory")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při vytváření clusteru HDInsight na vyžádání nemusíte explicitně odstraňovat cluster HDInsight. Cluster se odstraní na základě konfigurace, kterou jste zadali při vytváření kanálu. I po odstranění clusteru však účty úložiště přidružené ke clusteru nadále existují. Toto chování je záměrné, takže můžete zachovat data beze změny. Pokud však nechcete data zachovat, můžete odstranit účet úložiště, který jste vytvořili.

Případně můžete odstranit celou skupinu prostředků, kterou jste vytvořili pro tento kurz. Tím odstraníte účet úložiště a Azure Data Factory, které jste vytvořili.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **skupiny prostředků.**
1. Vyberte název skupiny prostředků, který jste vytvořili ve skriptu PowerShellu. Filtr použijte, pokud je v seznamu uvedeno příliš mnoho skupin prostředků. Otevře skupinu prostředků.
1. Na dlaždici **Zdroje** budete mít výchozí účet úložiště a datovou továrnu uvedenou, pokud nesdílíte skupinu prostředků s jinými projekty.
1. Vyberte **Odstranit skupinu prostředků**. Tím odstraníte účet úložiště a data uložená v účtu úložiště.

    ![Skupina prostředků pro odstranění webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Odstranění skupiny prostředků")

1. Chcete-li potvrdit odstranění, zadejte název skupiny prostředků a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak pomocí Azure Data Factory vytvořit cluster HDInsight na vyžádání a spustit úlohy [Apache Hive.](https://hive.apache.org/) Přejdete k dalšímu článku a dozvíte se, jak vytvořit clustery HDInsight s vlastní konfigurací.

> [!div class="nextstepaction"]
> [Vytváření clusterů Azure HDInsight s vlastní konfigurací](hdinsight-hadoop-provision-linux-clusters.md)
