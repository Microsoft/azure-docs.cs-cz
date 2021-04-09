---
title: Restartování virtuálních počítačů pro clustery Azure HDInsight
description: Přečtěte si, jak restartovat nereagující virtuální počítače pro clustery Azure HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: a61735dba60860459d007eb54d4655f41d5ae51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946864"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Restartování virtuálních počítačů pro clustery HDInsight

Clustery Azure HDInsight obsahují skupiny virtuálních počítačů (VM) jako uzly clusteru. Pro dlouhotrvající clustery můžou tyto uzly z různých důvodů přestat reagovat. Tento článek popisuje, jak restartovat nereagující virtuální počítače v clusteru HDInsight.

## <a name="when-to-reboot"></a>Kdy restartovat

> [!WARNING]
> Když restartujete virtuální počítače v clusteru, uzel nebude k dispozici a služby musí být v uzlu restartovány.

Když se uzel restartuje, může se stát, že cluster přestane být v pořádku a úlohy se můžou zpomalit nebo selhat. Pokud se pokoušíte restartovat aktivní hlavní uzel, všechny spuštěné úlohy se zastaví. Do clusteru nebudete moci odesílat úlohy, dokud nebudou služby znovu spuštěné a znovu spuštěny. Z těchto důvodů byste měli virtuální počítače restartovat pouze v případě potřeby. Zvažte restartování virtuálních počítačů v těchto případech:

- Pomocí SSH se nelze dostat do uzlu, ale reaguje na příkazy příkazového testu.
- Pracovní uzel nefunguje bez prezenčního signálu v uživatelském rozhraní Ambari.
- Dočasný disk je plný na uzlu.
- Tabulka procesu na virtuálním počítači obsahuje mnoho položek, ve kterých se proces dokončil, ale je uvedený jako stav ukončeno.

> [!NOTE]
> Restartování virtuálních počítačů se u clusterů **HBA** a **Kafka** nepodporuje, protože restartování může způsobit ztrátu dat.

## <a name="use-powershell-to-reboot-vms"></a>Použití PowerShellu k restartování virtuálních počítačů

Pro použití operace restartování uzlu jsou vyžadovány dva kroky: seznam uzlů a restartování uzlů.

1. Seznam uzlů. Seznam uzlů clusteru můžete získat na adrese [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost).

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Restartujte hostitele. Po získání názvů uzlů, které chcete restartovat, restartujte uzly pomocí [restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost).

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Použití REST API k restartování virtuálních počítačů

Pomocí funkce **vyzkoušet** v dokumentu rozhraní API můžete odesílat požadavky do HDInsight. Pro použití operace restartování uzlu jsou vyžadovány dva kroky: seznam uzlů a restartování uzlů.

1. Seznam uzlů. Seznam uzlů clusteru můžete získat z REST API nebo v Ambari. Další informace naleznete v tématu [seznam HDInsight hosts REST API operace](/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Restartujte hostitele. Po získání názvů uzlů, které chcete restartovat, restartujte uzly pomocí REST API k restartování uzlů. Název uzlu se řídí vzorem *NodeType (dolů/HN/ZK/GS)*  +  *×*  +  *prvních šest znaků názvu clusteru*. Další informace najdete v tématu věnovaném [restartování hostitelů služby HDInsight REST API operace](/rest/api/hdinsight/virtualmachines/restarthosts).

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

* [Restart – AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Virtuální počítače HDInsight REST API](/rest/api/hdinsight/virtualmachines)
* [REST API HDInsight](/rest/api/hdinsight/)