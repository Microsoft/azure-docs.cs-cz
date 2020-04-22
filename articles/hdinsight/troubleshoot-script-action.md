---
title: Poradce při potížích s akcemi skriptů v Azure HDInsightu
description: Obecné kroky pro řešení potíží s akcemi skriptů v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771973"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Poradce při potížích s akcemi skriptů v Azure HDInsightu

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="viewing-logs"></a>Zobrazení protokolů

Webové uživatelské prostředí Apache Ambari můžete použít k zobrazení informací zaznamenaných pomocí akcí skriptu. Pokud se skript během vytváření clusteru nezdaří, protokoly jsou ve výchozím účtu úložiště clusteru. Tato část obsahuje informace o tom, jak načíst protokoly pomocí obou těchto možností.

### <a name="apache-ambari-web-ui"></a>Webové uživatelské uzly Apache Ambari

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Na panelu v horní části stránky vyberte položku **ops.** Seznam zobrazuje aktuální a předchozí operace provedené v clusteru prostřednictvím ambari.

    ![Webový panel uživatelského uživatelského nastavení Ambari s vybranými ops](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Vyhledejte položky, které **mají\_spustit vlastní akci ve** sloupci **Operace.** Tyto položky jsou vytvořeny při spuštění akcí skriptu.

    ![Operace akce skriptu Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Chcete-li zobrazit výstup **STDOUT** a **STDERR,** vyberte položku **run\customscriptaction** a projděte odkazy. Tento výstup je generován při spuštění skriptu a může mít užitečné informace.

### <a name="default-storage-account"></a>Výchozí účet úložiště

Pokud se vytvoření clusteru nezdaří z důvodu chyby skriptu, protokoly jsou uloženy v účtu úložiště clusteru.

* Protokoly úložiště jsou `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`k dispozici na adrese .

    ![Protokoly akcí skriptu](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Pod tímto adresářem jsou protokoly uspořádány samostatně pro **uzel headnode**, **uzel pracovníka**a **uzel zookeeper**. Podívejte se na následující příklady:

    * **Čelnody**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Pracovní uzel**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper uzel**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Všechny **stdout** a **stderr** odpovídajícího hostitele se nahrají do účtu úložiště. Pro každou akci skriptu existuje jeden **výstup\*.txt** a **errors.\*** Soubor **output-*.txt** obsahuje informace o identifikátoru URI skriptu, který byl spuštěn na hostiteli. Následující text je příkladem těchto informací:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Je možné, že opakovaně vytvoříte cluster akcí skriptů se stejným názvem. V takovém případě můžete rozlišit příslušné protokoly na základě názvu složky **DATE.** Například struktura složek pro **cluster, mycluster**, vytvořená v různých datech, se například podobá následujícím položkám protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Pokud vytvoříte cluster akcí skriptu se stejným názvem ve stejný den, můžete použít jedinečnou předponu k identifikaci příslušných souborů protokolu.

* Pokud vytvoříte cluster u půlnoci 12:00, je možné, že soubory protokolu se rozprostírají po dvou dnech. V takovém případě se zobrazí dvě různé složky kalendářních dat pro stejný cluster.

* Nahrávání souborů protokolu do výchozího kontejneru může trvat až pět minut, zejména u velkých clusterů. Pokud tedy chcete získat přístup k protokolům, neměli byste cluster okamžitě odstranit, pokud se akce skriptu nezdaří.

## <a name="ambari-watchdog"></a>Ambari hlídací pes

Neměňte heslo pro hlídacího psa Ambari, hdinsightwatchdog, ve vašem clusteru HDInsight založeném na Linuxu. Změna hesla přeruší možnost spouštět nové akce skriptu v clusteru HDInsight.

## <a name="cant-import-name-blobservice"></a>Nelze importovat název BlobService

__Příznaky__. Akce skriptu se nezdaří. Při zobrazení operace v Ambari se zobrazí text podobný následující chybě:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Příčina__. K této chybě dochází, pokud upgradujete klienta úložiště Python Azure Storage, který je součástí clusteru HDInsight. HDInsight očekává klienta Azure Storage 0.20.0.

__Rozlišení__. Chcete-li tuto chybu vyřešit, ručně se `ssh`připojte ke každému uzlu clusteru pomocí aplikace . Chcete-li přeinstalovat správnou verzi klienta úložiště, spusťte následující příkaz:

```bash
sudo pip install azure-storage==0.20.0
```

Informace o připojení ke clusteru pomocí SSH najdete v [tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historie nezobrazuje skripty použité při vytváření clusteru

Pokud byl cluster vytvořen před 15. Změna velikosti clusteru způsobí, že se skripty zobrazí v historii akcí skriptu.

Existují dvě výjimky:

* Váš cluster byl vytvořen před 1 září 2015. Toto datum je, kdy byly zavedeny akce skriptu. Žádný cluster vytvořený před tímto datem nemohl použít akce skriptu pro vytvoření clusteru.

* Při vytváření clusteru jste použili více akcí skriptu. Nebo jste použili stejný název pro více skriptů nebo stejný název, stejný identifikátor URI, ale různé parametry pro více skriptů. V těchto případech se zobrazí následující chyba:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).