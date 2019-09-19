---
title: 'Kurz: Clustery Apache Hadoop na vyžádání v Azure HDInsight – Data Factory'
description: Kurz – Naučte se vytvářet clustery Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 04/18/2019
ms.openlocfilehash: 0b3062e14873ec971163c125fccd6852d8662663
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098724"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Kurz: Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto kurzu se naučíte, jak vytvořit cluster [Apache Hadoop](https://hadoop.apache.org/) na vyžádání v Azure HDInsight pomocí Azure Data Factory. Pak pomocí datových kanálů v Azure Data Factory spouštíte úlohy podregistru a odstraníte cluster. Po skončení tohoto kurzu se naučíte zprovoznění úlohy s velkým objemem dat, kde se spouští vytvoření clusteru, spuštění úloh a odstranění clusteru podle plánu.

Tento kurz se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Vysvětlení aktivity Azure Data Factory
> * Vytvoření datové továrny pomocí Azure Portal
> * Vytvoření propojených služeb
> * Vytvoření kanálu
> * Aktivace kanálu
> * Monitorování kanálu
> * Ověření výstupu

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředí PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/overview) installed.

* Objekt Azure Active Directory služby. Po vytvoření instančního objektu se ujistěte, že jste načetli **ID aplikace** a **ověřovací klíč** podle pokynů v odkazovaném článku. Tyto hodnoty budete potřebovat později v tomto kurzu. Také se ujistěte, že instanční objekt je členem role *přispěvatele* předplatného nebo skupiny prostředků, ve které se cluster vytváří. Pokyny k načtení požadovaných hodnot a přiřazení správných rolí najdete v tématu [vytvoření Azure Active Directory instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Vytvoření předběžných objektů Azure

V této části vytvoříte různé objekty, které se budou používat pro cluster HDInsight, který vytvoříte na vyžádání. Vytvořený účet úložiště bude obsahovat vzorový skript [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) (`partitionweblogs.hql`), který použijete k simulaci ukázkové [Apache Hive](https://hive.apache.org/) úlohy, která běží na clusteru.

V této části se používá skript Azure PowerShell k vytvoření účtu úložiště a zkopírování požadovaných souborů v rámci účtu úložiště. Ukázkový skript Azure PowerShell v této části provádí následující úlohy:

1. Přihlaste se k Azure.
2. Vytvoří skupinu prostředků Azure.
3. Vytvoří účet služby Azure Storage.
4. Vytvoří v účtu úložiště kontejner objektů BLOB.
5. Zkopíruje vzorový skript HiveQL (**partitionweblogs. HQL**) do kontejneru objektů BLOB. Skript je k dispozici [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)na adrese. Ukázkový skript je již k dispozici v jiném veřejném kontejneru objektů BLOB. Níže uvedený skript PowerShellu vytvoří kopii těchto souborů na účet Azure Storage, který vytvoří.

> [!WARNING]  
> Druh `BlobStorage` účtu úložiště se nedá použít pro clustery HDInsight.

**Pokud chcete vytvořit účet úložiště a zkopírovat soubory pomocí Azure PowerShell:**

> [!IMPORTANT]  
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který bude skript vytvořit.
> Zapište **název skupiny prostředků**, **název účtu úložiště**a **klíč účtu úložiště** , který je vydaný skriptem. Budete je potřebovat v další části.

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
2. V levém podokně vyberte **skupiny prostředků** .
3. Vyberte název skupiny prostředků, který jste vytvořili ve svém skriptu PowerShellu. Pokud máte v seznamu příliš mnoho skupin prostředků, použijte filtr.
4. Na dlaždici **prostředky** uvidíte jeden prostředek uvedený v seznamu, pokud nesdílíte skupinu prostředků s jinými projekty. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Vyberte název účtu úložiště.
5. Vyberte dlaždice **objektů BLOB** .
6. Vyberte kontejner **adfgetstarted** . Zobrazí se složka s názvem **hivescripts**.
7. Otevřete složku a ujistěte se, že obsahuje ukázkový soubor skriptu **partitionweblogs. HQL**.

## <a name="understand-the-azure-data-factory-activity"></a>Pochopení aktivity Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestruje a automatizuje přesun a transformaci dat. Azure Data Factory může vytvořit cluster HDInsight Hadoop za běhu ke zpracování vstupního datového řezu a odstranit cluster po dokončení zpracování. 

V Azure Data Factory Datová továrna může mít jeden nebo více datových kanálů. Datový kanál obsahuje jednu nebo více aktivit. Existují dva typy aktivit:

- [Aktivity přesunu dat](../data-factory/copy-activity-overview.md) – k přesunu dat ze zdrojového úložiště dat do cílového úložiště dat slouží aktivity přesunu dat.
- [Aktivity transformace dat](../data-factory/transform-data.md). Aktivity transformace dat slouží k transformaci nebo zpracování dat. Aktivita podregistru HDInsight je jednou z aktivit transformace, které podporuje Data Factory. V tomto kurzu použijete aktivitu transformace podregistru.

V tomto článku nakonfigurujete aktivitu podregistru tak, aby se vytvořil cluster Hadoop služby HDInsight na vyžádání. V případě, že se aktivita spouští ke zpracování dat, je zde Tato akce:

1. Pro zpracování řezu se automaticky vytvoří cluster Hadoop An HDInsight za běhu. 

2. Vstupní data jsou zpracována spuštěním skriptu HiveQL v clusteru. V tomto kurzu provede skript HiveQL přidružený k aktivitě podregistru tyto akce:

    - Použije existující tabulku (*hivesampletable*) k vytvoření další tabulky **HiveSampleOut**.
    - Naplní tabulku **HiveSampleOut** o pouze konkrétní sloupce z původních *hivesampletable*.
    
3. Po dokončení zpracování se cluster HDInsight Hadoop odstraní a cluster se po nakonfigurované době nečinný (nastavení timeToLive). Pokud je k dispozici další datový řez pro zpracování v této timeToLive době nečinnosti, použije se stejný cluster ke zpracování řezu.  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo přejděte na **+ vytvořit** > **Data Factory**pro**analýzu** > prostředků.

    ![Azure Data Factory na portálu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory na portálu")

3. Zadejte nebo vyberte následující hodnoty pro novou dlaždici **objektu pro vytváření dat** :

    |Vlastnost  |Value  |
    |---------|---------|
    |Name | Zadejte název objektu pro vytváření dat. Tento název musí být globálně jedinečný.|
    |Subscription | Vyberte své předplatné Azure. |
    |Resource group | Vyberte **použít existující** a pak vyberte skupinu prostředků, kterou jste vytvořili pomocí skriptu PowerShellu. |
    |Version | Ponechte v **v2**. |
    |Location | Umístění je automaticky nastaveno na umístění, které jste zadali během vytváření skupiny prostředků. Pro tento kurz je umístění nastavené na **východní USA**. |

    ![Vytvoření Azure Data Factory pomocí Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Vytvoření Azure Data Factory pomocí Azure Portal")

4. Vyberte **Vytvořit**. Vytváření datové továrny může trvat 2 až 4 minuty.

5. Po vytvoření objektu pro vytváření dat se zobrazí oznámení o **úspěšném nasazení** s tlačítkem **Přejít na prostředek** .  Vyberte **Přejít k prostředku** a otevřete Data Factory výchozí zobrazení.

6. Vyberte **vytvořit & monitorování** a spusťte tak Azure Data Factory portál pro vytváření a monitorování.

    ![Přehled Azure Data Factoryového portálu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Přehled Azure Data Factory")

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte ve vaší datové továrně dvě propojené služby.

- **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript pro podregistr, který je spuštěn v clusteru.
- **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří cluster HDInsight a spustí skript pro podregistr. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. V levém podokně stránky **Začínáme** vyberte ikonu **Autor** .

    ![Vytvoření propojené služby Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Vytvoření propojené služby Azure Data Factory")

2. V levém dolním rohu okna vyberte **připojení** a pak vyberte **+ Nový**.

    ![Vytvoření připojení v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Vytvoření připojení v Azure Data Factory")

3. V dialogovém okně **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.

    ![Vytvořit propojenou službu Azure Storage pro Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Vytvořit propojenou službu Azure Storage pro Data Factory")

4. Zadejte následující hodnoty pro propojenou službu úložiště:

    |Vlastnost |Value |
    |---|---|
    |Name |Zadejte `HDIStorageLinkedService`.|
    |Předplatné Azure |V rozevíracím seznamu vyberte své předplatné.|
    |Název účtu úložiště |Vyberte účet Azure Storage, který jste vytvořili jako součást skriptu prostředí PowerShell.|

    Pak vyberte **Dokončit**.

    ![Zadejte název pro propojenou službu Azure Storage] . (./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Zadejte název pro propojenou službu Azure Storage") .

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu.

2. V okně **Nová propojená služba** vyberte kartu **COMPUTE** .

3. Vyberte **Azure HDInsight**a pak vyberte **pokračovat**.

    ![Vytvořit propojenou službu HDInsight pro Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Vytvořit propojenou službu HDInsight pro Azure Data Factory")

4. V okně **Nová propojená služba** zadejte následující hodnoty a nechte zbývající jako výchozí:

    | Vlastnost | Value |
    | --- | --- |
    | Name | Zadejte `HDInsightLinkedService`.|
    | type | Vyberte **HDInsight na vyžádání**. |
    | Propojená služba Azure Storage | Vyberte `HDIStorageLinkedService`. |
    | Typ clusteru | Vyberte **Hadoop** |
    | Hodnota TTL (Time to Live) | Zadejte dobu, po kterou má být cluster HDInsight k dispozici, než se automaticky odstraní.|
    | ID instančního objektu | Zadejte ID aplikace Azure Active Directory instančního objektu, který jste vytvořili jako součást požadavků. |
    | Klíč objektu služby | Zadejte ověřovací klíč pro objekt Azure Active Directory služby. |
    | Předpona názvu clusteru | Zadejte hodnotu, která bude obsahovat předponu pro všechny typy clusterů, které jsou vytvořeny datovou továrnou. |
    |Subscription |V rozevíracím seznamu vyberte své předplatné.|
    | Vybrat skupinu prostředků | Vyberte skupinu prostředků, kterou jste vytvořili jako součást skriptu prostředí PowerShell, který jste použili dříve.|
    |Vybrat oblast | V rozevíracím seznamu vyberte oblast.|
    | Typ operačního systému/uživatelské jméno SSH clusteru | Zadejte uživatelské jméno SSH, obvykle `sshuser`. |
    | Typ operačního systému/heslo SSH clusteru | Zadejte heslo pro uživatele SSH. |
    | Typ operačního systému/uživatelské jméno clusteru | Zadejte uživatelské jméno clusteru, obvykle `admin`. |
    | Typ operačního systému/heslo uživatele clusteru | Zadejte heslo pro uživatele clusteru. |

    Pak vyberte **Dokončit**.

    ![Zadejte hodnoty pro propojenou službu HDInsight] . (./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Zadejte hodnoty pro propojenou službu HDInsight") .

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte tlačítko **+** (plus) a pak vyberte **Kanál**.

    ![Vytvoření kanálu v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Vytvoření kanálu v Azure Data Factory")

2. Na panelu nástrojů **aktivity** rozbalte **HDInsight**a přetáhněte aktivitu **podregistru** na plochu návrháře kanálu. Na kartě **Obecné** zadejte název aktivity.

    ![Přidání aktivit do kanálu Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Přidání aktivit do kanálu Data Factory")

3. Ujistěte se, že máte vybranou aktivitu podregistr, vyberte kartu **cluster HDI** a v rozevíracím seznamu **propojená služba HDInsight** vyberte propojenou službu, kterou jste vytvořili dříve, **HDinightLinkedService**, pro HDInsight.

    ![Zadejte podrobnosti o clusteru HDInsight pro kanál] . (./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Zadejte podrobnosti o clusteru HDInsight pro kanál") .

4. Vyberte kartu **skript** a proveďte následující kroky:

    1. V poli pro **propojenou službu skriptu**vyberte v rozevíracím seznamu možnost **HDIStorageLinkedService** . Tato hodnota je propojená služba úložiště, kterou jste vytvořili dříve.

    1. V poli **cesta k souboru**vyberte **Procházet úložiště** a přejděte do umístění, kde je k dispozici ukázkový skript podregistru. Pokud jste dříve spustili skript prostředí PowerShell, toto umístění by mělo `adfgetstarted/hivescripts/partitionweblogs.hql`být.

        ![Zadání podrobností o skriptu podregistru pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Zadání podrobností o skriptu podregistru pro kanál")

    1. V části **Upřesnit** > **parametry**vyberte možnost **automaticky vyplnit ze skriptu**. Tato možnost vyhledá všechny parametry v rámci skriptu podregistru, které vyžadují hodnoty za běhu. Skript, který použijete (**partitionweblogs. HQL**), má **výstupní** parametr. Zadejte **hodnotu** ve formátu `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` tak, aby odkazovala na existující složku v Azure Storage. V této cestě se rozlišují velká a malá písmena. Toto je cesta, kde bude uložen výstup skriptu. `wasbs` Schéma je nezbytné, protože účty úložiště mají nyní povolený zabezpečený přenos, který je ve výchozím nastavení povolen.
    
        ![Zadejte parametry pro skript podregistr] . (./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Zadejte parametry pro skript podregistr") .

1. Pro ověření kanálu vyberte **ověřit** . Výběrem tlačítka **>>** (šipka doprava) zavřete okno ověřování.

    ![Ověření kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Ověření kanálu Azure Data Factory")

1. Nakonec vyberte **publikovat vše** pro publikování artefaktů do Azure Data Factory.

    ![Publikování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publikování kanálu Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Aktivace kanálu

1. Z panelu nástrojů na návrhové ploše vyberte **Přidat** > **aktivační událost nyní**.

    ![Aktivace kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Aktivace kanálu Azure Data Factory")

2. Na postranním panelu klikněte na **Dokončit** .

## <a name="monitor-a-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. Všimněte si stavu běhu ve sloupci **stav** .

    ![Monitorování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorování kanálu Azure Data Factory")

1. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

1. Můžete také vybrat ikonu **Zobrazit spuštění aktivit** a zobrazit tak spuštění aktivit spojené s tímto kanálem. Na následujícím snímku obrazovky vidíte jenom jednu spuštěnou aktivitu, protože kanál, který jste vytvořili, je jenom jedna aktivita. Chcete-li přejít zpět k předchozímu zobrazení, vyberte **kanály** v horní části stránky.

    ![Monitorování aktivity Azure Data Factoryového kanálu](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorování aktivity Azure Data Factoryového kanálu")

## <a name="verify-the-output"></a>Ověření výstupu

1. Výstup ověříte tak, že v Azure Portal přejdete do účtu úložiště, který jste použili v tomto kurzu. Měli byste vidět následující složky nebo kontejnery:

    - Zobrazí se **adfgerstarted/OutputFolder** obsahující výstup skriptu podregistru, který byl spuštěn jako součást kanálu.

    - Zobrazí se kontejner **> časového\<razítka >-\<adfhdidatafactory-Link-Service-Name** . Tento kontejner je výchozím umístěním úložiště clusteru HDInsight, který byl vytvořen jako součást spuštění kanálu.

    - Zobrazí se kontejner **adfjobs** , který obsahuje protokoly úloh Azure Data Factory.  

        ![Ověření výstupu kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Ověření výstupu kanálu Azure Data Factory")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při vytváření clusteru HDInsight na vyžádání nemusíte cluster HDInsight explicitně odstraňovat. Cluster se odstraní na základě konfigurace, kterou jste zadali při vytváření kanálu. I když se cluster odstraní, pořád existují účty úložiště přidružené ke clusteru. Toto chování je záměrné, takže můžete uchovávat data beze změny. Pokud ale data nechcete uchovávat, můžete odstranit účet úložiště, který jste vytvořili.

Případně můžete odstranit celou skupinu prostředků, kterou jste pro tento kurz vytvořili. Tím se odstraní účet úložiště a Azure Data Factory, který jste vytvořili.

### <a name="delete-the-resource-group"></a>Odstranit skupinu prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **skupiny prostředků** .
1. Vyberte název skupiny prostředků, který jste vytvořili ve svém skriptu PowerShellu. Pokud máte v seznamu příliš mnoho skupin prostředků, použijte filtr. Otevře se skupina prostředků.
1. Na dlaždici **prostředky** máte k dispozici výchozí účet úložiště a datovou továrnu, pokud nesdílíte skupinu prostředků s jinými projekty.
1. Vyberte **Odstranit skupinu prostředků**. Tím se odstraní účet úložiště a data uložená v účtu úložiště.

    ![Azure Portal odstranit skupinu prostředků](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Odstranit skupinu prostředků")

1. Zadáním názvu skupiny prostředků potvrďte odstranění a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak pomocí Azure Data Factory vytvořit cluster HDInsight na vyžádání a spouštět úlohy [Apache Hive](https://hive.apache.org/) . V dalším článku se dozvíte, jak vytvářet clustery HDInsight s vlastní konfigurací.

> [!div class="nextstepaction"]
>[Vytváření clusterů Azure HDInsight s vlastní konfigurací](hdinsight-hadoop-provision-linux-clusters.md)
