---
title: 'Rychlý Start: Vytvoření clusteru Apache Spark v Azure HDInsight pomocí Azure PowerShell'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShellu vytvořit cluster Apache Spark v Azure HDInsight a jak spustit jednoduchý dotaz Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: fd7300fd21d20476305dc5404158f255d533ffed
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539322"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí prostředí PowerShell

V tomto rychlém startu použijete Azure PowerShell k vytvoření clusteru Apache Spark ve službě Azure HDInsight. Pak vytvoříte Poznámkový blok Jupyter a použijete ho ke spouštění dotazů Spark SQL pro Apache Hive tabulek. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Rozhraní Apache Spark Framework pro Azure HDInsight umožňuje rychlé analýzy dat a výpočetní prostředí pomocí zpracování v paměti. Poznámkový blok Jupyter vám umožňuje pracovat s daty, kombinovat kód s textem Markdownu a provádět jednoduché vizualizace.

[Přehled: Apache Spark ve službě Azure HDInsight](apache-spark-overview.md)  |  [Apache Spark](https://spark.apache.org/)  |  [Apache Hive](https://hive.apache.org/)  |  [Jupyter notebook](https://jupyter.org/)

Pokud používáte více clusterů společně, budete chtít vytvořit virtuální síť a pokud používáte cluster Spark, budete také chtít použít konektor pro skladiště z podregistru. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) a [integrace Apache Spark a Apache Hive pomocí konektoru skladu s podregistru](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Požadavek

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [PowerShell AZ Module](/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Vytvoření clusteru Apache Spark v HDInsight

> [!IMPORTANT]  
> Clustery HDInsight se fakturují za minutu bez ohledu na to, jestli je používáte, nebo ne. Až přestanete cluster používat, nezapomeňte ho odstranit. Další informace najdete v části [Vyčištění prostředků](#clean-up-resources) tohoto článku.

Vytvoření clusteru HDInsight zahrnuje vytvoření těchto objektů a prostředků Azure:

- Skupina prostředků Azure. Skupina prostředků Azure je kontejner prostředků Azure.
- Účet služby Azure Storage nebo Azure Data Lake Storage.  Každý cluster HDInsight vyžaduje závislé úložiště dat. V tomto rychlém startu vytvoříte cluster, který jako úložiště clusteru používá objekty blob Azure Storage. Další informace o použití Data Lake Storage Gen2 najdete v tématu [Rychlý start: Nastavení clusterů ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
- Cluster různých typů clusterů v HDInsight.  V tomto rychlém startu vytvoříte cluster Spark 2.3.

Pomocí skriptu PowerShellu vytvoříte prostředky. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po spuštění skriptu PowerShell se zobrazí výzva k zadání následujících hodnot:

|Parametr|Hodnota|
|------|------|
|Název skupiny prostředků Azure | Zadejte jedinečný název pro skupinu prostředků.|
|Umístění| Zadejte oblast Azure, například „USA – střed“. |
|Název výchozího účtu úložiště | Zadejte jedinečný název účtu úložiště. |
|Název clusteru | Zadejte jedinečný název pro cluster HDInsight.|
|Přihlašovací údaje clusteru | Tento účet slouží pro připojení k řídicímu panelu clusteru v pozdější fázi rychlého startu.|
|Přihlašovací údaje uživatele SSH | Klienti SSH se dají použít k vytvoření vzdálené relace příkazového řádku s clustery v HDInsight.|

1. Vyberte **vyzkoušet** v pravém horním rohu pro následující blok kódu pro otevření [Azure Cloud Shell](../../cloud-shell/overview.md)a pak postupujte podle pokynů pro připojení k Azure.

2. Zkopírujte následující skript prostředí PowerShell a vložte ho do Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Vytvoření clusteru trvá přibližně 20 minut. Než budete moct pokračovat k další relaci, musí se cluster nejdříve vytvořit.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být tím, že nemáte správná oprávnění k tomu. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

[Jupyter notebook](https://jupyter.org/) je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **clustery HDInsight** .
   
   ![Snímek obrazovky zobrazující Azure Portal hledání H D.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. V seznamu vyberte cluster, který jste vytvořili.
   
   ![Snímek obrazovky s clusterem, který jste vytvořili, zobrazuje H D Insight clusterů.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. Na stránce **Přehled** clusteru vyberte **řídicí panely clusteru** a pak vyberte **Jupyter notebook** . Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

1. Vyberte **Nový**  >  **PySpark** a vytvořte Poznámkový blok.

   ![Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).

## <a name="run-apache-spark-sql-statements"></a>Spustit Apache Spark příkazy SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![stav jádra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "stav jádra")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené. 
1. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Když použijete Jupyter Notebook s clusterem Spark v HDInsight, získáte předvolbu `sqlContext` , kterou můžete použít ke spouštění dotazů na podregistr pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavený kontext `sqlContext`. Dotaz načte prvních 10 řádků z tabulky Hive ( **hivesampletable** ), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Získání výsledků trvá přibližně 30 sekund. Výstup vypadá takto:

    ![Apache Hive dotazování ve Sparku ve službě HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Dotaz na podregistr v HDInsight Spark")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazů na podregistr v HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazů na podregistr v HDInsight")

1. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit** . Ukončením poznámkového bloku se uvolní prostředky clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

HDInsight ukládá vaše data ve službě Azure Storage nebo v úložišti Azure Data Lake, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na kurzu uvedeném v části [Další kroky](#next-steps), měli byste cluster zachovat.

Přepněte zpět na web Azure Portal a vyberte **Odstranit** .

![Azure Portal odstranit cluster HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků** . Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Vyčištění postupného pomocí PowerShellu AZ Module

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Spark v HDInsight a spustit základní dotaz Spark SQL. Přejděte k dalšímu kurzu, kde se dozvíte, jak používat cluster HDInsight ke spouštění interaktivních dotazů na ukázkových datech.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)