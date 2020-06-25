---
title: Restartování virtuálních počítačů pro cluster Azure HDInsight
description: Přečtěte si, jak restartovat nereagující virtuální počítače pro cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 46efccad60c06c73145bbf30c119f6a47cb856d5
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323441"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Restartování virtuálních počítačů pro cluster HDInsight

Clustery HDInsight obsahují skupiny virtuálních počítačů jako uzly clusteru. U dlouho spuštěných clusterů můžou tyto uzly po různých důvodech přestat reagovat. Tento článek popisuje, jak restartovat nereagující virtuální počítače v clusteru HDInsight.

## <a name="when-to-reboot"></a>Kdy restartovat

Restartování virtuálních počítačů v clusteru přináší výpadky uzlu a restartování služeb na uzlu. I když se uzel restartuje, může se stát, že cluster přestane být v pořádku, úlohy se můžou zpomalit nebo selhat. Pokud se pokoušíte restartovat aktivní hlavní uzel, všechny spuštěné úlohy budou ukončeny a dokud nebudou služby znovu spuštěny, nebudete moci odesílat úlohy do clusteru. V případě potřeby byste měli zvážit restartování virtuálních počítačů. Tady je několik pokynů, kdy je potřeba zvážit restartování virtuálních počítačů.

- Do uzlu nemůžete použít SSH, ale reaguje na příkazy příkazového testu.
- Pracovní uzel nefunguje bez prezenčního signálu v uživatelském rozhraní Ambari.
- Dočasný disk je plný na uzlu.
- Tabulka procesu na virtuálním počítači obsahuje mnoho položek, ve kterých se proces dokončil, ale je uvedený jako stav ukončeno.

## <a name="use-rest-api-to-reboot-vms"></a>Použití REST API k restartování virtuálních počítačů

Restartování uzlu je podporováno pouze prostřednictvím REST API aktuálně. Pomocí funkce **vyzkoušet** v dokumentu rozhraní API můžete odesílat požadavky do HDInsight. K použití operace restartování uzlu se vyžadují dva kroky: seznam uzlů a restartování uzlů.

1. Seznam uzlů. Seznam uzlů clusteru můžete získat z REST API nebo v Ambari. Další podrobnosti najdete v [seznamu HDInsight hostitelé REST API operace](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts) .

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Restartujte hostitele. Po získání názvů uzlů, které chcete restartovat, použijte restart hostitelů REST API k restartování uzlů. Další podrobnosti najdete v [REST API operaci v hostitelích HDInsight pro restartování](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts) .

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Skutečné názvy uzlů, které chcete restartovat, jsou uvedeny v poli JSON v textu požadavku.

```json
[
  "gateway1",
  "gateway3"
]
```

## <a name="next-steps"></a>Další kroky

* [Virtuální počítače HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [REST API HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)