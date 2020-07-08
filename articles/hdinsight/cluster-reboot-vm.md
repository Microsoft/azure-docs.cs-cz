---
title: Restartování virtuálních počítačů pro cluster Azure HDInsight
description: Přečtěte si, jak restartovat nereagující virtuální počítače pro cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077010"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Restartování virtuálních počítačů pro cluster HDInsight

Clustery HDInsight obsahují skupiny virtuálních počítačů jako uzly clusteru. U dlouho spuštěných clusterů můžou tyto uzly po různých důvodech přestat reagovat. Tento článek popisuje, jak restartovat nereagující virtuální počítače v clusteru HDInsight.

## <a name="when-to-reboot"></a>Kdy restartovat

> [!WARNING]  
> Restartování virtuálních počítačů v clusteru přináší výpadky uzlu a restartování služeb na uzlu. 

I když se uzel restartuje, může se stát, že cluster přestane být v pořádku, úlohy se můžou zpomalit nebo selhat. Pokud se pokoušíte restartovat aktivní hlavní uzel, všechny spuštěné úlohy budou ukončeny a dokud nebudou služby znovu spuštěny, nebudete moci odesílat úlohy do clusteru. V případě potřeby byste měli zvážit restartování virtuálních počítačů. Tady je několik pokynů, kdy je potřeba zvážit restartování virtuálních počítačů.

- Do uzlu nemůžete použít SSH, ale reaguje na příkazy příkazového testu.
- Pracovní uzel nefunguje bez prezenčního signálu v uživatelském rozhraní Ambari.
- Dočasný disk je plný na uzlu.
- Tabulka procesu na virtuálním počítači obsahuje mnoho položek, ve kterých se proces dokončil, ale je uvedený v seznamu "ukončený stav".

> [!WARNING]  
> Při restartu virtuálních počítačů pro **adaptéry HBA** a **Kafka** clustes byste měli být opatrní, protože může dojít ke ztrátě dat.

## <a name="use-powershell-to-reboot-vms"></a>Použití PowerShellu k restartování virtuálních počítačů

K použití operace restartování uzlu se vyžadují dva kroky: seznam uzlů a restartování uzlů.

1. Seznam uzlů. Seznam uzlů clusteru můžete získat prostřednictvím [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Restartujte hostitele. Po získání názvů uzlů, které chcete restartovat, restartujte uzly pomocí [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Použití REST API k restartování virtuálních počítačů

Pomocí funkce **vyzkoušet** v dokumentu rozhraní API můžete odesílat požadavky do HDInsight. K použití operace restartování uzlu se vyžadují dva kroky: seznam uzlů a restartování uzlů.

1. Seznam uzlů. Seznam uzlů clusteru můžete získat z REST API nebo v Ambari. Další podrobnosti najdete v [seznamu HDInsight hosts REST API operace](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Restartujte hostitele. Po získání názvů uzlů, které chcete restartovat, použijte restart Nodes REST API k restartování uzlů. Název uzlu se řídí vzorem **"NodeType (dolů/HN/ZK/GS)" + "x" + "prvních 6 znaků názvu clusteru"**. Další podrobnosti najdete v případě [restartování hostitelů v HDInsight REST API operace](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Skutečné názvy uzlů, které chcete restartovat, jsou uvedeny v poli JSON v textu požadavku.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Další kroky

* [Restart – AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Virtuální počítače HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [REST API HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)
