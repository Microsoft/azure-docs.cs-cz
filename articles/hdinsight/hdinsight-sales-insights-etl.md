---
title: 'Kurz: Vytvoření kanálu ETL od konce, který bude odvodit přehledy o prodeji v Azure HDInsightu'
description: Zjistěte, jak pomocí azure HDInsight vytvářet kanály ETL k odvození přehledů z dat o prodeji pomocí clusterů Spark na vyžádání a Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535226"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Kurz: Vytvoření kanálu dat od konce pro odvození přehledů o prodeji v Azure HDInsight

V tomto kurzu vytvoříte kanál dat od konce, který provádí operace extrakce, transformace a načtení (ETL). Kanál bude používat [clustery Apache Spark](./spark/apache-spark-overview.md) a Apache Hive spuštěné na Azure HDInsight pro dotazování a manipulaci s daty. Budete také používat technologie, jako je Azure Data Lake Storage Gen2 pro ukládání dat a Power BI pro vizualizaci.

Tento datový kanál kombinuje data z různých úložišť, odstraňuje všechna nechtěná data, připojí nová data a načte je zpět do úložiště, aby mohl vizualizovat obchodní přehledy. Další informace o kanálech ETL v [části Extrakce, transformace a zatížení (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Vypodávací příkazové konto Azure – alespoň verze 2.2.0. Viz [Instalace příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* jq, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Člen [předdefinované role Azure – vlastník](../role-based-access-control/built-in-roles.md).

* Pokud pomocí prostředí PowerShell aktivujete kanál Data Factory, budete potřebovat [modul Az](https://docs.microsoft.com/powershell/azure/overview).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) pro vizualizaci obchodních přehledů na konci tohoto kurzu.

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonování úložiště pomocí skriptů a dat

1. Přihlaste se k předplatnému Azure. Pokud máte v plánu používat Azure Cloud Shell, pak vyberte **Try it** v pravém horním rohu bloku kódu. Jinak zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ujistěte se, že jste členem [vlastníka](../role-based-access-control/built-in-roles.md)role Azure . Nahraďte `user@contoso.com` svůj účet a zadejte příkaz:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Pokud není vrácen žádný záznam, nejste členem a nebudete moci dokončit tento kurz.

1. Stáhněte si data a skripty pro tento kurz z [úložiště ETL eTL přehledy prodeje HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Zadejte následující příkaz:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Ujistěte se, že `salesdata scripts templates` byly vytvořeny. Ověřte pomocí následujícího příkazu:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Nasazení prostředků Azure potřebných pro kanál

1. Přidejte oprávnění ke spuštění pro všechny skripty zadáním:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Nastavte proměnnou pro skupinu prostředků. Nahraďte `RESOURCE_GROUP_NAME` název existující nebo nové skupiny prostředků a zadejte příkaz:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Spusťte skript. Nahraďte `LOCATION` požadovanou hodnotou a zadejte příkaz:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Pokud si nejste jisti, kterou oblast určit, můžete načíst seznam podporovaných oblastí pro vaše předplatné pomocí příkazu [az seznam účtů.](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations)

    Příkaz nasadí následující prostředky:

    * Účet úložiště objektů blob Azure. Tento účet bude obsahovat údaje o prodeji společnosti.
    * Účet Azure Data Lake Storage Gen2. Tento účet bude sloužit jako účet úložiště pro oba clustery HDInsight. Přečtěte si další informace o HDInsightu a Storage Storage Data Lake Gen2 v [Azure HDInsight integraci s Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uživatelem přiřazená spravovaná identita. Tento účet poskytuje clusterům HDInsight přístup k účtu Data Lake Storage Gen2.
    * Cluster Apache Spark. Tento cluster se použije k vyčištění a transformaci nezpracovaných dat.
    * Cluster [interaktivních dotazů](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Tento cluster umožní dotazování na prodejní data a jejich vizualizaci pomocí Power BI.
    * Virtuální síť Azure podporovaná pravidly skupiny zabezpečení sítě (NSG). Tato virtuální síť umožňuje clusterům komunikovat a zabezpečit jejich komunikaci.

Vytvoření clusteru může trvat přibližně 20 minut.

Výchozí heslo pro přístup SSH ke `Thisisapassword1`clusterům je . Chcete-li změnit heslo, přejděte `./templates/resourcesparameters_remainder.json` do souboru a `sparksshPassword` `sparkClusterLoginPassword`změňte heslo pro parametry , `llapClusterLoginPassword`, a `llapsshPassword` parametry.

### <a name="verify-deployment-and-collect-resource-information"></a>Ověření nasazení a shromažďování informací o prostředcích

1. Pokud chcete zkontrolovat stav vašeho nasazení, přejděte do skupiny prostředků na webu Azure Portal. V části **Nastavení**vyberte **Nasazení**a potom v nasazení. Zde můžete zobrazit prostředky, které byly úspěšně nasazeny a prostředky, které jsou stále v průběhu.

1. Chcete-li zobrazit názvy clusterů, zadejte následující příkaz:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Chcete-li zobrazit účet úložiště Azure a přístupový klíč, zadejte následující příkaz:

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

Azure Data Factory je nástroj, který pomáhá automatizovat Azure Pipelines. Není to jediný způsob, jak tyto úkoly splnit, ale je to skvělý způsob, jak automatizovat procesy. Další informace o Azure Data Factory najdete v [dokumentaci k Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Tato továrna na data bude mít jeden kanál se dvěma aktivitami:

* První aktivita zkopíruje data z úložiště objektů blob Azure do účtu úložiště Storage 2 data Lake, aby napodobila ingestování dat.
* Druhá aktivita změní data v clusteru Spark. Skript transformuje data odebráním nežádoucích sloupců. Také připojí nový sloupec, který vypočítá výnosy, které generuje jedna transakce.

Pokud chcete nastavit kanál Azure Data Factory, spusťte následující příkaz.  Pořád bys měl `hdinsight-sales-insights-etl` být v adresáři.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Tento skript dělá následující věci:

1. Vytvoří instanční `Storage Blob Data Contributor` objekt s oprávněními na účet úložiště Data Lake Storage Gen2.
1. Získá ověřovací token pro autorizaci požadavků POST do [rozhraní REST API systému dat Úložiště dat Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. V souborech `sparktransform.py` a `query.hql` vyplní skutečný název vašeho účtu úložiště Data Lake Storage Gen2.
1. Získá klíče úložiště pro účty úložiště datového jezera Gen2 a úložiště objektů blob.
1. Vytvoří další nasazení prostředků k vytvoření kanálu Azure Data Factory, s jeho přidružené propojené služby a aktivity. Předá klíče úložiště jako parametry do souboru šablony tak, aby propojené služby mohly správně přistupovat k účtům úložiště.

## <a name="run-the-data-pipeline"></a>Spuštění datového kanálu

### <a name="trigger-the-data-factory-activities"></a>Aktivace aktivit datové továrny

První aktivita v kanálu Data Factory, kterou jste vytvořili, přesune data z úložiště objektů Blob do gen2 úložiště datového jezera. Druhá aktivita použije transformace Spark na data a uloží transformované soubory .csv do nového umístění. Dokončení celého kanálu může trvat několik minut.

Chcete-li načíst název Data Factory, zadejte následující příkaz:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Chcete-li spustit potrubí, můžete buď:

* Aktivuj kanál Data Factory v PowerShellu. `RESOURCEGROUP`Nahraďte `DataFactoryName` a s příslušnými hodnotami spusťte následující příkazy:

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

    Podle potřeby `Get-AzDataFactoryV2PipelineRun` znovu spusťte ke sledování průběhu.

    Nebo

* Otevřete datovou továrnu a vyberte **Autor & monitor**. Aktivuj `IngestAndTransform` kanál z portálu. Informace o spouštění kanálů prostřednictvím portálu najdete v tématu [Vytváření clusterů Apache Hadoop na vyžádání v HDInsight u Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Chcete-li ověřit, zda byl kanál spuštěn, můžete provést některý z následujících kroků:

* Přejděte do části **Monitor** v datové továrně prostřednictvím portálu.
* V Průzkumníkovi azure storage najdete účet úložiště Storage Gen 2 úložiště datového jezera. Přejděte `files` do systému souborů a `transformed` potom přejděte do složky a zkontrolujte její obsah, abyste zjistili, zda byl kanál úspěšný.

Další způsoby transformace dat pomocí hdinsightu najdete v [tomto článku o používání poznámkového bloku Jupyter](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Vytvoření tabulky v clusteru interaktivních dotazů pro zobrazení dat v Power BI

1. Zkopírujte `query.hql` soubor do clusteru LLAP pomocí protokolu SCP. Zadejte příkaz:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Připomenutí: Výchozí heslo `Thisisapassword1`je .

1. Pomocí SSH pro přístup ke clusteru LLAP. Zadejte příkaz:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Ke spuštění skriptu použijte následující příkaz:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Tento skript vytvoří spravovanou tabulku v clusteru interaktivních dotazů, ke které máte přístup z Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Vytvoření řídicího panelu Power BI z dat o prodeji

1. Otevřete Power BI Desktop.

1. V nabídce přejděte na **Získat data** > **Více...**  > Interaktivní dotaz **Azure** > **HDInsight**.

1. Vyberte **Connect** (Připojit).

1. Z dialogového okna **Interaktivní dotaz HDInsight:**
    1. Do textového pole **Server** zadejte název clusteru LLAP ve formátu `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Do **database** textového pole `default`databáze zadejte .
    1. Vyberte **OK**.

1. Z dialogového okna **AzureHive:**
    1. Do textového pole Uživatelské `admin` **jméno** zadejte .
    1. Do **Password** textového pole `Thisisapassword1`Heslo zadejte .
    1. Vyberte **Connect** (Připojit).

1. Z **navigátoru** `sales`vyberte `sales_raw` a/nebo zobrazte náhled dat. Po načtení dat můžete experimentovat s řídicím panelem, který chcete vytvořit. Podívejte se na následující odkazy, které vám pomohou začít s řídicími panely Power BI:

* [Úvod do řídicích panelů pro návrháře Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Kurz: Začínáme se službou Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte všechny prostředky pomocí následujícího příkazu, abyste za ně nebyli účtováni.

1. Chcete-li skupinu prostředků odebrat, zadejte příkaz:

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
> [Extrahovat, transformovat a načíst (ETL) v měřítku](./hadoop/apache-hadoop-etl-at-scale.md)
