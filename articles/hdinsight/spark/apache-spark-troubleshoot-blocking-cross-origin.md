---
title: Chyba Jupyter 404 – "Blokování rozhraní API pro cross Origin" – Azure HDInsight
description: Chyba Jupyter server 404 "Nebyl nalezen" kvůli "Blokování rozhraní API pro cross Origin" v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894414"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scénář: Chyba Jupyter server 404 "Nebyl nalezen" kvůli "Blokování rozhraní API pro cross Origin" v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při přístupu ke službě Jupyter na HDInsight se zobrazí chybové pole s nápisem "Nebyl nalezen". Pokud zkontrolujete Jupyter protokoly, uvidíte něco takového:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

V poli Původ se také může zobrazit IP adresa v protokolu Jupyter.

## <a name="cause"></a>Příčina

Tato chyba může být způsobena několika věcmi:

- Pokud jste nakonfigurovali pravidla skupiny zabezpečení sítě (NSG), aby se omezil přístup ke clusteru. Omezení přístupu pomocí pravidel skupiny nsg vám stále umožní přímý přístup k Apache Ambari a dalším službám pomocí IP adresy, nikoli názvu clusteru. Při přístupu k Jupyteru se však může zobrazit chyba 404 "Nebylnalezen".

- Pokud jste své bráně HDInsight udělili jiný než `xxx.azurehdinsight.net`standardní název DNS.

## <a name="resolution"></a>Řešení

1. Upravte jupyter.py soubory na těchto dvou místech:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Najít řádek, který `NotebookApp.allow_origin='\"https://{2}.{3}\"'` říká: A `NotebookApp.allow_origin='\"*\"'`změnit ji na: .

1. Restartujte službu Jupyter z Ambari.

1. Zadáním `ps aux | grep jupyter` příkazového řádku by mělo být zřejmé, že umožňuje připojení libovolné adresy URL.

To je méně bezpečné, než nastavení jsme již měli na místě. Ale předpokládá se, že přístup ke clusteru je omezen a že jeden zvenčí je povoleno připojení ke clusteru, protože máme nsg na místě.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
