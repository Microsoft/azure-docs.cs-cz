---
title: 'Kurz: vytvoření kanálu ukončení pro extrakci, transformaci, načítání (ETL) k odvození obchodních přehledů'
description: Naučte se, jak pomocí Azure HDInsight vytvořit kanály ETL k odvozování přehledů z prodejních dat pomocí clusterů Spark na vyžádání a Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695817"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Kurz: vytvoření uceleného datového kanálu pro odvození obchodních přehledů

V tomto kurzu vytvoříte kompletní datový kanál, který provede operace extrakce, transformace a načtení. Kanál bude používat clustery Apache Spark a Apache Hive běžící na Azure HDInsight pro dotazování a manipulaci s daty. Mezi další používané technologie patří Data Lake Storage Gen2 pro úložiště dat a Power BI pro vizualizaci.

Tento datový kanál kombinuje data ze všech různých úložišť, odebírá všechna nepotřebná data, přidává nová data a načítá je zpátky do vašeho úložiště, aby bylo možné vizualizovat obchodní přehledy. Přečtěte si další informace o kanálech ETL [extrakce, transformaci a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Na konci tohoto kurzu si stáhněte [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) k vizualizaci obchodních přehledů.

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonování úložiště pomocí skriptů a dat

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. V horním řádku nabídek otevřete Cloud Shell. Pokud se zobrazí výzva Azure Cloud Shell, vyberte předplatné pro vytvoření sdílené složky.

    ![Otevřít Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. V rozevírací nabídce vybrat prostředí vyberte "bash".
1. Přihlaste se ke svému účtu Azure a nastavte předplatné. 
1. Nastavte skupinu prostředků pro projekt.
    1. Vyberte jedinečný název skupiny prostředků.
    1. Spuštěním fragmentu kódu níže v Azure Cloud Shell nastavte proměnné, které budou použity v pozdějších krocích.

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Stáhněte si data a skripty pro tento kurz z [úložiště ETL Sales Insights HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) , a to tak, že na Cloud Shell zadáte následující příkazy:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. V příkazovém řádku prostředí zadejte `ls`, abyste viděli, že se vytvořily následující soubory a adresáře:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Nasazení prostředků Azure potřebných pro kanál 

1. Přidat oprávnění EXECUTE pro skript `chmod +x scripts/*.sh`
1. Pomocí příkazu `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` spusťte skript pro nasazení následujících prostředků v Azure:

    1. Účet Azure Blob Storage – tento účet bude obsahovat data o prodeji společnosti.
    2. Účet Azure Data Lake Storage Gen2 – tento účet bude sloužit jako účet úložiště pro oba clustery HDInsight. Přečtěte si další informace o HDInsight a Data Lake Storage Gen2 v tématu [integrace služby Azure HDInsight s Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Spravovaná identita přiřazená uživatelem – tento účet uděluje clusterům HDInsight přístup k účtu Data Lake Storage Gen2.
    4. Apache Spark Luster – tento cluster se použije k vyčištění a transformaci nezpracovaných dat.
    5. Cluster interaktivních dotazů Apache Hive – tento cluster umožní dotazování na prodejní data vizualizace s využitím Power BI
    6. Virtuální síť Azure, kterou podporuje pravidla skupiny zabezpečení sítě (NSG) – Tato virtuální síť umožňuje clusterům komunikovat a také zabezpečit jejich komunikaci. 

Vytvoření clusteru může trvat přibližně 20 minut.

Skript `resources.sh` obsahuje následující příkaz, který používá šablonu Správce prostředků (`resourcestemplate.json`) k vytvoření zadaných prostředků s požadovanou konfigurací.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Skript `resources.sh` také nahraje soubory CSV Sales data do nově vytvořeného účtu úložiště BLOB pomocí příkazu:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Výchozí heslo používané pro přístup SSH ke clusterům je `Thisisapassword1`. Pokud byste chtěli změnit heslo, přejděte na soubor `resourcesparameters.json` a změňte heslo pro parametry `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` a `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Ověření nasazení a shromažďování informací o prostředcích

1. Pokud chcete zjistit stav svého nasazení, přejděte do skupiny prostředků v Azure Portal. V části **Nastavení**klikněte na **nasazení** . Klikněte na název nasazení `ResourcesDeployment`. Tady vidíte, které prostředky se úspěšně nasadily a které pořád probíhá.
1. Po dokončení nasazení navštivte Azure Portal > **skupiny prostředků** > < RESOURCE_GROUP_NAME >
1. Vyhledejte nový účet služby Azure Storage, který byl vytvořen pro uložení prodejních souborů. Název účtu úložiště začíná na `blob` a pak obsahuje náhodný řetězec. 
    1. Poznamenejte si název účtu úložiště, abyste ho mohli později použít.
    1. Klikněte na název účtu služby Blob Storage.
    1. Na levé straně portálu v části **Nastavení**klikněte na **přístupové klíče**.
    1. Zkopírujte řetězec do pole **klíč1** a uložte ho pro pozdější použití.
1. Vyhledejte účet Data Lake Storage Gen2, který byl vytvořen jako úložiště pro clustery HDInsight. Tento účet se nachází ve stejné skupině prostředků jako účet úložiště objektů blob, ale začíná na `adlsgen2`.
    1. Poznamenejte si název účtu Data Lake Storage Gen2.
    1. Klikněte na název účtu Data Lake Storage Gen2.
    1. Na levé straně portálu v části **Nastavení**klikněte na **přístupové klíče** .
    1. Zkopírujte řetězec do pole **klíč1** a uložte ho pro pozdější použití.

> [!Note]
> Jakmile budete znát názvy účtů úložiště, můžete také získat klíče účtu pomocí následujícího příkazu na příkazovém řádku Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Vytvoření služby Azure Data Factory

Azure Data Factory je nástroj, který pomáhá automatizovat Azure Pipelines. Nejedná se o jediný způsob, jak tyto úlohy provést, ale je to skvělý způsob, jak tyto procesy automatizovat. Další informace o Azure Data Factory najdete v tématu Další informace o [Azure Data Factory dokumentaci](https://azure.microsoft.com/services/data-factory/). 

Tato Azure Data Factory bude mít jeden kanál se dvěma aktivitami: 

1. První aktivita zkopíruje data z Azure Blob Storage do účtu úložiště Data Lake Storage Gen 2, aby napodoboval přijímání dat.
2. Druhá aktivita převede data v clusteru Spark. Skript transformuje data odebráním nežádoucích sloupců a připojením nového sloupce, který počítá výnosy vygenerované jedinou transakcí.

Pokud chcete nastavit kanál Azure Data Factory, spusťte skript `adf.sh`:

1. Přidejte k souboru oprávnění k provedení pomocí `chmod +x adf.sh`
1. Pomocí příkazu `./adf.sh` skript spusťte. 

Tento skript provádí následující akce:

1. Vytvoří instanční objekt s oprávněním `Storage Blob Data Contributor` v účtu úložiště Data Lake Storage Gen2.
1. Získá ověřovací token pro autorizaci požadavků POST do [REST API systému souborů Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Vyplní skutečný název vašeho účtu úložiště Data Lake Storage Gen2 v souborech `sparktransform.py` a `query.hql`.
1. Získá klíče úložiště pro účet Data Lake Storage Gen2 a Blob Storage.
1. Vytvoří jiné nasazení prostředků, aby se vytvořil kanál Azure Data Factory s přidruženými propojenými službami a aktivitami. Předá klíče úložiště jako parametry souboru šablony, aby propojené služby mohly správně přistupovat k účtům úložiště.

Kanál ADF se nasadí pomocí tohoto příkazu:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Spuštění datového kanálu

### <a name="trigger-the-adf-activities"></a>Aktivovat aktivity ADF

První aktivita v kanálu ADF, kterou jste vytvořili, přesune data z úložiště objektů blob do Data Lake Storage Gen2. Druhá aktivita aplikuje transformace Spark na data a uloží transformovaný soubory CSV do nového umístění. Dokončení celého kanálu může trvat několik minut.

Pro aktivaci kanálů můžete buď:

1. Spusťte následující příkazy, které aktivují kanály ADF v PowerShellu. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Můžete také otevřít Data Factory, vybrat vytvořit & monitorovat a aktivovat kanál pro kopírování a pak z portálu spustit kanál Spark. Informace o aktivaci kanálů prostřednictvím portálu najdete v tématu [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline) .

Chcete-li ověřit, zda byly kanály spuštěny, můžete provést jeden z následujících kroků:

1. Přejděte do části **monitor** v Azure Data Factory prostřednictvím portálu.
2. Přejděte do svého Data Lake Storage Průzkumníku úložiště účtu úložiště Gen 2, přejděte do systému souborů `files` a přejděte do složky `transformed` a zkontrolujte její obsah, abyste viděli, jestli kanál uspěl.

Další způsoby, jak transformovat data pomocí HDInsight, najdete v tomto článku o použití [poznámkového bloku Jupyter](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query) .

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Vytvoření tabulky v clusteru interaktivních dotazů pro zobrazení dat v Power BI

1. Zkopírujte soubor Query. HQL do clusteru LLAP pomocí spojovacího bodu služby:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Spusťte SSH do clusteru LLAP pomocí následujícího příkazu a pak zadejte své heslo. Pokud jste nezměnili soubor `resourcesparameters.json`, heslo je `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Spuštěním následujícího příkazu spusťte skript.

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Tento skript vytvoří spravovanou tabulku v clusteru interaktivních dotazů, ke kterému můžete přistupovat z Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Vytvoření řídicího panelu Power BI z prodejních dat

1. Otevřít Power BI Desktop
1. Vyberte **Načíst data**.
1. Vyhledejte **cluster HDInsight Interactive Query**.
1. Vložte identifikátor URI pro svůj cluster. Měla by být ve formátu `https://<LLAP CLUSTER NAME>.azurehdinsight.net` typ `default` pro databázi.
1. Zadejte uživatelské jméno a heslo, které používáte pro přístup ke clusteru.

Jakmile se data načtou, můžete experimentovat s řídicím panelem, který chcete vytvořit. Začínáme s Power BImi řídicími panely najdete na následujících odkazech:

* [Úvod k řídicím panelům pro návrháře Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Kurz: Začínáme s služba Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte všechny prostředky pomocí následujících kroků, abyste za ně neúčtovali poplatky.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md)
