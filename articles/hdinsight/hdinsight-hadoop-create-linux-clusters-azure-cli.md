---
title: Vytvořte clustery systému Apache Hadoop pomocí Azure CLI classic – Azure HDInsight
description: Zjistěte, jak vytvářet clustery HDInsight pomocí multiplatformní rozhraní CLI Azure classic.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 93ee51c8a00e5cfcbffd56f96b627b68dd124aea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034689"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Vytvoření clusterů HDInsight pomocí rozhraní příkazového řádku Azure Classic

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroky v tomto názorném postupu dokumentu, vytvoření clusteru HDInsight 3.5 pomocí rozhraní příkazového řádku Azure Classic.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure Classic CLI**. Kroky v tomto dokumentu poslední byly testovány s klasické rozhraní příkazového řádku Azure verze 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

Postupujte podle kroků popsaných v [připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli) a připojte se k předplatnému pomocí **přihlášení** metody.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Následující kroky je třeba provést z příkazového řádku, jako je PowerShell nebo Bash.

1. K ověření ke svému předplatnému Azure, použijte následující příkaz:

        azure login

    Zobrazí se výzva k zadání jména a hesla. Pokud máte více předplatných Azure, použijte `azure account set <subscriptionname>` nastavit předplatné, které pomocí příkazů rozhraní příkazového řádku classic.

2. Přepněte do režimu Azure Resource Manager pomocí následujícího příkazu:

        azure config mode arm

3. Vytvořte skupinu prostředků. Tato skupina prostředků obsahuje HDInsight cluster a související účtu úložiště.

        azure group create groupname location

    * Nahraďte `groupname` s jedinečným názvem skupiny.

    * Nahraďte `location` s geografickou oblast, kterou chcete vytvořit skupinu v.

       Seznam platných umístění, použijte `azure location list` příkaz a pak použijte jednu z umístění, ze `Name` sloupce.

4. Vytvoření účtu úložiště Tento účet úložiště se používá jako výchozí úložiště pro HDInsight cluster.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Nahraďte `groupname` s názvem skupiny vytvořené v předchozím kroku.

    * Nahraďte `location` pomocí stejné umístění použité v předchozím kroku.

    * Nahraďte `storagename` s jedinečným názvem účtu úložiště.

        > [!NOTE]
        > Další informace o parametrech použité v tomto příkazu použít `azure storage account create -h` Chcete-li zobrazit nápovědu pro tento příkaz.

5. Načtení klíče pro přístup k účtu úložiště.

        azure storage account keys list -g groupname storagename

    * Nahraďte `groupname` s názvem skupiny prostředků.
    * Nahraďte `storagename` s názvem účtu úložiště.

     V datech, která je vrácena, uložte `key` hodnota `key1`.

6. Vytvoření clusteru HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Nahraďte `groupname` s názvem skupiny prostředků.

    * Nahraďte `Hadoop` s typem clusteru, který chcete vytvořit. Například `Hadoop`, `HBase`, `Kafka`, `Spark`, nebo `Storm`.

     > [!IMPORTANT]
     > HDInsight clustery se dělí na různé typy, které odpovídají úlohy nebo technologie, která clusteru je vyladěný pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je Storm a HBase na jednom clusteru.

    * Nahraďte `location` pomocí stejné umístění, které jsou používané v předchozích krocích.

    * Nahraďte `storagename` názvem účtu úložiště.

    * Nahraďte `storagekey` s klíčem, kterou jste získali v předchozím kroku.

    * Pro `--defaultStorageContainer` parametrů, použijte stejný název, který používáte pro cluster.

    * Nahraďte `admin` a `httppassword` pomocí jména a hesla, které chcete použít při přístupu ke clusteru prostřednictvím protokolu HTTPS.

    * Nahraďte `sshuser` a `sshuserpassword` pomocí uživatelského jména a hesla, které chcete použít při přístupu ke clusteru pomocí SSH

    > [!IMPORTANT]
    > Tento příklad vytvoří cluster se dvěma uzly pracovního procesu. Po vytvoření clusteru můžete také změnit počet uzlů pracovního procesu pomocí provádí operace škálování. Pokud máte v úmyslu používat více než 32 uzlů pracovního procesu, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM. Velikost hlavního uzlu můžete nastavit pomocí `--headNodeSize` parametru během vytváření clusteru.
    >
    > Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Může trvat několik minut na dokončení procesu vytváření clusteru. Obvykle přibližně 15.

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

Teď, když úspěšně vytvoříte clusteru služby HDInsight pomocí rozhraní příkazového řádku classic, použijte následující postup, jak pracovat s vaším clusterem:

### <a name="hadoop-clusters"></a>Clustery Hadoop

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase

* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v Javě pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm

* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií se Stormem v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
