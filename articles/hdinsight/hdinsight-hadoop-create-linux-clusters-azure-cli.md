---
title: Vytváření clusterů systému Hadoop pomocí příkazového řádku-Azure HDInsight | Microsoft Docs
description: Informace o vytváření clusterů HDInsight pomocí rozhraní příkazového řádku Azure napříč platformami 1.0.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: e56829c771ae47933f79c519920a20c1308873fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31397582"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Vytvoření clusterů HDInsight pomocí rozhraní příkazového řádku Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroky v tohoto návodu dokumentu vytváření clusteru HDInsight 3.5 pomocí Azure CLI 1.0.

> [!IMPORTANT]
> Toto téma popisuje, jak vytvořit cluster služby HDInsight pomocí Azure CLI 1.0. Tato verze rozhraní příkazového řádku je zastaralá a podpora pro vytváření clusterů HDInsight nebyl přidán do Azure CLI 2.0.
>
> Prostředí Azure PowerShell můžete použít také k vytváření a správě clusterů HDInsight. Další informace najdete v tématu [Tvorba clusterů HDInsight pomocí Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) dokumentu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure CLI**. Kroky v tomto dokumentu byly naposledy testovány s Azure CLI verze 0.10.14.

    > [!IMPORTANT]
    > Azure CLI 1.0 je zastaralý a podpora pro vytváření clusterů HDInsight nebyl přidán do Azure CLI 2.0.

## <a name="log-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

Postupujte podle kroků popsaných v tématu [Připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli) a připojte se k předplatnému pomocí metody **přihlášení**.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Z příkazového řádku, jako je například PowerShell nebo Bash je třeba provést následující kroky.

1. K ověření vašeho předplatného Azure, použijte následující příkaz:

        azure login

    Zobrazí se výzva k zadání jména a hesla. Pokud máte víc předplatných Azure, použijte `azure account set <subscriptionname>` nastavte předplatné, které příkazy rozhraní příkazového řádku Azure používají.

2. Přepněte do režimu Azure Resource Manager pomocí následujícího příkazu:

        azure config mode arm

3. Vytvořte skupinu prostředků. Tato skupina prostředků obsahuje HDInsight cluster a související účet úložiště.

        azure group create groupname location

    * Nahraďte `groupname` s jedinečným názvem skupiny.

    * Nahraďte `location` s zeměpisnou oblast, kterou chcete vytvořit skupinu v.

       Seznam platných umístění, použijte `azure location list` příkaz a pak použijte jednu z umístění z `Name` sloupce.

4. Vytvoření účtu úložiště Tento účet úložiště se používá jako výchozí úložiště pro HDInsight cluster.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Nahraďte `groupname` s názvem skupiny vytvořené v předchozím kroku.

    * Nahraďte `location` s stejné umístění, které jsou používané v předchozím kroku.

    * Nahraďte `storagename` s jedinečným názvem pro účet úložiště.

        > [!NOTE]
        > Další informace o parametrech použité v tomto příkazu použít `azure storage account create -h` chcete zobrazit nápovědu pro tento příkaz.

5. Načtěte klíč používaný k přístupu k účtu úložiště.

        azure storage account keys list -g groupname storagename

    * Nahraďte `groupname` s názvem skupiny prostředků.
    * Nahraďte `storagename` s názvem účtu úložiště.

     V datech, která je vrácena, uložte `key` hodnota `key1`.

6. Vytvoření clusteru HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Nahraďte `groupname` s názvem skupiny prostředků.

    * Nahraďte `Hadoop` typu clusteru, který chcete vytvořit. Například `Hadoop`, `HBase`, `Kafka`, `Spark`, nebo `Storm`.

     > [!IMPORTANT]
     > HDInsight clustery mají různé typy, které odpovídají zatížení nebo technologie, která clusteru je přizpůsobená pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je například Storm a HBase v jednom clusteru.

    * Nahraďte `location` s stejné umístění, které jsou používané v předchozích krocích.

    * Nahraďte `storagename` s názvem účtu úložiště.

    * Nahraďte `storagekey` klíčem získaných v předchozím kroku.

    * Pro `--defaultStorageContainer` parametr, použijte stejný název jako jste používali pro cluster.

    * Nahraďte `admin` a `httppassword` s jméno a heslo, které chcete použít při přístupu ke clusteru pomocí protokolu HTTPS.

    * Nahraďte `sshuser` a `sshuserpassword` pomocí uživatelského jména a hesla, které chcete použít při přístupu ke clusteru pomocí protokolu SSH

    > [!IMPORTANT]
    > Tento příklad vytvoří cluster se dvěma uzly pracovního procesu. Můžete také změnit počet uzlů pracovního procesu až po vytvoření clusteru provedením operace škálování. Pokud máte v úmyslu používat více než 32 uzlů pracovního procesu, je nutné vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM. Velikost hlavního uzlu lze nastavit pomocí `--headNodeSize` parametru během vytváření clusteru.
    >
    > Další informace o velikosti uzlu a souvisejících nákladů, najdete v části [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

    To může trvat několik minut na dokončení procesu vytváření clusteru. Obvykle přibližně 15.

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

Teď, když jste úspěšně vytvořili clusteru HDInsight pomocí rozhraní příkazového řádku Azure, použijte následující postupy se dozvíte, jak pracovat s clusteru:

### <a name="hadoop-clusters"></a>Clustery Hadoop

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase

* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v jazyce Java pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm

* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent, Python v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologie se Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
