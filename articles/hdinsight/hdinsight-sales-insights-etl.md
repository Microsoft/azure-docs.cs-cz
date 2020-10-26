---
title: 'Kurz: vytvoření kompletního kanálu ETL pro odvodit Sales Insights v Azure HDInsight'
description: Naučte se, jak pomocí Azure HDInsight vytvořit kanály ETL k odvozování přehledů z prodejních dat pomocí clusterů Spark na vyžádání a Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 17e4468785ff113a85265a17a8314a0174d5d360
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535378"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Kurz: vytvoření uceleného datového kanálu pro odvoditelné prodejní přehledy ve službě Azure HDInsight

V tomto kurzu vytvoříte kompletní datový kanál, který provede operace extrakce, transformace a načítání (ETL). Kanál bude používat clustery [Apache Spark](./spark/apache-spark-overview.md) a Apache Hive běžící na Azure HDInsight pro dotazování a manipulaci s daty. Budete také používat technologie, jako je Azure Data Lake Storage Gen2 pro úložiště dat, a Power BI pro vizualizaci.

Tento datový kanál kombinuje data z různých úložišť, odebírá všechna nepotřebná data, přidává nová data a načítá je zpátky do vašeho úložiště pro vizualizaci obchodních přehledů. Další informace o kanálech ETL najdete v tématu věnovaném [extrakci, transformaci a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

* Azure CLI – minimálně verze 2.2.0 Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* JQ, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Člen [předdefinovaného vlastníka role Azure](../role-based-access-control/built-in-roles.md)

* Pokud k aktivaci kanálu Data Factory používáte PowerShell, budete potřebovat [modul AZ](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) k vizualizaci obchodních přehledů na konci tohoto kurzu.

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonování úložiště pomocí skriptů a dat

1. Přihlaste se k předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. V opačném případě zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ujistěte se, že jste členem role Azure [Owner](../role-based-access-control/built-in-roles.md). Nahraďte `user@contoso.com` účtem a potom zadejte příkaz:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Pokud se nevrátí žádný záznam, nejste členem a nebudete moct tento kurz dokončit.

1. Stáhněte si data a skripty pro tento kurz z [úložiště ETL Sales Insights HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Zadejte následující příkaz:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Ujistěte `salesdata scripts templates` se, že byly vytvořeny. Ověřte pomocí následujícího příkazu:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Nasazení prostředků Azure potřebných pro kanál

1. Přidejte oprávnění EXECUTE pro všechny skripty zadáním:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Nastavte proměnnou pro skupinu prostředků. Nahraďte `RESOURCE_GROUP_NAME` názvem existující nebo novou skupinou prostředků a zadejte příkaz:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Spusťte skript. Nahraďte `LOCATION` požadovanou hodnotou a pak zadejte příkaz:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Pokud si nejste jistí, kterou oblast určíte, můžete pro své předplatné načíst seznam podporovaných oblastí pomocí příkazu [AZ Account list-Locations](/cli/azure/account#az-account-list-locations) .

    Příkaz nasadí následující prostředky:

    * Účet úložiště objektů BLOB v Azure. Tento účet bude obsahovat data o prodeji společnosti.
    * Účet Azure Data Lake Storage Gen2. Tento účet bude sloužit jako účet úložiště pro oba clustery HDInsight. Přečtěte si další informace o HDInsight a Data Lake Storage Gen2 v tématu [integrace služby Azure HDInsight s Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Spravovaná identita přiřazená uživatelem Tento účet uděluje clusterům HDInsight přístup k účtu Data Lake Storage Gen2.
    * Cluster Apache Spark. Tento cluster se použije k vyčištění a transformaci nezpracovaných dat.
    * Cluster [interaktivních dotazů](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Tento cluster umožní dotazování na data prodeje a jejich vizualizaci pomocí Power BI.
    * Virtuální síť Azure, kterou podporuje pravidla skupiny zabezpečení sítě (NSG). Tato virtuální síť umožňuje clusterům komunikovat a zabezpečit jejich komunikaci.

Vytvoření clusteru může trvat přibližně 20 minut.

Výchozí heslo pro přístup SSH ke clusterům je `Thisisapassword1` . Pokud chcete změnit heslo, přejděte na `./templates/resourcesparameters_remainder.json` soubor a změňte heslo pro `sparksshPassword` `sparkClusterLoginPassword` parametry,, `llapClusterLoginPassword` a `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Ověření nasazení a shromažďování informací o prostředcích

1. Pokud chcete zjistit stav svého nasazení, na Azure Portal se podívejte do skupiny prostředků. V části **Nastavení** vyberte **nasazení** a pak nasazení. Tady vidíte prostředky, které se úspěšně nasadily, a prostředky, které pořád probíhají.

1. Chcete-li zobrazit názvy clusterů, zadejte následující příkaz:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Pokud chcete zobrazit účet služby Azure Storage a přístupový klíč, zadejte následující příkaz:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Chcete-li zobrazit účet Data Lake Storage Gen2 a přístupový klíč, zadejte následující příkaz:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Azure Data Factory je nástroj, který pomáhá automatizovat Azure Pipelines. Nejedná se o jediný způsob, jak tyto úlohy provést, ale je to skvělý způsob, jak procesy automatizovat. Další informace o Azure Data Factory najdete v dokumentaci k [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Tato datová továrna bude mít jeden kanál se dvěma aktivitami:

* První aktivita zkopíruje data z úložiště objektů BLOB v Azure do účtu úložiště Data Lake Storage Gen 2, který bude napodobovat přijímání dat.
* Druhá aktivita převede data v clusteru Spark. Skript transformuje data odebráním nežádoucích sloupců. Také přidá nový sloupec, který vypočítá výnosy vygenerované v jedné transakci.

Pokud chcete nastavit kanál Azure Data Factory, spusťte níže uvedený příkaz.  Stále byste měli být v `hdinsight-sales-insights-etl` adresáři.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Tento skript provádí následující akce:

1. Vytvoří instanční objekt s `Storage Blob Data Contributor` oprávněními pro data Lake Storage Gen2 účet úložiště.
1. Získá ověřovací token pro autorizaci požadavků POST do [Data Lake Storage Gen2 REST API systému souborů](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Vyplní skutečný název vašeho účtu úložiště Data Lake Storage Gen2 v `sparktransform.py` `query.hql` souborech a.
1. Získá klíče úložiště pro účty Data Lake Storage Gen2 a BLOB Storage.
1. Vytvoří jiné nasazení prostředků, aby se vytvořil kanál Azure Data Factory s přidruženými propojenými službami a aktivitami. Předá klíče úložiště jako parametry souboru šablony, aby propojené služby mohly správně přistupovat k účtům úložiště.

## <a name="run-the-data-pipeline"></a>Spuštění datového kanálu

### <a name="trigger-the-data-factory-activities"></a>Aktivovat aktivity Data Factory

První aktivita v kanálu Data Factory, kterou jste vytvořili, přesune data z úložiště objektů blob do Data Lake Storage Gen2. Druhá aktivita aplikuje transformace Spark na data a uloží transformační soubory. CSV do nového umístění. Dokončení celého kanálu může trvat několik minut.

Pokud chcete načíst název Data Factory, zadejte následující příkaz:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Pro aktivaci kanálu můžete:

* Aktivujte kanál Data Factory v PowerShellu. Nahraďte `RESOURCEGROUP` a `DataFactoryName` příslušnými hodnotami a spusťte následující příkazy:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Znovu spusťte `Get-AzDataFactoryV2PipelineRun` podle potřeby a sledujte průběh.

    Nebo

* Otevřete datovou továrnu a vyberte **vytvořit & monitorování** . Aktivujte `IngestAndTransform` kanál z portálu. Informace o aktivaci kanálů prostřednictvím portálu najdete v tématu [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Pokud chcete ověřit, že je kanál spuštěný, můžete provést jeden z následujících kroků:

* V datové továrně na portálu můžete přejít do části **monitorování** .
* V Průzkumník služby Azure Storage přejít na účet úložiště Data Lake Storage Gen 2. Přejděte do `files` systému souborů a potom přejděte do `transformed` složky a zkontrolujte její obsah, abyste viděli, jestli kanál uspěl.

Další způsoby, jak transformovat data pomocí služby HDInsight, najdete v [tomto článku o použití Jupyter notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Vytvoření tabulky v clusteru interaktivních dotazů pro zobrazení dat v Power BI

1. Zkopírujte `query.hql` soubor do clusteru LLAP pomocí spojovacího bodu služby. Zadejte příkaz:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Připomenutí: výchozí heslo je `Thisisapassword1` .

1. K přístupu ke clusteru LLAP použijte SSH. Zadejte příkaz:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Ke spuštění skriptu použijte následující příkaz:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Tento skript vytvoří spravovanou tabulku v clusteru interaktivních dotazů, ke kterému můžete přistupovat z Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Vytvoření řídicího panelu Power BI z prodejních dat

1. Otevřete Power BI Desktop.

1. V nabídce přejděte na **získat data**  >  **...**  >  **Azure**  >  **Interaktivní dotaz HDInsight**

1. Vyberte **Connect** (Připojit).

1. Z dialogového okna **HDInsight Interactive Query** :
    1. Do textového pole **Server** zadejte název clusteru LLAP ve formátu `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. Do textového pole **databáze** zadejte `default` .
    1. Vyberte **OK** .

1. V dialogovém okně **AzureHive** :
    1. Do textového pole **uživatelské jméno** zadejte `admin` .
    1. Do textového pole **heslo** zadejte `Thisisapassword1` .
    1. Vyberte **Connect** (Připojit).

1. Z **navigátoru** vyberte `sales` a/nebo `sales_raw` Zobrazte náhled dat. Po načtení dat můžete experimentovat s řídicím panelem, který chcete vytvořit. Pokud chcete začít s Power BI řídicími panely, přečtěte si následující odkazy:

* [Úvod do řídicích panelů pro návrháře Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Kurz: Začínáme se službou Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte všechny prostředky pomocí následujícího příkazu, abyste se za ně neúčtovali.

1. Pokud chcete odebrat skupinu prostředků, zadejte příkaz:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Chcete-li odebrat instanční objekt, zadejte příkazy:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md)