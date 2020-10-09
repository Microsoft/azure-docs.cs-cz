---
title: 'Kurz: clustery na vyžádání v Azure HDInsight s Data Factory'
description: Kurz – Naučte se vytvářet clustery Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 7353366af14ca785c5635e1bde8101c1d71cd47f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87079118"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Kurz: vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto kurzu se naučíte, jak vytvořit cluster [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) na vyžádání v Azure HDInsight pomocí Azure Data Factory. Pak pomocí datových kanálů v Azure Data Factory spouštíte úlohy podregistru a odstraníte cluster. Na konci tohoto kurzu se naučíte, jak `operationalize` Spustit úlohu s velkými objemy dat, kde se spouští vytvoření clusteru, spuštění úlohy a odstraňování clusteru.

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

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

* Prostředí PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/) installed.

* Objekt Azure Active Directory služby. Po vytvoření instančního objektu se ujistěte, že jste načetli **ID aplikace** a **ověřovací klíč** podle pokynů v odkazovaném článku. Tyto hodnoty budete potřebovat později v tomto kurzu. Také se ujistěte, že instanční objekt je členem role *přispěvatele* předplatného nebo skupiny prostředků, ve které se cluster vytváří. Pokyny k načtení požadovaných hodnot a přiřazení správných rolí najdete v tématu [vytvoření Azure Active Directory instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Vytvoření předběžných objektů Azure

V této části vytvoříte různé objekty, které se budou používat pro cluster HDInsight, který vytvoříte na vyžádání. Vytvořený účet úložiště bude obsahovat vzorový skript HiveQL, `partitionweblogs.hql` který použijete k simulaci ukázkové Apache Hive úlohy, která běží na clusteru.

V této části se používá skript Azure PowerShell k vytvoření účtu úložiště a zkopírování požadovaných souborů v rámci účtu úložiště. Ukázkový skript Azure PowerShell v této části provádí následující úlohy:

1. Přihlaste se k Azure.
2. Vytvoří skupinu prostředků Azure.
3. Vytvoří účet služby Azure Storage.
4. Vytvoří v účtu úložiště kontejner objektů BLOB.
5. Zkopíruje vzorový skript HiveQL (**partitionweblogs. HQL**) do kontejneru objektů BLOB. Skript je k dispozici na adrese [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . Ukázkový skript je již k dispozici v jiném veřejném kontejneru objektů BLOB. Níže uvedený skript PowerShellu vytvoří kopii těchto souborů na účet Azure Storage, který vytvoří.

### <a name="create-storage-account-and-copy-files"></a>Vytvoření účtu úložiště a kopírování souborů

> [!IMPORTANT]  
> Zadejte názvy pro skupinu prostředků Azure a účet úložiště Azure, který bude skript vytvořit.
> Zapište **název skupiny prostředků**, **název účtu úložiště**a **klíč účtu úložiště** , který je vydaný skriptem. V další části je budete potřebovat.

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

### <a name="verify-storage-account"></a>Ověřit účet úložiště

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vlevo přejděte na **všechny služby**  >  **Obecné**  >  **skupiny prostředků**.
1. Vyberte název skupiny prostředků, který jste vytvořili ve svém skriptu PowerShellu. Pokud máte v seznamu příliš mnoho skupin prostředků, použijte filtr.
1. V zobrazení **Přehled** uvidíte jeden prostředek uvedený, pokud nesdílíte skupinu prostředků s jinými projekty. Tento prostředek je účet úložiště s názvem, který jste zadali dříve. Vyberte název účtu úložiště.
1. Vyberte dlaždici **kontejnery** .
1. Vyberte kontejner **adfgetstarted** . Zobrazí se složka s názvem **`hivescripts`** .
1. Otevřete složku a ujistěte se, že obsahuje ukázkový soubor skriptu **partitionweblogs. HQL**.

## <a name="understand-the-azure-data-factory-activity"></a>Pochopení aktivity Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestruje a automatizuje přesun a transformaci dat. Azure Data Factory může vytvořit cluster HDInsight Hadoop za běhu ke zpracování vstupního datového řezu a odstranit cluster po dokončení zpracování.

V Azure Data Factory Datová továrna může mít jeden nebo více datových kanálů. Datový kanál obsahuje jednu nebo více aktivit. Existují dva typy aktivit:

* [Aktivity přesunu dat](../data-factory/copy-activity-overview.md). Aktivity přesunu dat slouží k přesunu dat ze zdrojového úložiště dat do cílového úložiště dat.
* [Aktivity transformace dat](../data-factory/transform-data.md). Aktivity transformace dat slouží k transformaci nebo zpracování dat. Aktivita podregistru HDInsight je jednou z aktivit transformace, které podporuje Data Factory. V tomto kurzu použijete aktivitu transformace podregistru.

V tomto článku nakonfigurujete aktivitu podregistru tak, aby se vytvořil cluster Hadoop služby HDInsight na vyžádání. V případě, že se aktivita spouští ke zpracování dat, je zde Tato akce:

1. Pro zpracování řezu se automaticky vytvoří cluster Hadoop An HDInsight za běhu.

2. Vstupní data jsou zpracována spuštěním skriptu HiveQL v clusteru. V tomto kurzu provede skript HiveQL přidružený k aktivitě podregistru tyto akce:

    * Použije existující tabulku (*hivesampletable*) k vytvoření další tabulky **HiveSampleOut**.
    * Naplní tabulku **HiveSampleOut** o pouze konkrétní sloupce z původních *hivesampletable*.

3. Po dokončení zpracování se cluster HDInsight Hadoop odstraní a cluster se po nakonfigurované době nečinný (nastavení timeToLive). Pokud je k dispozici další datový řez pro zpracování v této timeToLive době nečinnosti, použije se stejný cluster ke zpracování řezu.  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo přejděte na **`+ Create a resource`**  >  **Analytics**  >  **Data Factory**.

    ![Azure Data Factory na portálu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory na portálu")

3. Zadejte nebo vyberte následující hodnoty pro novou dlaždici **objektu pro vytváření dat** :

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Název | Zadejte název objektu pro vytváření dat. Tento název musí být globálně jedinečný.|
    |Verze | Ponechte v **v2**. |
    |Předplatné | Vyberte své předplatné Azure. |
    |Skupina prostředků | Vyberte skupinu prostředků, kterou jste vytvořili pomocí skriptu PowerShellu. |
    |Umístění | Umístění je automaticky nastaveno na umístění, které jste zadali během vytváření skupiny prostředků. Pro tento kurz je umístění nastavené na **východní USA**. |
    |Povolit GIT|Zrušte toto políčko.|

    ![Vytvoření Azure Data Factory pomocí Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Vytvoření Azure Data Factory pomocí Azure Portal")

4. Vyberte **Vytvořit**. Vytváření datové továrny může trvat 2 až 4 minuty.

5. Po vytvoření objektu pro vytváření dat se zobrazí oznámení o **úspěšném nasazení** s tlačítkem **Přejít na prostředek** .  Vyberte **Přejít k prostředku** a otevřete Data Factory výchozí zobrazení.

6. Vyberte **vytvořit & monitorování** a spusťte tak Azure Data Factory portál pro vytváření a monitorování.

    ![Přehled Azure Data Factoryového portálu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Přehled Azure Data Factory")

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte ve vaší datové továrně dvě propojené služby.

* **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript pro podregistr, který je spuštěn v clusteru.
* **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří cluster HDInsight a spustí skript pro podregistr. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. V levém podokně stránky **Začínáme** vyberte ikonu **Autor** .

    ![Vytvoření propojené služby Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Vytvoření propojené služby Azure Data Factory")

2. V levém dolním rohu okna vyberte **připojení** a pak vyberte **+ Nový**.

    ![Vytvoření připojení v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Vytvoření připojení v Azure Data Factory")

3. V dialogovém okně **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.

    ![Vytvořit propojenou službu Azure Storage pro Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Vytvořit propojenou službu Azure Storage pro Data Factory")

4. Zadejte následující hodnoty pro propojenou službu úložiště:

    |Vlastnost |Hodnota |
    |---|---|
    |Název |Zadejte `HDIStorageLinkedService`.|
    |Předplatné Azure |V rozevíracím seznamu vyberte své předplatné.|
    |Název účtu úložiště |Vyberte účet Azure Storage, který jste vytvořili jako součást skriptu prostředí PowerShell.|

    Vyberte možnost **Testovat připojení** a v případě úspěchu a pak vyberte **vytvořit**.

    ![Zadejte název pro propojenou službu Azure Storage.](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Zadejte název pro propojenou službu Azure Storage.")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu.

2. V okně **Nová propojená služba** vyberte kartu **COMPUTE** .

3. Vyberte **Azure HDInsight**a pak vyberte **pokračovat**.

    ![Vytvořit propojenou službu HDInsight pro Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Vytvořit propojenou službu HDInsight pro Azure Data Factory")

4. V okně **Nová propojená služba** zadejte následující hodnoty a nechte zbývající jako výchozí:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Název | Zadejte `HDInsightLinkedService`.|
    | Typ | Vyberte **HDInsight na vyžádání**. |
    | Propojená služba Azure Storage | Vyberte `HDIStorageLinkedService`. |
    | Typ clusteru | Vyberte **Hadoop** |
    | Hodnota TTL (Time to Live) | Zadejte dobu, po kterou má být cluster HDInsight k dispozici, než se automaticky odstraní.|
    | ID instančního objektu | Zadejte ID aplikace Azure Active Directory instančního objektu, který jste vytvořili jako součást požadavků. |
    | Klíč objektu služby | Zadejte ověřovací klíč pro objekt Azure Active Directory služby. |
    | Předpona názvu clusteru | Zadejte hodnotu, která bude obsahovat předponu pro všechny typy clusterů vytvořené datovou továrnou. |
    |Předplatné |V rozevíracím seznamu vyberte své předplatné.|
    | Výběr skupiny prostředků | Vyberte skupinu prostředků, kterou jste vytvořili jako součást skriptu prostředí PowerShell, který jste použili dříve.|
    | Typ operačního systému/uživatelské jméno SSH clusteru | Zadejte uživatelské jméno SSH, obvykle `sshuser` . |
    | Typ operačního systému/heslo SSH clusteru | Zadejte heslo pro uživatele SSH. |
    | Typ operačního systému/uživatelské jméno clusteru | Zadejte uživatelské jméno clusteru, obvykle `admin` . |
    | Typ operačního systému/heslo clusteru | Zadejte heslo pro uživatele clusteru. |

    Potom vyberte **Vytvořit**.

    ![Zadejte hodnoty pro propojenou službu HDInsight.](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Zadejte hodnoty pro propojenou službu HDInsight.")

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **+** tlačítko (plus) a pak vyberte **kanál**.

    ![Vytvoření kanálu v Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Vytvoření kanálu v Azure Data Factory")

1. Na panelu nástrojů **aktivity** rozbalte **HDInsight**a přetáhněte aktivitu **podregistru** na plochu návrháře kanálu. Na kartě **Obecné** zadejte název aktivity.

    ![Přidání aktivit do kanálu Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Přidání aktivit do kanálu Data Factory")

1. Ujistěte se, že máte vybranou aktivitu podregistr, a vyberte kartu **cluster HDI** . A v rozevíracím seznamu **propojená služba HDInsight** vyberte propojenou službu, kterou jste vytvořili dříve, **HDInsightLinkedService**pro HDInsight.

    ![Zadejte podrobnosti o clusteru HDInsight pro kanál.](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Zadejte podrobnosti o clusteru HDInsight pro kanál.")

1. Vyberte kartu **skript** a proveďte následující kroky:

    1. V poli pro **propojenou službu skriptu**vyberte v rozevíracím seznamu možnost **HDIStorageLinkedService** . Tato hodnota je propojená služba úložiště, kterou jste vytvořili dříve.

    1. V poli **cesta k souboru**vyberte **Procházet úložiště** a přejděte do umístění, kde je k dispozici ukázkový skript podregistru. Pokud jste dříve spustili skript prostředí PowerShell, toto umístění by mělo být `adfgetstarted/hivescripts/partitionweblogs.hql` .

        ![Zadání podrobností o skriptu podregistru pro kanál](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Zadání podrobností o skriptu podregistru pro kanál")

    1. V části **Upřesnit**  >  **parametry**vyberte **`Auto-fill from script`** . Tato možnost vyhledá všechny parametry v rámci skriptu podregistru, které vyžadují hodnoty za běhu.

    1. Do textového pole **hodnota** přidejte existující složku ve formátu `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . V této cestě se rozlišují velká a malá písmena. Tato cesta je místo, kde bude uložen výstup skriptu. `wasbs`Schéma je nezbytné, protože účty úložiště mají nyní povolený zabezpečený přenos, který je ve výchozím nastavení povolen.

        ![Zadejte parametry pro skript podregistr.](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Zadejte parametry pro skript podregistr.")

1. Pro ověření kanálu vyberte **ověřit** . Výběrem **>>** tlačítka (šipka doprava) zavřete okno ověřování.

    ![Ověření kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Ověření kanálu Azure Data Factory")

1. Nakonec vyberte **publikovat vše** pro publikování artefaktů do Azure Data Factory.

    ![Publikování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publikování kanálu Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Aktivace kanálu

1. Z panelu nástrojů na návrhové ploše vyberte **Přidat**  >  **aktivační událost nyní**.

    ![Aktivace kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Aktivace kanálu Azure Data Factory")

2. Na postranním panelu vyberte **OK** .

## <a name="monitor-a-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. Všimněte si stavu běhu ve sloupci **stav** .

    ![Monitorování kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorování kanálu Azure Data Factory")

1. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

1. Můžete také vybrat ikonu **Zobrazit spuštění aktivit** a zobrazit tak spuštění aktivit spojené s tímto kanálem. Na následujícím snímku obrazovky vidíte jenom jednu spuštěnou aktivitu, protože kanál, který jste vytvořili, je jenom jedna aktivita. Chcete-li přejít zpět k předchozímu zobrazení, vyberte **kanály** v horní části stránky.

    ![Monitorování aktivity Azure Data Factoryového kanálu](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorování aktivity Azure Data Factoryového kanálu")

## <a name="verify-the-output"></a>Ověření výstupu

1. Výstup ověříte tak, že v Azure Portal přejdete do účtu úložiště, který jste použili v tomto kurzu. Měli byste vidět následující složky nebo kontejnery:

    * Zobrazí se **adfgerstarted/OutputFolder** obsahující výstup skriptu podregistru, který byl spuštěn jako součást kanálu.

    * Zobrazí se **adfhdidatafactory \<linked-service-name> - \<timestamp> ** kontejner. Tento kontejner je výchozím umístěním úložiště clusteru HDInsight, který byl vytvořen jako součást spuštění kanálu.

    * Zobrazí se kontejner **adfjobs** , který obsahuje protokoly úloh Azure Data Factory.  

        ![Ověření výstupu kanálu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Ověření výstupu kanálu Azure Data Factory")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při vytváření clusteru HDInsight na vyžádání nemusíte cluster HDInsight explicitně odstraňovat. Cluster se odstraní na základě konfigurace, kterou jste zadali při vytváření kanálu. I po odstranění clusteru i nadále existují účty úložiště přidružené ke clusteru. Toto chování je záměrné, takže můžete uchovávat data beze změny. Pokud ale data nechcete zachovat, můžete odstranit účet úložiště, který jste vytvořili.

Nebo můžete odstranit celou skupinu prostředků, kterou jste pro tento kurz vytvořili. Tento proces odstraní účet úložiště a Azure Data Factory, který jste vytvořili.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **skupiny prostředků** .
1. Vyberte název skupiny prostředků, který jste vytvořili ve svém skriptu PowerShellu. Pokud máte v seznamu příliš mnoho skupin prostředků, použijte filtr. Otevře se skupina prostředků.
1. Na dlaždici **prostředky** máte k dispozici výchozí účet úložiště a datovou továrnu, pokud nesdílíte skupinu prostředků s jinými projekty.
1. Vyberte **Odstranit skupinu prostředků**. Tím se odstraní účet úložiště a data uložená v účtu úložiště.

    ![Azure Portal odstranit skupinu prostředků](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Odstranění skupiny prostředků")

1. Zadáním názvu skupiny prostředků potvrďte odstranění a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí Azure Data Factory vytvořit cluster HDInsight na vyžádání a spouštět úlohy Apache Hive. V dalším článku se dozvíte, jak vytvářet clustery HDInsight s vlastní konfigurací.

> [!div class="nextstepaction"]
> [Vytváření clusterů Azure HDInsight s vlastní konfigurací](hdinsight-hadoop-provision-linux-clusters.md)
