---
title: Chyba Jupyter 404-"blokování rozhraní API pro více zdrojů" – Azure HDInsight
description: Jupyter Server 404 "nenalezen" v důsledku "blokování rozhraní API pro více zdrojů" ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 472dde22e5df32e7d2a276b0515a0f1edafe52b3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545663"
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

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).