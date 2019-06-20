---
title: 'Kurz: Vytváření clusterů na vyžádání Apache Hadoop v Azure HDInsight pomocí Data Factory '
description: Zjistěte, jak vytvářet clustery na vyžádání Apache Hadoop v HDInsight pomocí Azure Data Factory.
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 04/18/2019
ms.openlocfilehash: 937cb9ba2d9ce63ba36805bc7a946e8ee6e18cf3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275446"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Kurz: Vytváření clusterů na vyžádání Apache Hadoop v HDInsight pomocí Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte, jak vytvořit [Apache Hadoop](https://hadoop.apache.org/) clusteru na vyžádání v Azure HDInsight pomocí Azure Data Factory. Pak použijete datové kanály ve službě Azure Data Factory ke spuštění úlohy Hive a cluster odstranit. Na konci tohoto kurzu se dozvíte, jak pro zprovoznění úlohu velké objemy dat spouštět, kde se vytvoření clusteru, spuštění úlohy a odstranění clusteru provádět podle plánu.

Tento kurz se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Porozumět aktivitě Azure Data Factory
> * Vytvoření datové továrny pomocí webu Azure portal
> * Vytvoření propojených služeb
> * Vytvoření kanálu
> * Aktivace kanálu
> * Monitorování kanálu
> * Ověření výstupu

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* PowerShell [Az modulu](https://docs.microsoft.com/powershell/azure/overview) nainstalované.

* Instanční objekt Azure Active Directory. Jakmile vytvoříte instanční objekt služby, je potřeba načíst **ID aplikace** a **ověřovací klíč** podle pokynů v odkazovaném článku. Tyto hodnoty budete potřebovat později v tomto kurzu. Také se ujistěte, že instanční objekt služby je členem skupiny *Přispěvatel* pro dané předplatné nebo skupinu prostředků, ve kterém se cluster vytvoří. Pokyny k načtení požadované hodnoty a přiřazení správných rolí najdete v tématu [vytvoření instančního objektu služby Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Vytváření objektů předběžné Azure

V této části vytvoříte různé objekty, které se použije pro cluster HDInsight, které vytvoříte na vyžádání. Na vytvořený účet úložiště bude obsahovat ukázky [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skriptu (`hivescript.hql`), který používáte k simulaci ukázku [Apache Hive](https://hive.apache.org/) úlohu, která běží v clusteru.

Tato část používá skript Azure Powershellu k vytvoření účtu úložiště a zkopírujte požadované soubory v rámci účtu úložiště. Ukázkový skript Azure Powershellu v této části provádí následující úlohy:

1. Přihlásí do Azure.
2. Vytvoří skupinu prostředků Azure.
3. Vytvoří účet služby Azure Storage.
4. Vytvoří kontejner objektů Blob v účtu úložiště
5. Ukázkový skript HiveQL zkopíruje (**hivescript.hql**) kontejneru objektů Blob. Skript je k dispozici na [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Ukázkový skript je již k dispozici v jiném veřejném kontejneru objektů Blob. Níže uvedeného skriptu Powershellu vytvoří kopii těchto souborů do účtu služby Azure Storage, který vytvoří.

> [!WARNING]  
> Druh účtu úložiště `BlobStorage` nelze použít pro clustery HDInsight.

**Vytvoření účtu úložiště a kopírovat soubory pomocí Azure Powershellu:**

> [!IMPORTANT]  
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který bude vytvořen skriptem.
> Zapište si **název skupiny prostředků**, **název účtu úložiště**, a **klíč účtu úložiště** výstupem skriptem. Budete je potřebovat v další části.

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
    -Container $sourceContainerName

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

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

**Ověření vytvoření účtu úložiště**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **skupiny prostředků** v levém podokně.
3. Vyberte název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr.
4. Na **prostředky** dlaždici se zobrazí jeden prostředek, které jsou uvedeny, dokud skupinu prostředků sdílet s jinými projekty. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Vyberte název účtu úložiště.
5. Vyberte **objekty BLOB** dlaždice.
6. Vyberte **adfgetstarted** kontejneru. Zobrazit složku s názvem **hivescripts**.
7. Otevřete složku a ujistěte se, že obsahuje ukázkový soubor skriptu, **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Porozumět aktivitě Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestruje a automatizuje přesouvání a transformaci dat. Azure Data Factory můžete vytvářet HDInsight Hadoop cluster just-in-time ke zpracování vstupní datový řez a po dokončení zpracování se cluster odstranit. 

Ve službě Azure Data Factory služby data factory může mít jeden nebo více datových kanálů. Datový kanál má jednu nebo víc aktivit. Existují dva typy aktivit:

- [Aktivity přesunu dat](../data-factory/copy-activity-overview.md) -použít aktivity přesunu dat pro přesun dat ze zdrojového úložiště dat do cílového úložiště dat.
- [Aktivity transformace dat](../data-factory/transform-data.md). Transformovat a zpracovávat data pomocí aktivity transformace dat. Aktivita HDInsight Hive je jedním z aktivity transformace podporovaných službou Data Factory. V tomto kurzu použijete aktivitu Hive transformace.

V tomto článku nakonfigurujete aktivitu Hive k vytvoření clusteru HDInsight Hadoop na vyžádání. Při spuštění aktivity pro zpracování dat, stane se toto:

1. Pro vás just-in-time zpracování řezu se automaticky vytvoří cluster HDInsight Hadoop. 

2. Vstupní data zpracovává spouští skript HiveQL v clusteru. V tomto kurzu se skript HiveQL spojená s aktivitou hivu provede následující akce:

    - Používá existující tabulku (*hivesampletable*) k vytvoření další tabulky **HiveSampleOut**.
    - Naplní **HiveSampleOut** tabulky obsahující pouze konkrétní sloupce z původní *hivesampletable*.
    
3. Po dokončení zpracování a cluster nakonfigurovaného množství času (nastavení timeToLive) nečinnosti se odstraní cluster HDInsight Hadoop. Pokud další datový řez je k dispozici pro zpracování se v tuto chvíli nečinná timeToLive, stejný cluster se používá ke zpracování řezu.  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo přejděte na **+ vytvořit prostředek** > **Analytics** > **služby Data Factory**.

    ![Na portálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory na portálu")

3. Zadejte nebo vyberte následující hodnoty **nová datová továrna** dlaždice:

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Název | Zadejte název datové továrny. Tento název musí být globálně jedinečný.|
    |Předplatné | Vyberte své předplatné Azure. |
    |Skupina prostředků | Vyberte **použít existující** a pak vyberte skupinu prostředků, kterou jste vytvořili, pomocí Powershellového skriptu. |
    |Version | Ponechte **V2**. |
    |Location | Umístění se nastaví automaticky do umístění, které jste zadali při vytváření skupiny prostředků dříve. Pro účely tohoto kurzu, umístění se nastaví **USA – východ**. |

    ![Vytvoření datové továrny Azure pomocí webu Azure portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "vytvoření služby Azure Data Factory pomocí webu Azure portal")

4. Vyberte **Vytvořit**. Vytváří se objekt pro vytváření dat může trvat mezi 2 až 4 minuty.

5. Po vytvoření datové továrny, vám přijde **nasazení bylo úspěšné** oznámení s **přejít k prostředku** tlačítko.  Vyberte **přejít k prostředku** otevřete výchozího zobrazení datové továrny.

6. Vyberte **vytvořit a monitorovat** ke spuštění Azure Data Factory pro vytváření a monitorování portálu.

    ![Přehled služby Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory – přehled")

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte dvě propojené služby v rámci vaší datové továrně.

- **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript Hive, který běží v clusteru.
- **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří HDInsight cluster a spustí skript Hive. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. V levém podokně **pusťme se do práce** stránky, vyberte **Autor** ikonu.

    ![Vytvoření služby Azure Data Factory propojené](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "vytvoření Azure Data Factory propojené služby")

2. Vyberte **připojení** z levého dolního rohu okna a pak vyberte **+ nová**.

    ![Vytvoření připojení v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "vytvořit připojení ve službě Azure Data Factory")

3. V **Nová propojená služba** dialogu **Azure Blob Storage** a pak vyberte **pokračovat**.

    ![Propojená služba Data Factory vytvořit Azure Storage](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "propojenou službu Data Factory vytvořit Azure Storage")

4. Zadejte následující hodnoty na propojenou službu storage:

    |Vlastnost |Hodnota |
    |---|---|
    |Name |Zadejte `HDIStorageLinkedService`.|
    |Předplatné Azure |Vyberte své předplatné z rozevíracího seznamu.|
    |Název účtu úložiště |Vyberte účet služby Azure Storage, kterou jste vytvořili jako součást skriptu prostředí PowerShell.|

    Pak vyberte **Dokončit**.

    ![Zadejte název pro službu Azure Storage propojená služba](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "zadejte název pro službu Azure Storage propojená služba")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu.

2. V **Nová propojená služba** okna, vyberte **Compute** kartu.

3. Vyberte **Azure HDInsight**a pak vyberte **pokračovat**.

    ![HDInsight vytvořit propojenou službu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight vytvořit propojenou službu Azure Data Factory")

4. V **Nová propojená služba** okno, zadejte následující hodnoty a ponechte zbývající jako výchozí:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Name | Zadejte `HDInsightLinkedService`.|
    | Type | Vyberte **HDInsight na vyžádání**. |
    | Propojená služba Azure Storage | Vyberte `HDIStorageLinkedService`. |
    | Typ clusteru | Vyberte **hadoop** |
    | Hodnota TTL (Time to Live) | Zadejte dobu trvání, pro které chcete cluster HDInsight bude k dispozici před jejich odstraněním automaticky.|
    | ID instančního objektu | Zadejte ID aplikace pro objekt služby Azure Active Directory, kterou jste vytvořili jako součást požadavků. |
    | Klíč instančního objektu | Zadejte ověřovací klíč instančního objektu služby Azure Active Directory. |
    | Předpona názvu clusteru | Zadejte hodnotu, která bude mu předcházet text pro všechny typy clusteru, které jsou vytvořeny pomocí služby data factory. |
    |Předplatné |Vyberte své předplatné z rozevíracího seznamu.|
    | Výběr skupiny prostředků | Vyberte skupinu prostředků, kterou jste vytvořili jako součást skriptu prostředí PowerShell, který jste použili dříve.|
    |Zvolte oblast | Z rozevíracího seznamu vyberte oblast.|
    | Uživatelské jméno SSH clusteru/typu operačního systému | Zadejte uživatelské jméno SSH, běžně `sshuser`. |
    | Heslo SSH operačního systému clusteru/typu | Zadejte heslo pro uživatele SSH |
    | Typ operačního systému/uživatelské jméno clusteru | Zadejte uživatelské jméno clusteru běžně `admin`. |
    | Heslo uživatele operačního systému clusteru/typu | Zadejte heslo pro uživatele clusteru. |

    Pak vyberte **Dokončit**.

    ![Zadejte hodnoty pro HDInsight propojená služba](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "zadejte hodnoty pro HDInsight propojená služba")

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte tlačítko **+** (plus) a pak vyberte **Kanál**.

    ![Vytvoření kanálu ve službě Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "ve službě Azure Data Factory vytvořit kanál")

2. V **aktivity** sady nástrojů, rozbalte **HDInsight**a přetáhněte ji **Hive** aktivity na plochu návrháře kanálu. V **Obecné** kartu, zadejte název aktivity.

    ![Přidání aktivit do kanálu služby Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "přidání aktivit do kanálu služby Data Factory")

3. Ujistěte se, že je vybraná aktivita Hive, vyberte **Cluster Hdinsight** kartu a od **propojená služba HDInsight** rozevíracího seznamu vyberte propojenou službu jste vytvořili dříve,  **HDinightLinkedService**, pro HDInsight.

    ![Zadejte podrobnosti o clusteru HDInsight pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "HDInsight poskytují podrobnosti o clusteru pro kanál")

4. Vyberte **skript** kartě a proveďte následující kroky:

    1. Pro **propojenou službu skript**vyberte **HDIStorageLinkedService** z rozevíracího seznamu. Tato hodnota je na propojenou službu storage, kterou jste vytvořili dříve.

    1. Pro **cesta k souboru**vyberte **procházet úložiště** a přejděte do umístění, kde je k dispozici ukázkový skript Hive. Pokud jste dříve spustili skript prostředí PowerShell, toto umístění by mělo být `adfgetstarted/hivescripts/hivescript.hql`.

        ![Zadejte podrobnosti skriptu Hive pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive zadejte podrobnosti o skriptu pro kanál")

    1. V části **Upřesnit** > **parametry**vyberte **automaticky vyplnit ze skriptu**. Tato možnost hledá žádné parametry ve skriptu Hive, které vyžadují hodnoty v době běhu. Skript, který používáte (**hivescript.hql**) má **výstup** parametru. Zadejte **hodnotu** ve formátu `wasb://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` tak, aby odkazovala na existující složku ve službě Azure Storage. V této cestě se rozlišují velká a malá písmena. Toto je cesta, kam se má uložit výstup skriptu.
    
        ![Zadejte parametry skriptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "zadat parametry pro skript Hive")

1. Vyberte **ověřit** ověření kanálu. Výběrem tlačítka **>>** (šipka doprava) zavřete okno ověřování.

    ![Ověření kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "ověření kanálu Azure Data Factory")

1. Nakonec vyberte **Publikovat vše** publikovat artefakty do služby Azure Data Factory.

    ![Publikování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publikování kanálu Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Aktivace kanálu

1. Vyberte z panelu nástrojů na plochu návrháře **aktivační událost přidat** > **aktivovat**.

    ![Aktivace kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "aktivace kanálu Azure Data Factory")

2. Vyberte **Dokončit** v rozbalovací postranní panel.

## <a name="monitor-a-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. Všimněte si, že stav pro běh **stav** sloupce.

    ![Monitorování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "monitorování kanálu Azure Data Factory")

1. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

1. Můžete také vybrat **zobrazit spuštění aktivit** ikonu zobrazíte spuštění aktivit související se kanálu. Na snímku obrazovky níže uvidíte jenom jednu aktivitu spustit, protože pouze jednu aktivitu v kanálu, který jste vytvořili. Pokud chcete přepnout zpět na předchozí zobrazení, vyberte **kanály** horní části stránky.

    ![Monitorujte aktivity kanálů Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "monitorujte aktivity kanálů Azure Data Factory")

## <a name="verify-the-output"></a>Ověření výstupu

1. K ověření výstupu na webu Azure Portal přejděte do účtu úložiště, který jste použili pro účely tohoto kurzu. Měli byste vidět následující složky nebo kontejnerů:

    - Zobrazí **adfgerstarted/outputfolder** , který obsahuje výstup skriptu Hive, který byl spuštěn jako součást kanálu.

    - Zobrazí **adfhdidatafactory -\<propojené service-name >-\<časové razítko >** kontejneru. Tento kontejner je výchozí umístění úložiště pro cluster HDInsight, který byl vytvořen jako součást spuštění kanálu.

    - Zobrazí **adfjobs** protokoly kontejneru, který se má úloha služby Azure Data Factory.  

        ![Ověření výstupu kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "ověření výstupu kanálu Azure Data Factory")

## <a name="clean-up-the-tutorial"></a>Vyčistěte kurz

Při vytváření clusteru HDInsight na vyžádání není potřeba explicitně odstranění clusteru HDInsight. Při odstranění clusteru, v závislosti na konfiguraci, kterou jste zadali při vytváření kanálu. Ale i po odstranění clusteru účty úložiště přidružené ke clusteru i nadále existovat. Toto chování je záměrné, tak, aby vaše data můžete ponechat beze změny. Pokud nechcete zachovat data, však může odstranit účet úložiště, který jste vytvořili.

Alternativně můžete odstranit celou skupinu prostředků, kterou jste vytvořili pro účely tohoto kurzu. Tím se odstraní účet úložiště a Azure Data Factory, který jste vytvořili.

### <a name="delete-the-resource-group"></a>Odstranit skupinu prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vyberte **skupiny prostředků** v levém podokně.
1. Vyberte název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr. Otevře se skupiny prostředků.
1. Na **prostředky** dlaždice, musí mít výchozí účet úložiště a data factory, uvedeny, dokud skupinu prostředků sdílet s jinými projekty.
1. Vyberte **Odstranit skupinu prostředků**. Tím odstraníte účet úložiště a data uložená v účtu úložiště.

    ![Odstranit skupinu prostředků](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "odstranit skupinu prostředků")

1. Zadejte název skupiny prostředků pro potvrzení odstranění a pak vyberte **odstranit**.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak pomocí Azure Data Factory k vytvoření clusteru HDInsight na vyžádání a spusťte [Apache Hive](https://hive.apache.org/) úlohy. Přejděte k dalším článku se naučíte, jak vytvářet clustery HDInsight s vlastní konfigurací.

> [!div class="nextstepaction"]
>[Vytvoření clusterů Azure HDInsight s vlastní konfigurací](hdinsight-hadoop-provision-linux-clusters.md)


