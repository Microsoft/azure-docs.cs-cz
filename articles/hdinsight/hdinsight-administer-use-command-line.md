---
title: Správa clusterů Hadoop pomocí rozhraní příkazového řádku Azure – Azure HDInsight
description: Zjistěte, jak používat rozhraní příkazového řádku Azure pro správu clusterů Hadoop v Azure HDInsight. Rozhraní příkazového řádku Azure funguje ve Windows, Mac a Linux.
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: dea0f004c4283bf594e46097092a52dedabb9f4b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599016"
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Správa clusterů Hadoop v HDInsight pomocí rozhraní příkazového řádku Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Další informace o použití [rozhraní příkazového řádku Azure](../cli-install-nodejs.md) ke správě clusterů Hadoop v Azure HDInsight. Rozhraní příkazového řádku Azure je implementované v Node.js. Dá se použít na jakékoli platformě, která podporuje Node.js, včetně systému Windows, Mac a Linux. V současné době HDInsight nepodporuje [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure).

Tento článek se týká jenom používání rozhraní příkazového řádku Azure s HDInsight. Obecné informace o tom, jak pomocí Azure CLI, najdete v článku [instalace a konfigurace rozhraní příkazového řádku Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Rozhraní příkazového řádku Azure** – Informace týkající se instalace a konfigurace najdete v tématu [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
* **Připojení k Azure**, pomocí následujícího příkazu:

    ```cli
    azure login
    ```
  
    Další informace týkající se ověřování pomocí pracovního nebo školního účtu najdete v tématu [Připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).
* **Přepněte do režimu Azure Resource Manager**, a to pomocí následujícího příkazu:
  
    ```cli
    azure config mode arm
    ```

Chcete-li zobrazit nápovědu, použijte **-h** přepnout.  Příklad:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Vytvoření clusterů pomocí rozhraní příkazového řádku
Zobrazit [vytváření clusterů v HDInsight pomocí Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Seznam a zobrazit podrobnosti o clusteru
Seznam a zobrazit podrobnosti o clusteru, použijte následující příkazy:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Příkazového řádku zobrazit seznam clusterů][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Odstranění clusterů
Pomocí následujícího příkazu odstraňte cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Můžete také odstranit cluster tak, že odstraníte skupinu prostředků, která obsahuje clusteru. Poznámka: Tato akce odstraní všechny prostředky ve skupině, včetně výchozího účtu úložiště.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálování clusterů
Chcete-li změnit velikost clusteru Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Povolí nebo zakáže přístup protokolu HTTP pro cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Povolí nebo zakáže přístup protokolu RDP pro cluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak provádět různé úlohy správy clusteru HDInsight. Další informace naleznete v následujících článcích:

* [Správa HDInsight pomocí webu Azure Portal][hdinsight-admin-portal]
* [Správa HDInsight pomocí Azure Powershellu][hdinsight-admin-powershell]
* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Jak používat rozhraní příkazového řádku Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Seznam a zobrazení clusterů"
