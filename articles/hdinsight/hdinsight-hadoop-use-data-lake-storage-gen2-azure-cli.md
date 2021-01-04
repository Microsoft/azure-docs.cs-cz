---
title: Vytvoření Azure HDInsight – Azure Data Lake Storage Gen2 – Azure CLI
description: Naučte se používat Azure Data Lake Storage Gen2 s clustery Azure HDInsight pomocí Azure CLI.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: 4ffe5acce831435d3771a2eb48a90b16f584c92a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704006"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Vytvoření clusteru s Data Lake Storage Gen2 pomocí Azure CLI

Pokud chcete vytvořit cluster HDInsight, který používá Data Lake Storage Gen2 pro úložiště, postupujte podle těchto kroků.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte Azure Data Lake Storage Gen2, přečtěte si [část přehled](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit příklady skriptu rozhraní příkazového řádku, máte tři možnosti:
    - Použijte [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal (viz další oddíl).
    - Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu.
    - Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](/cli/azure/install-azure-cli) (2.0.13 nebo novější). Přihlaste se k Azure pomocí `az login` účtu, který je přidružený k předplatnému Azure, pod kterým chcete nasadit uživatelsky přiřazenou spravovanou identitu. Rozhraní příkazového řádku Azure 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Můžete [stáhnout ukázkový soubor šablony](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) a [stáhnout ukázkový soubor parametrů](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Než použijete šablonu a fragment kódu Azure CLI níže, nahraďte následující zástupné symboly jejich správnými hodnotami:

| Zástupný symbol | Popis |
|---|---|
| `<SUBSCRIPTION_ID>` | ID vašeho předplatného Azure |
| `<RESOURCEGROUPNAME>` | Skupina prostředků, ve které chcete vytvořit nový cluster a účet úložiště. |
| `<MANAGEDIDENTITYNAME>` | Název spravované identity, který bude mít oprávnění k vašemu účtu úložiště pomocí Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nový účet úložiště s Azure Data Lake Storage Gen2, který se vytvoří. |
| `<FILESYSTEMNAME>`  | Název systému souborů, který by měl tento cluster používat v účtu úložiště. |
| `<CLUSTERNAME>` | Název clusteru HDInsight. |
| `<PASSWORD>` | Vaše zvolené heslo pro přihlášení ke clusteru pomocí SSH a řídicího panelu Ambari |

Níže uvedený fragment kódu provede následující úvodní kroky:

1. Přihlásí se k účtu Azure.
1. Nastaví aktivní předplatné, ve kterém se budou provádět operace vytvoření.
1. Vytvoří novou skupinu prostředků pro nové aktivity nasazení.
1. Vytvoří uživatelem přiřazenou spravovanou identitu.
1. Přidá do Azure CLI rozšíření pro použití funkcí pro Data Lake Storage Gen2.
1. Pomocí příznaku vytvoří nový účet úložiště s Data Lake Storage Gen2 `--hierarchical-namespace true` .

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Potom se přihlaste k portálu. Přidejte do role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště novou spravovanou identitu přiřazenou uživatelem. Tento krok je popsaný v kroku 3 tématu [použití Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Ujistěte se, že váš účet úložiště má přiřazenou identitu role **Přispěvatel dat objektů BLOB úložiště** , jinak se vytvoření clusteru nezdaří.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pro odebrání prostředků zadejte všechny nebo některé z následujících příkazů:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili cluster HDInsight. Nyní se naučíte pracovat s clusterem.

### <a name="apache-spark-clusters"></a>Clustery Apache Spark

* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBA

* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací Java pro Apache HBA v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
