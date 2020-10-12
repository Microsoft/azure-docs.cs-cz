---
title: Řešení potíží s Apache Oozie v Azure HDInsight
description: Řešení určitých chyb Apache Oozie v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: fb795a9d7100019b2b1820c592f87025b77f5878
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045854"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Řešení potíží s Apache Oozie v Azure HDInsight

Pomocí uživatelského rozhraní Apache Oozie můžete zobrazit protokoly Oozie. Uživatelské rozhraní Oozie obsahuje také odkazy na protokoly JobTracker pro úlohy MapReduce, které spustila pracovní postup. Vzor pro řešení potíží by měl být následující:

1. Zobrazte úlohu ve webovém uživatelském rozhraní Oozie.

2. Pokud dojde k chybě nebo selhání konkrétní akce, vyberte akci a ověřte, zda pole **chybová zpráva** obsahuje další informace o selhání.

3. Pokud je k dispozici, použijte adresu URL z akce, chcete-li zobrazit další podrobnosti, například protokoly JobTracker, pro akci.

Níže jsou uvedené konkrétní chyby, se kterými se můžete setkat, a jejich řešení.

## <a name="ja009-cant-initialize-cluster"></a>JA009: nejde inicializovat cluster.

### <a name="issue"></a>Problém

Stav úlohy se změní na **pozastaveno**. Podrobnosti úlohy zobrazují `RunHiveScript` stav **START_MANUAL**. Výběrem této akce se zobrazí následující chybová zpráva:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Příčina

Adresy úložiště objektů BLOB v Azure, které se používají v souboru **job.xml** , neobsahují kontejner úložiště nebo název účtu úložiště. Formát adresy úložiště BLOB musí být `wasbs://containername@storageaccountname.blob.core.windows.net` .

### <a name="resolution"></a>Řešení

Změňte adresu úložiště objektů blob, kterou úloha používá.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie nemůže zosobnit &lt; uživatele.&gt;

### <a name="issue"></a>Problém

Stav úlohy se změní na **pozastaveno**. Podrobnosti úlohy zobrazují `RunHiveScript` stav **START_MANUAL**. Pokud vyberete akci, zobrazí se tato chybová zpráva:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Příčina

Aktuální nastavení oprávnění nepovoluje Oozie zosobnit zadaný uživatelský účet.

### <a name="resolution"></a>Řešení

Oozie může zosobnit uživatele ve **`users`** skupině. Použijte `groups USERNAME` k zobrazení skupin, kterých je uživatelský účet členem. Pokud uživatel není členem **`users`** skupiny, přidejte uživatele do skupiny pomocí následujícího příkazu:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Může to trvat několik minut, než HDInsight rozpozná, že uživatel byl do skupiny přidaný.

---

## <a name="launcher-error-sqoop"></a>Chyba spouštěče (Sqoop)

### <a name="issue"></a>Problém

Stav úlohy se změní na **ukončeno**. Podrobnosti úlohy zobrazují `RunSqoopExport` stav **Chyba**. Pokud vyberete akci, zobrazí se tato chybová zpráva:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Příčina

Sqoop nemůže načíst ovladač databáze vyžadovaný pro přístup do databáze.

### <a name="resolution"></a>Řešení

Při použití Sqoop z úlohy Oozie je nutné zahrnout ovladač databáze s jinými prostředky, jako je například workflow.xml, úloha používá. V části workflow.xml také odkazovat na archiv, který obsahuje ovladač databáze `<sqoop>...</sqoop>` .

Například pro příklad úlohy [použijte pracovní postupy Hadoop Oozie](hdinsight-use-oozie-linux-mac.md)použijte následující postup:

1. Zkopírujte `mssql-jdbc-7.0.0.jre8.jar` soubor do adresáře **/tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Upravte `workflow.xml` a přidejte následující kód XML na nový řádek výše `</sqoop>` :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
