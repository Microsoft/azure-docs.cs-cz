---
title: Chyba Jupyter 404-"blokování rozhraní API pro více zdrojů" – Azure HDInsight
description: Jupyter Server 404 "nenalezen" v důsledku "blokování rozhraní API pro více zdrojů" ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f2000c3938e7366dd85f36b8de9a8425e91fab8a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287938"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scénář: Jupyter Server 404 "nenalezen" v důsledku "blokování rozhraní API pro více zdrojů" ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při přístupu ke službě Jupyter v HDInsight se zobrazí chybové pole "Nenalezeno". Pokud provedete kontrolu protokolů Jupyter, zobrazí se něco podobného:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

V protokolu Jupyter se taky může zobrazit IP adresa v poli "původ".

## <a name="cause"></a>Příčina

Tato chyba může být způsobena několika způsoby:

- Pokud jste nakonfigurovali pravidla skupiny zabezpečení sítě (NSG), která budou omezovat přístup ke clusteru. Omezení přístupu pomocí pravidel NSG vám pořád umožní přímý přístup k Apache Ambari a dalším službám s použitím IP adresy místo názvu clusteru. Při přístupu k Jupyter se ale může zobrazit chyba 404 "Nenalezeno".

- Pokud jste vašemu bráně HDInsight přizpůsobili jiný název DNS než standard `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Řešení

1. Upravte soubory jupyter.py na těchto dvou místech:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Vyhledejte řádek, který říká: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` a změňte jej na: `NotebookApp.allow_origin='\"*\"'` .

1. Restartujte službu Jupyter z Ambari.

1. Zadáním `ps aux | grep jupyter` na příkazovém řádku by se mělo ukázat, že umožňuje připojení libovolné adresy URL.

Toto je méně bezpečné než nastavení, které jsme už nastavili. Ale předpokládá se, že přístup ke clusteru je omezený a že jeden z nich se může připojit ke clusteru, protože jsme NSGi na místě.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]