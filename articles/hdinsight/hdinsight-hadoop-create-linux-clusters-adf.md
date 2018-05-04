---
title: Vytváření clusterů systému Hadoop na vyžádání pomocí služby Data Factory - Azure HDInsight | Microsoft Docs
description: Naučte se vytvářet na vyžádání clusterů systému Hadoop v HDInsight pomocí Azure Data Factory.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: 6344b9a50f182a2b9ab05562c29099c9d6976f0b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Vytvářet na vyžádání clusterů systému Hadoop v HDInsight pomocí Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) je služba integrace cloudových dat, která orchestruje a automatizuje přesouvání a transformaci dat. Může vytvořit HDInsight Hadoop clusteru v běhu ke zpracování řez vstupních dat a po dokončení zpracování odstranění clusteru. Některé z výhod používání cluster systému HDInsight Hadoop na vyžádání jsou:

- Můžete pouze platím čas úlohy běží na clusteru HDInsight Hadoop (plus krátké doby nečinnosti konfigurovat). Fakturace pro clustery služby HDInsight se fakturují za minutu, zda jsou jejich používání, nebo ne. Při použití na vyžádání propojené služby HDInsight v objektu pro vytváření dat, jsou vytvářet clustery na vyžádání. A clustery jsou automaticky odstraněny při dokončení úlohy. Proto platíte jenom pro úlohu s krátké době nečinnosti (nastavení time to live).
- Můžete vytvořit pracovní postup pomocí objektu pro vytváření dat kanál. Například můžete mít kanál kopírování dat z místního serveru SQL do Azure blob storage, zpracování dat při spuštění skriptu Hive a Pig skript na cluster systému HDInsight Hadoop na vyžádání. Zkopírujte výsledná data do Azure SQL Data Warehouse pro BI aplikace využívat.
- Můžete naplánovat pracovní postup spustit pravidelně (HODINOVĚ, denně, týdně, měsíčně, atd.).

V Azure Data Factory objekt pro vytváření dat může mít jeden nebo více datových kanálů. Datový kanál obsahuje jeden nebo více aktivit. Existují dva typy aktivit: [aktivity přesunu dat](../data-factory/copy-activity-overview.md) a [aktivit transformace dat](../data-factory/transform-data.md). Pro přesun dat z jiného úložiště dat zdrojového do cílového úložiště dat použijete aktivity přesunu dat (v současné době pouze aktivita kopírování). Aktivity transformace dat použijete transformace nebo zpracovat data. Aktivita HDInsight Hive je jedním z aktivit transformace podporovaných službou Data Factory. V tomto kurzu použijete aktivitu transformace Hive.

Můžete nakonfigurovat aktivitu hive používat vlastní cluster HDInsight Hadoop nebo cluster systému HDInsight Hadoop na vyžádání. V tomto kurzu aktivity Hive v kanálu objekt pro vytváření dat je nakonfigurován na používání clusteru HDInsight na vyžádání. Proto při spuštění aktivity ke zpracování dat řezu, stane se toto:

1. Pro můžete v běhu při zpracování řezu se automaticky vytvoří cluster HDInsight Hadoop.  
2. Spuštění skriptu HiveQL v clusteru zpracovává vstupní data.
3. Po dokončení zpracování a cluster nakonfigurovaného množství času (nastavení timeToLive) nečinný odstranění clusteru HDInsight Hadoop. Pokud další datový řez je dostupný pro zpracování s timeToLive dobu nečinnosti, stejného clusteru se používá ke zpracování řezu.  

V tomto kurzu skript HiveQL přidružený k aktivitě hive provede následující akce:

1. Vytvoří externí tabulku, která odkazuje na data protokolu nezpracovaná webové uložené v Azure Blob storage.
2. Oddíly nezpracovaná data podle roku a měsíce.
3. Ukládá oddílů data do Azure blob storage.

V tomto kurzu skript HiveQL přidružený k aktivitě hive vytvoří externí tabulku, která odkazuje na nezpracovaná webové protokolu data uložená ve službě Azure Blob Storage. Zde jsou řádky vzorku pro každý měsíc ve vstupním souboru.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Skript HiveQL oddíly nezpracovaná data podle roku a měsíce. Vytvoří tři výstupní složky podle předchozích vstup. Daná složka obsahuje soubor s položky z každý měsíc.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Seznam aktivit transformace dat služby Data Factory kromě Hive aktivity, naleznete v části [transformovat a analyzovat pomocí Azure Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> V současné době můžete vytvořit pouze verze clusteru HDInsight 3.2 z Azure Data Factory.

## <a name="prerequisites"></a>Požadavky
Než začnete plnit pokyny v tomto článku, musíte mít následující položky:

* [Předplatné Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure Powershell

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Příprava účtu úložiště
V tomto scénáři můžete použít až tři účty úložiště:

- Výchozí účet úložiště pro HDInsight cluster
- účet úložiště pro vstupních dat
- účet úložiště pro výstupní data

Pro zjednodušení tento kurz, použijete jeden účet úložiště k obsluze tři účely. Najít v této části prostředí Azure PowerShell ukázkový skript provede následující úlohy:

1. Přihlásí se k Azure.
2. Vytvořte skupinu prostředků Azure.
3. Vytvořit účet služby Azure Storage
4. Vytvořte kontejner objektů Blob v účtu úložiště
5. Zkopírujte následující dva soubory do kontejneru objektů Blob:

   * Vstupní datový soubor: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Skript HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Oba soubory jsou uloženy ve veřejném kontejneru Blob.


**Příprava úložiště a kopírovat soubory pomocí Azure PowerShell:**
> [!IMPORTANT]
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který bude vytvořen skriptem.
> Zapište **název skupiny prostředků**, **název účtu úložiště**, a **klíč účtu úložiště** výstupem skriptem. Je nutné v další části.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
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

Pokud potřebujete pomoc s skript prostředí PowerShell, přečtěte si [pomocí Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md). Pokud chcete místo toho použijte rozhraní příkazového řádku Azure, najdete v článku [příloha](#appendix) části pro skript příkazového řádku Azure CLI.

**K prozkoumání účet úložiště a obsah**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **skupiny prostředků** v levém podokně.
3. Dvakrát klikněte na název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr.
4. Na **prostředky** dlaždice, musí mít jeden prostředek uvedené Pokud sdílíte s jinými projekty skupiny prostředků. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Klikněte na název účtu úložiště.
5. Klikněte **objekty BLOB** dlaždice.
6. Klikněte **adfgetstarted** kontejneru. Zobrazí dvě složky: **inputdata** a **skriptu**.
7. Otevřete složku a zkontrolujte soubory ve složkách. Inputdata soubor input.log s vstupní data a složky skript obsahuje soubor skriptu HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Vytvořte objekt pro vytváření dat pomocí šablony Resource Manageru
Účet úložiště, vstupní data a skript HiveQL připravený jste připraveni k vytvoření služby Azure data factory. Existuje několik metod pro vytvoření služby data factory. V tomto kurzu vytvoříte objekt pro vytváření dat nasazením šablonu Azure Resource Manager pomocí portálu Azure. Můžete také nasadit šablony Resource Manageru pomocí [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) a [prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Ostatními metodami vytvoření objektu pro vytváření dat najdete v části [kurz: sestavení prvního objektu pro vytváření dat](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Klikněte na následující obrázek pro přihlášení do Azure a otevřete šablonu Resource Manageru na webu Azure Portal. Šablona se nachází v https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Najdete v článku [entit služby Data Factory v šabloně](#data-factory-entities-in-the-template) části Podrobné informace o entit definované v šabloně. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Vyberte **použít existující** možnost **skupiny prostředků** nastavení a vyberte název skupiny prostředků, kterou jste vytvořili v předchozím kroku (pomocí skriptu prostředí PowerShell).
3. Zadejte název objektu pro vytváření dat (**název objektu pro vytváření dat**). Tento název musí být globálně jedinečný.
4. Zadejte **název účtu úložiště** a **klíč účtu úložiště** jste si poznamenali v předchozím kroku.
5. Vyberte **souhlasím s podmínkami a ujednáními** stanovené výše po přečtení **podmínky a ujednání**.
6. Vyberte **připnout na řídicí panel** možnost.
6. Klikněte na tlačítko **nákupu nebo vytvořit**. Zobrazí na dlaždici na řídicím panelu názvem **nasazení šablony**. Počkejte **skupiny prostředků** otevře se okno skupiny prostředků. Můžete také kliknutím na dlaždici s názvem jako název skupiny prostředků a otevřete okno skupiny prostředků.
6. Kliknutím na dlaždici otevřít skupinu prostředků, je-li okně skupiny prostředků není otevřený. Nyní se zobrazí jeden další prostředek objektu pro vytváření dat uvedené kromě prostředků účtu úložiště.
7. Klikněte na název objektu pro vytváření dat (hodnota, kterou jste zadali pro **název objektu pro vytváření dat** parametr).
8. V okně Data Factory klikněte **Diagram** dlaždici. Diagram znázorňuje jednu aktivitu s vstupní datové sady a výstupní datové:

    ![Azure Data Factory HDInsight na vyžádání Hive aktivity kanálu diagram](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Názvy jsou definovány v šabloně Resource Manager.
9. Klikněte dvakrát na **AzureBlobOutput**.
10. Na **poslední aktualizovat řezy**, se zobrazí jeden řez. Pokud je stav **v průběhu**, počkejte, dokud se změní na **připraven**. Obvykle trvá přibližně **20 minut** k vytvoření clusteru HDInsight.

### <a name="check-the-data-factory-output"></a>Zkontrolujte výstup objektu pro vytváření dat

1. Stejný postup můžete použijte v poslední relaci ke kontrole kontejnery v kontejneru adfgetstarted. Existují dva nové kontejnery kromě **adfgetsarted**:

   * Kontejner s názvem, který odpovídá vzorci: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Tento kontejner je výchozím kontejnerem pro HDInsight cluster.
   * adfjobs: Tento kontejner je kontejner pro protokoly úlohy ADF.

     Výstup objektu pro vytváření dat je uložen v **afgetstarted** nakonfigurované v šabloně Resource Manager.
2. Klikněte na tlačítko **adfgetstarted**.
3. Klikněte dvakrát na **partitioneddata**. Zobrazí **rok = 2014** složky protože ze všech webových protokolů v roce 2014.

    ![Azure Data Factory HDInsight na vyžádání Hive aktivity kanálu výstup](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Pokud přejdete k podrobnostem v seznamu, zobrazí se tří složek pro leden, února a března. A je do protokolu pro každý měsíc.

    ![Azure Data Factory HDInsight na vyžádání Hive aktivity kanálu výstup](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entity služby Data Factory v šabloně
Zde je, jak vypadá nejvyšší úrovně šablony Resource Manageru pro vytváření dat:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definování datové továrny
Datovou továrnu definujete v šabloně Resource Manageru, jak je znázorněno v následující ukázce:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
DataFactoryName je název objektu pro vytváření dat, které určíte při nasazování šablony. Objekt pro vytváření dat se aktuálně podporuje jenom v oblasti Východ USA, Severní Evropa a západní USA.

### <a name="defining-entities-within-the-data-factory"></a>Definování entity v rámci objektu pro vytváření dat
V šabloně JSON jsou definovány následující entity služby Data Factory:

* [Propojená služba Azure Storage](#azure-storage-linked-service)
* [Propojená služba HDInsightu na vyžádání](#hdinsight-on-demand-linked-service)
* [Vstupní datová sada Azure Blob](#azure-blob-input-dataset)
* [Výstupní datová sada Azure Blob](#azure-blob-output-dataset)
* [Data Pipeline s aktivitou kopírování](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Propojená služba AzureStorage propojí váš účet služby Azure Storage s datovou továrnou. V tomto kurzu se používá stejný účet úložiště jako výchozí účet úložiště HDInsight, úložiště vstupní data a výstupní datové úložiště. Proto můžete definovat jen jeden Azure Storage, propojené služby. V definici propojené služby je třeba zadat název a klíč účtu úložiště Azure. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](../data-factory/connector-azure-blob-storage.md).

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Vlastnost **connectionString** používá parametry storageAccountName a storageAccountKey. Zadejte hodnoty pro tyto parametry při nasazení šablony.  

#### <a name="hdinsight-on-demand-linked-service"></a>Propojená služba HDInsightu na vyžádání
V definici služby propojené HDInsight na vyžádání zadejte hodnoty pro parametry konfigurace, které jsou používány služba Data Factory k vytvoření clusteru HDInsight Hadoop za běhu. Podrobnosti o vlastnostech JSON používaných k definici propojené služby HDInsightu najdete v článku [Propojené služby Compute](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Je třeba počítat s následujícím:

* Vytvoří objekt pro vytváření dat **systémem Linux** HDInsight cluster.
* Vytvoření clusteru HDInsight Hadoop ve stejné oblasti jako účet úložiště.
* Upozornění *timeToLive* nastavení. Objekt pro vytváření dat cluster odstraní automaticky po clusteru se v nečinnosti, po dobu 30 minut.
* Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní.

Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [!IMPORTANT]
> Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů používají následující formát: „adf**název_vašeho_objektu_pro_vytváření_dat**-**název_propojené_služby**-razítko_data_a_času“. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).

#### <a name="azure-blob-input-dataset"></a>Vstupní datová sada Azure Blob
V definici vstupní datové sady zadejte názvy kontejneru objektů blob, složku a soubor, který obsahuje vstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](../data-factory/connector-azure-blob-storage.md).

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Všimněte si následujících konkrétní nastavení v definici JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob
V definici výstupní datovou sadu zadejte názvy kontejneru objektů blob a složky, která obsahuje výstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](../data-factory/connector-azure-blob-storage.md).  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath Určuje cestu ke složce, která obsahuje výstupní data:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[Datovou sadu dostupnosti](../data-factory/concepts-datasets-linked-services.md) nastavení vypadá takto:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

V Azure Data Factory výstupní datovou sadu dostupnosti jednotky kanálu. V tomto příkladu je řez vytváří jednou měsíčně poslední den v měsíci (EndOfInterval). 

#### <a name="data-pipeline"></a>Data Pipeline
Můžete definovat kanál, který transformuje data pomocí skriptu Hive v clusteru Azure HDInsight na vyžádání. Popisy elementů JSON sloužících k definování kanálu v tomto příkladu najdete v oddílu [Kód JSON kanálu](../data-factory/concepts-pipelines-activities.md).

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

Kanál obsahuje jednu aktivitu, aktivita HDInsightHive. Jako počáteční a koncová data jsou v leden 2016, data pro pouze jeden měsíc () zpracování řezu se. Obě *spustit* a *end* aktivity mají na datum v minulosti, takže objektu pro vytváření dat zpracovává data pro daný měsíc okamžitě. Pokud element end budoucí datum, data factory vytvoří jiné řez, když nastane čas. Další informace najdete v tématu [Data Factory plánování a provádění](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Vyčistěte kurz

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Odstranění kontejnerů objektů blob, vytvořit cluster HDInsight na vyžádání
S na vyžádání propojené služby HDInsight HDInsight cluster vytvoří pokaždé, když řez je potřeba zpracovat, pokud neexistuje aktivní cluster (timeToLive); a cluster bude odstraněn, pokud se provádí zpracování. Pro každý cluster Azure Data Factory vytvoří kontejner objektů blob v Azure blob storage používá jako výchozí účet stroage pro cluster. I když dojde k odstranění clusteru HDInsight, nebudou odstraněny výchozího kontejneru blob storage a přidruženého účtu úložiště. Toto chování je záměrné. Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Odstranit **adfjobs** a **adfyourdatafactoryname-linkedservicename razítko_data_a_času** složky. Kontejner adfjobs obsahuje protokoly úlohy Azure Data Factory.

### <a name="delete-the-resource-group"></a>Odstraňte skupinu prostředků
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) se používá k nasazení, správě a monitorování vašeho řešení jako se skupinou.  Odstranění skupiny prostředků se odstraní všechny součásti ve skupině.  

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **skupiny prostředků** v levém podokně.
3. Klikněte na název skupiny prostředků, kterou jste vytvořili ve vašem skriptu prostředí PowerShell. Pokud máte příliš mnoho skupin prostředků, které jsou uvedeny, použijte filtr. Skupina prostředků se otevře v novém okně.
4. Na **prostředky** dlaždice, musí mít výchozí účet úložiště a objektu pro vytváření dat uvedené Pokud sdílíte s jinými projekty skupiny prostředků.
5. Klikněte na tlačítko **odstranit** nahoře v okně. Díky tomu odstraní účet úložiště a data uložená v účtu úložiště.
6. Zadejte název skupiny prostředků Potvrdit odstranění, a pak klikněte na **odstranit**.

V případě, že nechcete odstranit účet úložiště, když odstraníte skupinu prostředků, zvažte následující architektura oddělením firemních dat z výchozí účet úložiště. V takovém případě je třeba jedna skupina prostředků pro účet úložiště s daty obchodních a jiné skupině prostředků pro výchozí účet úložiště pro HDInsight propojené služby a služby data factory. Pokud odstraníte druhé skupině prostředků, neovlivní účet úložiště obchodní data. Postupujte následovně:

* Přidejte následující ke skupině prostředků nejvyšší úrovně společně s Microsoft.DataFactory/datafactories prostředků ve vaší šabloně Resource Manager. Vytvoří účet úložiště:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Přidejte nový bod propojené služby do nového účtu úložiště:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Nakonfigurujte další dependsOn a additionalLinkedServiceNames ondemand propojená služba HDInsight:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili, jak používat Azure Data Factory k vytvoření clusteru HDInsight na vyžádání ke zpracování úloh Hive. Další informace:

* [Kurz Hadoopu: začněte používat systémem Linux Hadoop v HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vytvořit clustery se systémem Linux Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Dokumentace prostředí HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Dokumentace k objektu pro vytváření dat](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Příloha

### <a name="azure-cli-script"></a>Azure CLI skriptu
Místo použití prostředí Azure PowerShell udělat tento kurz můžete použít rozhraní příkazového řádku Azure. Chcete-li používat rozhraní příkazového řádku Azure, nejprve nainstalujte rozhraní příkazového řádku Azure podle následujících pokynů:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Příprava úložiště a zkopírujte soubory pomocí rozhraní příkazového řádku Azure

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Název kontejneru je *adfgetstarted*. Protože se jedná, uchovávejte ho. V opačném případě je potřeba aktualizovat šablony Resource Manageru. Pokud potřebujete pomoc s Tento skript rozhraní příkazového řádku, přečtěte si [použití Azure CLI s Azure Storage](../storage/common/storage-azure-cli.md).
