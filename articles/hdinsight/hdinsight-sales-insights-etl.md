---
title: 'Kurz: vytvoření kompletního kanálu ETL pro odvoditelné prodejní přehledy'
description: Naučte se, jak pomocí Azure HDInsight vytvořit kanály ETL k odvozování přehledů z prodejních dat pomocí clusterů Spark na vyžádání a Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 07714575bb4adfccf5d72a093874852c1bfebcc8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843783"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Kurz: vytvoření uceleného datového kanálu pro odvození obchodních přehledů

V tomto kurzu vytvoříte kompletní datový kanál, který provede operace extrakce, transformace a načítání (ETL). Kanál bude používat clustery Apache Spark a Apache Hive běžící na Azure HDInsight pro dotazování a manipulaci s daty. Budete také používat technologie, jako je Azure Data Lake Storage Gen2 pro úložiště dat, a Power BI pro vizualizaci.

Tento datový kanál kombinuje data z různých úložišť, odebírá všechna nepotřebná data, přidává nová data a načítá je zpátky do vašeho úložiště pro vizualizaci obchodních přehledů. Další informace o kanálech ETL najdete v tématu věnovaném [extrakci, transformaci a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Na konci tohoto kurzu si stáhněte [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) k vizualizaci obchodních přehledů.

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonování úložiště pomocí skriptů a dat

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V horním řádku nabídek otevřete Azure Cloud Shell. Pokud vás Cloud Shell, vyberte předplatné pro vytvoření sdílené složky.

   ![Otevření služby Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. V rozevírací nabídce **Vybrat prostředí** zvolte **bash**.
1. Přihlaste se ke svému účtu Azure a nastavte předplatné. 
1. Nastavte skupinu prostředků pro projekt.
   1. Pro skupinu prostředků vyberte jedinečný název.
   1. Spusťte následující fragment kódu v Cloud Shell k nastavení proměnných, které budou použity v pozdějších krocích:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Stáhněte si data a skripty pro tento kurz z [úložiště ETL Sales Insights HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) , a to tak, že do Cloud Shell zadáte následující příkazy:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Zadáním `ls` na příkazovém řádku prostředí zjistíte, že byly vytvořeny následující soubory a adresáře:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Nasazení prostředků Azure potřebných pro kanál 

1. Přidejte oprávnění EXECUTE pro skript `chmod +x scripts/*.sh`.
1. Pomocí příkazu `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` spusťte skript pro nasazení následujících prostředků v Azure:

   1. Účet úložiště objektů BLOB v Azure. Tento účet bude obsahovat data o prodeji společnosti.
   2. Účet Azure Data Lake Storage Gen2. Tento účet bude sloužit jako účet úložiště pro oba clustery HDInsight. Přečtěte si další informace o HDInsight a Data Lake Storage Gen2 v tématu [integrace služby Azure HDInsight s Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Spravovaná identita přiřazená uživatelem Tento účet uděluje clusterům HDInsight přístup k účtu Data Lake Storage Gen2.
   4. Cluster Apache Spark. Tento cluster se použije k vyčištění a transformaci nezpracovaných dat.
   5. Cluster interaktivních dotazů Apache Hive. Tento cluster umožní dotazování na data prodeje a jejich vizualizaci pomocí Power BI.
   6. Virtuální síť Azure, kterou podporuje pravidla skupiny zabezpečení sítě (NSG). Tato virtuální síť umožňuje clusterům komunikovat a zabezpečit jejich komunikaci. 

Vytvoření clusteru může trvat přibližně 20 minut.

Skript `resources.sh` obsahuje následující příkaz. Tento příkaz používá šablonu Azure Resource Manager (`resourcestemplate.json`) k vytvoření zadaných prostředků s požadovanou konfigurací.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Skript `resources.sh` také nahraje soubory Sales data. CSV do nově vytvořeného účtu úložiště objektů BLOB pomocí tohoto příkazu:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Výchozí heslo pro přístup SSH ke clusterům je `Thisisapassword1`. Pokud chcete změnit heslo, přejděte na soubor `resourcesparameters.json` a změňte heslo pro parametry `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` a `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Ověření nasazení a shromažďování informací o prostředcích

1. Pokud chcete zjistit stav svého nasazení, na Azure Portal se podívejte do skupiny prostředků. V části **Nastavení**vyberte **nasazení** . Vyberte název nasazení `ResourcesDeployment`. Tady vidíte prostředky, které se úspěšně nasadily, a prostředky, které pořád probíhají.
1. Po dokončení nasazení přejdete do **skupiny prostředků** Azure Portal > > < RESOURCE_GROUP_NAME >.
1. Vyhledejte nový účet služby Azure Storage, který byl vytvořen pro uložení prodejních souborů. Název účtu úložiště začíná na `blob` a pak obsahuje náhodný řetězec. Udělejte toto:
   1. Poznamenejte si název účtu úložiště, abyste ho mohli později použít.
   1. Vyberte název účtu služby Blob Storage.
   1. Na levé straně portálu v části **Nastavení**vyberte **přístupové klíče**.
   1. Zkopírujte řetězec do pole **klíč1** a uložte ho pro pozdější použití.
1. Vyhledejte účet Data Lake Storage Gen2, který byl vytvořen jako úložiště pro clustery HDInsight. Tento účet se nachází ve stejné skupině prostředků jako účet úložiště objektů blob, ale začíná na `adlsgen2`. Udělejte toto:
   1. Poznamenejte si název účtu Data Lake Storage Gen2.
   1. Vyberte název účtu Data Lake Storage Gen2.
   1. Na levé straně portálu v části **Nastavení**vyberte **přístupové klíče**.
   1. Zkopírujte řetězec do pole **klíč1** a uložte ho pro pozdější použití.

> [!Note]
> Až poznáte názvy účtů úložiště, můžete získat klíče účtu pomocí následujícího příkazu na příkazovém řádku Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Azure Data Factory je nástroj, který pomáhá automatizovat kanály Azure. Nejedná se o jediný způsob, jak tyto úlohy provést, ale je to skvělý způsob, jak procesy automatizovat. Další informace o Azure Data Factory najdete v dokumentaci k [Azure Data Factory](https://azure.microsoft.com/en-us/services/data-factory/). 

Tato datová továrna bude mít jeden kanál se dvěma aktivitami: 

- První aktivita zkopíruje data z úložiště objektů BLOB v Azure do účtu úložiště Data Lake Storage Gen 2, který bude napodobovat přijímání dat.
- Druhá aktivita převede data v clusteru Spark. Skript transformuje data odebráním nežádoucích sloupců. Také přidá nový sloupec, který vypočítá výnosy vygenerované v jedné transakci.

Pokud chcete nastavit kanál Azure Data Factory, spusťte skript `adf.sh`:

1. K přidání oprávnění ke spuštění souboru použijte `chmod +x adf.sh`.
1. Ke spuštění skriptu použijte `./adf.sh`. 

Tento skript provádí následující akce:

1. Vytvoří instanční objekt s oprávněním `Storage Blob Data Contributor` v účtu úložiště Data Lake Storage Gen2.
1. Získá ověřovací token pro autorizaci požadavků POST do [Data Lake Storage Gen2 REST API systému souborů](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Vyplní skutečný název vašeho účtu úložiště Data Lake Storage Gen2 v souborech `sparktransform.py` a `query.hql`.
1. Získá klíče úložiště pro účty Data Lake Storage Gen2 a BLOB Storage.
1. Vytvoří jiné nasazení prostředků, aby se vytvořil kanál Azure Data Factory s přidruženými propojenými službami a aktivitami. Předá klíče úložiště jako parametry souboru šablony, aby propojené služby mohly správně přistupovat k účtům úložiště.

Kanál Data Factory je nasazený pomocí následujícího příkazu:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Spuštění datového kanálu

### <a name="trigger-the-data-factory-activities"></a>Aktivovat aktivity Data Factory

První aktivita v kanálu Data Factory, kterou jste vytvořili, přesune data z úložiště objektů blob do Data Lake Storage Gen2. Druhá aktivita aplikuje transformace Spark na data a uloží transformační soubory. CSV do nového umístění. Dokončení celého kanálu může trvat několik minut.

Pro aktivaci kanálů můžete buď:

- Spuštěním následujících příkazů aktivujte kanály Data Factory v PowerShellu: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Otevřete datovou továrnu a vyberte **vytvořit & monitorování**. Aktivujte kanál pro kopírování a pak kanál Spark z portálu. Informace o aktivaci kanálů prostřednictvím portálu najdete v tématu [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Chcete-li ověřit, zda byly kanály spuštěny, můžete provést jeden z následujících kroků:

- V datové továrně na portálu můžete přejít do části **monitorování** .
- V Průzkumník služby Azure Storage přejít na účet úložiště Data Lake Storage Gen 2. Přejděte do systému souborů `files` a potom přejděte do složky `transformed` a zkontrolujte její obsah, abyste viděli, jestli kanál úspěšně uspěl.

Další způsoby, jak transformovat data pomocí služby HDInsight, najdete v [tomto článku o použití Jupyter notebook](/spark/apache-spark-load-data-run-query.md).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Vytvoření tabulky v clusteru interaktivních dotazů pro zobrazení dat v Power BI

1. Zkopírujte soubor `query.hql` do clusteru LLAP pomocí spojovacího bodu služby:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Pomocí SSH pro přístup ke clusteru LLAP použijte následující příkaz a zadejte své heslo. Pokud jste nezměnili soubor `resourcesparameters.json`, heslo je `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Ke spuštění skriptu použijte následující příkaz:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Tento skript vytvoří spravovanou tabulku v clusteru interaktivních dotazů, ke kterému můžete přistupovat z Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Vytvoření řídicího panelu Power BI z prodejních dat

1. Otevřete Power BI Desktop.
1. Vyberte **Načíst data**.
1. Vyhledejte **cluster HDInsight Interactive Query**.
1. Vložte identifikátor URI pro svůj cluster. Měla by mít formát `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Jako databázi zadejte `default`.
1. Zadejte uživatelské jméno a heslo, které používáte pro přístup ke clusteru.

Po načtení dat můžete experimentovat s řídicím panelem, který chcete vytvořit. Pokud chcete začít s Power BI řídicími panely, přečtěte si následující odkazy:

* [Úvod k řídicím panelům pro návrháře Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Kurz: Začínáme s služba Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte všechny prostředky pomocí následujícího příkazu, abyste se za ně neúčtovali.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání (ETL) ve velkém měřítku](./hadoop/apache-hadoop-etl-at-scale.md)
