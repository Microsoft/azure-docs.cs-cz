---
title: Řešení potíží se skripty v Azure HDInsight
description: Obecné kroky při řešení potíží pro akce skriptů v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 7158e9f82ee01b320d448baeab51fcfd122be00d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944690"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Řešení potíží se skripty v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="viewing-logs"></a>Zobrazení protokolů

Pomocí webového uživatelského rozhraní Apache Ambari můžete zobrazit informace zaznamenané akcemi skriptu. Pokud se skript během vytváření clusteru nezdařil, protokoly jsou ve výchozím účtu úložiště clusteru. V této části najdete informace o tom, jak protokoly načíst pomocí obou těchto možností.

### <a name="apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. Na panelu v horní části stránky vyberte položku **OPS** . V seznamu se zobrazí aktuální a předchozí operace prováděné na clusteru prostřednictvím Ambari.

    ![Panel webového uživatelského rozhraní Ambari s vybranou operací Operations](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Vyhledá položky, které mají ve sloupci **Operations** **\_ customscriptaction běžet** . Tyto položky jsou vytvořeny při spuštění akcí skriptu.

    ![Operace s akcemi skriptu Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Chcete-li zobrazit výstup **stdout** a **stderr** , vyberte položku **run\customscriptaction** a přejděte k podrobnostem prostřednictvím odkazů. Tento výstup se generuje při spuštění skriptu a může mít užitečné informace.

### <a name="default-storage-account"></a>Výchozí účet úložiště

Pokud se vytvoření clusteru nepovede kvůli chybě skriptu, protokoly se uchovávají v účtu úložiště clusteru.

* Protokoly úložiště jsou k dispozici na adrese `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` .

    ![Protokoly akcí skriptů](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    V rámci tohoto adresáře jsou protokoly uspořádány samostatně pro **hlavnímu uzlu**, **pracovní uzel** a **Zookeeper uzel**. Podívejte se na následující příklady:

    * **Hlavnímu uzlu**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Pracovní uzel**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Uzel Zookeeper**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Všechny **stdout** a **stderr** odpovídajícího hostitele se nahrají do účtu úložiště. Pro každou akci skriptu existuje jeden **výstup- \* . txt** a **Errors. \* txt** . Soubor **Output-*. txt** obsahuje informace o identifikátoru URI skriptu, který byl spuštěn na hostiteli. Následující text je příkladem těchto informací:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* Je možné opakovaně vytvořit cluster akcí skriptu se stejným názvem. V takovém případě můžete rozlišovat příslušné protokoly na základě názvu složky **data** . Například struktura složek pro cluster **mycluster** vytvořená v různých datech vypadá podobně jako v následujících záznamech protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Pokud vytvoříte cluster akcí skriptu se stejným názvem ve stejném dni, můžete k identifikaci relevantních souborů protokolu použít jedinečnou předponu.

* Pokud vytvoříte cluster poblíž 12:00. půlnoci, je možné, že soubory protokolu jsou rozloženy do dvou dnů. V takovém případě uvidíte dvě různé složky kalendářních dat pro stejný cluster.

* Nahrávání souborů protokolu do výchozího kontejneru může trvat až pět minut, zejména u velkých clusterů. Pokud tedy chcete získat přístup k protokolům, neměli byste okamžitě odstranit cluster, pokud dojde k chybě skriptu.

## <a name="ambari-watchdog"></a>Sledovací zařízení Ambari

Neměňte heslo pro sledovací zařízení Ambari, hdinsightwatchdog v clusteru HDInsight se systémem Linux. Změna hesla zruší možnost spouštění nových akcí skriptu v clusteru HDInsight.

## <a name="cant-import-name-blobservice"></a>Nelze importovat název BlobService

__Příznaky__. Akce skriptu se nezdařila. Při zobrazení operace v Ambari se zobrazí text podobný následující chybě:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Příčina__: K této chybě dojde, když upgradujete klienta Python Azure Storage, který je součástí clusteru HDInsight. HDInsight očekává Azure Storage 0.20.0 klienta.

__Řešení__. Chcete-li tuto chybu vyřešit, připojte se ručně k jednotlivým uzlům clusteru pomocí nástroje `ssh` . Spusťte následující příkaz, který znovu nainstaluje správnou verzi klienta úložiště:

```bash
sudo pip install azure-storage==0.20.0
```

Informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historie nezobrazuje skripty používané při vytváření clusteru.

Pokud byl cluster vytvořen před 15. března 2016, nemusí se položka zobrazit v historii akcí skriptu. Změna velikosti clusteru způsobí, že se skripty objeví v historii akcí skriptu.

Existují dvě výjimky:

* Cluster byl vytvořen před 1. září 2015. Toto datum je v případě, že byly zavedeny akce skriptu. Žádný cluster vytvořený před tímto datem nedokázal použít akce skriptu pro vytvoření clusteru.

* Během vytváření clusteru jste použili několik akcí skriptů. Nebo jste použili stejný název pro více skriptů nebo stejný název, stejný identifikátor URI, ale různé parametry pro více skriptů. V těchto případech se zobrazí následující chyba:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]