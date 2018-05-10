---
title: 'Kurz: Vytvoření clusterů systému Hadoop na vyžádání v Azure HDInsight pomocí služby Data Factory | Microsoft Docs'
description: Naučte se vytvářet na vyžádání clusterů systému Hadoop v HDInsight pomocí Azure Data Factory.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: 53ff14e00b88f6d182579ba0d9df630fae9b3d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Kurz: Vytvoření na vyžádání clusterů systému Hadoop v HDInsight pomocí Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku zjistěte, jak vytvořit Hadoop cluster na vyžádání v Azure HDInsight pomocí Azure Data Factory. Poté použije datových kanálů v Azure Data Factory ke spuštění úlohy Hive a odstranění clusteru. Na konci tohoto kurzu zjistíte, jak pro zprovoznění úlohu velkých objemů dat spustit, kde se provádí vytváření clusteru, spusťte úlohu a odstranění clusteru podle plánu.

Tento kurz se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Porozumět aktivitě Azure Data Factory
> * Vytvořte objekt pro vytváření dat pomocí portálu Azure
> * Vytvoření propojených služeb
> * Vytvoření kanálu
> * Aktivační událost kanálu
> * Monitorování kanálu
> * Ověření výstupu

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Azure Powershell Pokyny najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

* Objekt zabezpečení služby Azure Active Directory. Po vytvoření objektu služby, je nutné načíst **ID aplikace** a **ověřovací klíč** pomocí pokynů v článku znalostní báze propojený. Je nutné tyto hodnoty později v tomto kurzu. Taky se ujistěte, je členem objektu služby *Přispěvatel* role předplatné nebo skupinu prostředků, ve kterém je vytvořen cluster. Pokyny k načtení požadované hodnoty a přiřazení správných rolí najdete v tématu [vytvořit objekt služby Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

V této části vytvoříte účet úložiště, který se použije jako výchozí úložiště pro cluster HDInsight, které vytvoříte na vyžádání. Tento účet úložiště také obsahuje ukázkový skript HiveQL (**hivescript.hql**), použijte k simulaci ukázkové úlohy Hive, který běží na clusteru.

Tato část používá skript Azure PowerShell k vytvoření účtu úložiště a zkopírujte požadované soubory v rámci účtu úložiště. Prostředí Azure PowerShell ukázkový skript v této části provede následující úlohy:

1. Protokoly v do Azure.
2. Vytvoří skupinu prostředků Azure.
3. Vytvoří účet služby Azure Storage.
4. Vytvoří kontejner objektů Blob v účtu úložiště
5. Zkopíruje ukázkový skript HiveQL (**hivescript.hql**) kontejner objektů Blob. Skript je k dispozici na [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Ukázkový skript je již k dispozici v jinou kontejneru veřejného objektu Blob. Následující skript prostředí PowerShell vytvoří kopii těchto souborů do účtu úložiště Azure, který vytváří.


**Vytvoření účtu úložiště a kopírovat soubory pomocí Azure PowerShell:**
> [!IMPORTANT]
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který bude vytvořen skriptem.
> Zapište **název skupiny prostředků**, **název účtu úložiště**, a **klíč účtu úložiště** výstupem skriptem. Je nutné v další části.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
<<<<<<< HEAD
Login-AzureRmAccount
=======
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
>>>>>>> refs/remotes/MicrosoftDocs/release-build-hdinsight-2018
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
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
3. Dvakrát klikněte na název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr.
4. Na **prostředky** dlaždice, zobrazí jeden prostředek uvedené Pokud sdílíte s jinými projekty skupiny prostředků. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Vyberte název účtu úložiště.
5. Vyberte **objekty BLOB** dlaždice.
6. Vyberte **adfgetstarted** kontejneru. Zobrazí složku s názvem **hivescripts**.
7. Otevřete složku a zajistěte, aby obsahuje ukázkový soubor skriptu, **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Porozumět aktivitě Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestruje a automatizuje přesouvání a transformaci dat. Azure Data Factory můžete vytvořit HDInsight Hadoop clusteru v běhu ke zpracování řez vstupních dat a po dokončení zpracování odstranění clusteru. 

V Azure Data Factory objekt pro vytváření dat může mít jeden nebo více datových kanálů. Datový kanál obsahuje jeden nebo více aktivit. Existují dva typy aktivit:

* [Aktivity přesunu dat](../data-factory/copy-activity-overview.md) -použít aktivity přesunu dat pro přesun dat z jiného úložiště dat zdrojového do cílového úložiště dat.
* [Aktivity transformace dat](../data-factory/transform-data.md). Aktivity transformace dat použijete transformace nebo zpracovat data. Aktivita HDInsight Hive je jedním z aktivit transformace podporovaných službou Data Factory. V tomto kurzu použijete aktivitu transformace Hive.

V tomto článku nakonfigurujete aktivity Hive k vytvoření clusteru HDInsight Hadoop na vyžádání. Při spuštění aktivity pro zpracování dat, stane se toto:

1. Pro můžete v běhu při zpracování řezu se automaticky vytvoří cluster služby HDInsight Hadoop. 

2. Spuštění skriptu HiveQL v clusteru zpracovává vstupní data. V tomto kurzu skript HiveQL přidružený k aktivitě hive provede následující akce:

    * Používá existující tabulce (*hivesampletable*) Chcete-li vytvořit jinou tabulkou **HiveSampleOut**.
    * Naplní **HiveSampleOut** tabulku s pouze konkrétní sloupce z původní *hivesampletable*.

3. Po dokončení zpracování a cluster nakonfigurovaného množství času (nastavení timeToLive) nečinný odstranění clusteru HDInsight Hadoop. Pokud další datový řez je dostupný pro zpracování s timeToLive dobu nečinnosti, stejného clusteru se používá ke zpracování řezu.  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

2. Na portálu Azure vyberte **vytvořit prostředek** > **Data + analýzy** > **Data Factory**.

    ![Na portálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory na portálu.")

2. Zadejte nebo vyberte hodnoty, jak je znázorněno na následujícím snímku obrazovky:

    ![Vytvoření služby Azure Data Factory pomocí portálu Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "vytvořit Azure Data Factory pomocí portálu Azure")

    Zadejte nebo vyberte tyto hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název** |  Zadejte název služby data Factory. Tento název musí být globálně jedinečný.|
    |**Předplatné**     |  Vyberte své předplatné Azure. |
    |**Skupina prostředků**     | Vyberte **použít existující** a pak vyberte skupinu prostředků, kterou jste vytvořili, pomocí skriptu prostředí PowerShell. |
    |**Verze**     | Vyberte **V2 (Preview)** |
    |**Umístění**     | Umístění se automaticky nastaví na umístění, které jste zadali při vytváření skupiny prostředků dříve. V tomto kurzu umístění se nastaví **východní USA 2**. |
    

3. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**. Zobrazí se nová dlaždice s názvem **odesílání nasazení** na řídicím panelu portálu. Vytváření objekt pro vytváření dat může trvat mezi 2 až 4 minuty.

    ![Průběh nasazení šablony](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "průběh nasazení šablony") 
 
4. Po vytvoření objektu pro vytváření dat na portálu se zobrazuje přehled služby data Factory.

    ![Přehled služby Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "přehled Azure Data Factory")

5. Vyberte **Autor & monitorování** spustíte Azure Data Factory vytváření obsahu a monitorování portálu.

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části můžete vytvářet dvě propojené služby v rámci vaší služby data factory.

- **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje taky skriptu Hive, který běží na clusteru.
- **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří HDInsight cluster a spouští skript Hive. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho.

###  <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. V levém podokně nástroje **můžeme začít** vyberte **upravit** ikonu.

    ![Vytvoření služby Azure Data Factory propojené](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "vytvoření služby Azure Data Factory propojené")

2. Vyberte **připojení** z okraje v levém dolním rohu okna a potom vyberte **+ nový**.

    ![Vytvoření připojení v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "v Azure Data Factory vytvořit připojení")

3. V **Nová propojená služba** dialogové okno, vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.

    ![Propojená služba Data Factory vytvořit Azure Storage](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "propojená služba Data Factory vytvořit Azure Storage")

4. Zadejte název úložiště, propojené služby, vyberte účet úložiště Azure jste vytvořili v rámci skriptu prostředí PowerShell a potom vyberte **Dokončit**.

    ![Zadejte název pro Azure Storage propojená služba](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "zadejte název pro Azure Storage propojená služba")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu.

2. V okně **Nová propojená služba** vyberte **Compute** > **Azure HDInsight** a potom vyberte **Pokračovat**.

    ![Vytvořit HDInsight propojená služba Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight vytvoření propojené služby pro Azure Data Factory")

3. V **Nová propojená služba** okno, zadejte požadované hodnoty.

    ![Zadejte hodnoty pro HDInsight propojená služba](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "zadejte hodnoty pro HDInsight propojená služba")

    Zadejte následující hodnoty a ponechejte zbývající jako výchozí.

    | Vlastnost | Popis |
    | --- | --- |
    | Název | Zadejte název pro propojená služba HDInsight |
    | Typ | Vyberte **HDInsight na vyžádání** |
    | Propojená služba Azure Storage | Vyberte úložiště propojené služby, kterou jste vytvořili dříve. |
    | Typ clusteru | Vyberte **hadoop** |
    | Hodnota TTL | Zadejte dobu trvání, pro které chcete cluster HDInsight k dispozici, než bude automaticky odstraněna.|
    | ID instančního objektu | Zadejte ID objektu služby Azure Active Directory, kterou jste vytvořili v rámci požadované součásti aplikace |
    | Hlavní klíč služby | Zadejte ověřovací klíč pro objekt zabezpečení služby Azure Active Directory |
    | Předpona názvu clusteru | Zadejte hodnotu, která bude obsahovat předponu pro všechny typy clusteru, které jsou vytvořené pomocí objektu pro vytváření dat |
    | Skupina prostředků | Vybrat skupinu prostředků, kterou jste vytvořili v rámci skriptu prostředí PowerShell, které jste použili předtím| 
    | Uživatelské jméno SSH clusteru | Zadejte uživatelské jméno SSH |
    | Heslo SSH clusteru | Zadejte heslo pro uživatele SSH |

    Vyberte **Finish** (Dokončit).

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte tlačítko **+** (plus) a pak vyberte **Kanál**.

    ![V Azure Data Factory vytvořit kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "v Azure Data Factory vytvořit kanál")

2. V **aktivity** sada nástrojů, rozbalte položku **HDInsight**a přetáhněte ji **Hive** aktivity na plochu návrháře kanálu. V **Obecné** kartě, zadejte název aktivity.

    ![Přidání aktivit do kanál služby Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "přidání aktivit do kanál služby Data Factory")

3. Ujistěte se, že máte vybraná aktivita Hive, vyberte **clusteru HDI** kartě a z **propojená služba HDInsight** rozevíracího seznamu, vyberte propojenou službu jste vytvořili dříve pro HDInsight.

    ![Zadejte podrobnosti o clusteru HDInsight pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "HDInsight poskytují podrobnosti o clusteru pro kanál")

4. Vyberte **skriptu** kartě a proveďte následující kroky:

    a. Pro **propojené služby skriptu**, vyberte **HDIStorageLinkedService**. Tato hodnota je propojená služba úložiště, které jste vytvořili dříve.

    b. Pro **cesta k souboru**, vyberte **procházet úložiště** a přejděte do umístění, kde je k dispozici ukázkový skript Hive. Pokud jste dříve spustili skript prostředí PowerShell, musí být toto umístění `adfgetstarted/hivescripts/hivescript.hql`.

    ![Zadat podrobnosti skriptu Hive pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive zadat podrobnosti skriptu pro kanál")

    c. V části **Upřesnit** > **parametry**, vyberte **automatické vyplňování ze skriptu**. Tato možnost hledá žádné parametry skriptu Hive, které vyžadují hodnoty za běhu. Můžete použít skript (**hivescript.hql**) má **výstup** parametr. Zadejte hodnotu ve formátu `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` tak, aby odkazoval k existující složce ve vašem úložišti Azure. V této cestě se rozlišují velká a malá písmena. Jedná se o cestu, kam se má uložit výstup skriptu.

    ![Zadejte parametry pro skriptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "zadat parametry skriptu Hive")

5. Vyberte **ověřením** ověření kanálu. Výběrem tlačítka **>>** (šipka doprava) zavřete okno ověřování.

    ![Ověření kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "ověření kanál Azure Data Factory")

5. Nakonec vyberte **publikovat všechny** publikovat artefakty do Azure Data Factory.

    ![Publikování kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publikovat kanál Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Aktivační událost kanálu

1. Na panelu nástrojů na plochu návrháře vyberte **aktivační událost** > **nyní aktivační událost**.

    ![Aktivovat kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "aktivovat kanál Azure Data Factory")

2. Vyberte **Dokončit** panelu automaticky otevírané okno straně.

## <a name="monitor-a-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. Všimněte si stav pro běh **stav** sloupce.

    ![Monitorovat kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "monitorovat kanál Azure Data Factory")

2. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

3. Můžete také vybrat **zobrazení aktivity spustí** ikonu zobrazíte aktivity při spuštění přidružené kanálu. Na tomto snímku obrazovky uvidíte jenom jedna aktivita spustit, protože je jenom jedna aktivita v kanálu, který jste vytvořili. Chcete-li přepnout zpět na předchozí zobrazení, vyberte **kanály** směrem k horní části stránky.

    ![Sledovat činnost kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "sledovat činnost kanál Azure Data Factory")


## <a name="verify-the-output"></a>Ověření výstupu

1. Pokud chcete ověřit výstup, na portálu Azure přejděte k účtu úložiště, který jste použili v tomto kurzu. Měli byste vidět následující složky nebo kontejnerů:

    - Zobrazí **adfgerstarted/outputfolder** obsahující výstup skriptu Hive, která byla spuštěna jako součást kanálu.

    - Zobrazí **adfhdidatafactory -\<propojené service-name >-\<časové razítko >** kontejneru. Tento kontejner je výchozí umístění úložiště clusteru HDInsight, který byl vytvořen jako součást spuštění kanálu.

    - Zobrazí **adfjobs** protokoly kontejneru, který má úlohy Azure Data Factory.  

        ![Zkontrolujte výstup kanál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "ověřte výstup kanál Azure Data Factory")


## <a name="clean-up-the-tutorial"></a>Vyčistěte kurz

Pomocí na deman vytváření clusteru HDInsight, není potřeba explicitně odstranit clusteru HDInsight. Odstranění clusteru na základě konfigurace, které jste zadali při vytváření kanálu. Ale i po odstranění clusteru, účty úložiště přidružené ke clusteru nadále existovat. Toto chování je záměrné tak, aby vaše data můžete ponechat beze změn. Pokud nechcete zachovat data, ale může odstranit účet úložiště, který jste vytvořili.

Alternativně můžete odstranit celý zdroj skupinu, kterou jste vytvořili v tomto kurzu. Tím odstraníte účet úložiště a Azure Data Factory, který jste vytvořili.

### <a name="delete-the-resource-group"></a>Odstraňte skupinu prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **skupiny prostředků** v levém podokně.
3. Vyberte název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr. Otevře se skupina prostředků.
4. Na **prostředky** dlaždice, musí mít výchozí účet úložiště a objektu pro vytváření dat uvedené Pokud sdílíte s jinými projekty skupiny prostředků.
5. Vyberte **Odstranit skupinu prostředků**. Díky tomu odstraní účet úložiště a data uložená v účtu úložiště.

    ![Odstraňte skupinu prostředků](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "odstranit skupinu prostředků")

6. Zadejte název skupiny prostředků a potvrďte odstranění a potom vyberte **odstranit**.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat Azure Data Factory k vytvoření clusteru HDInsight na vyžádání a spouštět úlohy Hive. Přechodu na další artciel se dozvíte, jak k vytvoření clusterů HDInsight pomocí vlastní konfigurace.

> [!div class="nextstepaction"]
>[Azure HDInsight vytvářet clustery s vlastní konfigurace](hdinsight-hadoop-provision-linux-clusters.md)


