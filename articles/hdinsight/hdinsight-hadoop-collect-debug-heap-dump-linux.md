---
title: Povolení výpisů paměti haldy pro služby Hadoop v HDInsight – Azure
description: Povolení výpisů paměti haldy pro služby Hadoop v clusterech HDInsight založených na Linuxu pro ladění a analýzu.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: e96cda8560d6fffa3475e7b3130ebc5954548eac
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592886"
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Povolení výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisů paměti haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v době, kdy byl vytvořen výpis paměti. Takže se hodí pro diagnostiku problémů, ke kterým dochází za běhu.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight, které používají systém Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Služby

Můžete povolit výpisů paměti haldy pro následující služby:

* **hcatalog** -tempelton
* **Hive** -hiveserver2, metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** – správce prostředků, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

Můžete také povolení výpisů paměti haldy pro mapy a snížit procesy spuštěné prostřednictvím HDInsight.

## <a name="configuration"></a>Principy konfigurace výpisu paměti haldy

Předáním možnosti jsou povolené výpisů paměti haldy (někdy označované jako požádá o, nebo parametry) na JVM při spuštění služby. Pro většinu služby Hadoop můžete upravit skript prostředí, používá ke spuštění služby k předání těchto možností.

Do každého skriptu se export pro  **\* \_OPTS**, který obsahuje možnosti předané JVM. Například v **hadoop env.sh** řádek, který začíná skriptu `export HADOOP_NAMENODE_OPTS=` obsahuje možnosti pro službu NameNode.

Mapovací a redukční procesy se mírně liší, jak tyto operace jsou podřízený proces služby MapReduce. Každý mapování nebo snižte proces běží v kontejneru podřízené a existují dvě položky, které obsahují možnosti JVM. Obě součástí **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Doporučujeme použít Ambari upravit skripty a nastavení mapred-site.xml, jako popisovač Ambari replikace změn napříč uzly v clusteru. Zobrazit [pomocí Ambari](#using-ambari) najdete konkrétní kroky.

### <a name="enable-heap-dumps"></a>Povolení výpisů paměti haldy

Tato možnost umožňuje výpisů paměti haldy, dojde OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** Označuje, že tato možnost je povolená. Ve výchozím nastavení je tato možnost zakázána.

> [!WARNING]
> Jako soubory s výpisem paměti mohou být velké výpisů paměti haldy nejsou povolené pro služby Hadoop v HDInsight. Pokud je povolit pro odstraňování potíží, nezapomeňte po reprodukovat problém a shromážděné soubory s výpisem paměti je zakázat.

### <a name="dump-location"></a>Umístění s výpisem paměti

Výchozí umístění pro soubor s výpisem paměti je aktuální pracovní adresář. Můžete určit, kde je soubor uložen pomocí následující možnosti:

    -XX:HeapDumpPath=/path

Například použití `-XX:HeapDumpPath=/tmp` způsobí, že se výpisy paměti, který bude uložen v adresáři TMP.

### <a name="scripts"></a>Scripts

Můžete také spustit skript po **OutOfMemoryError** vyvolá. Například aktivuje oznámení, abyste věděli, že došlo k chybě. Použijte možnost pro spuštění skriptu na __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Hadoop je distribuovaný systém, libovolný skript, které musí být umístěn na všech uzlech v clusteru, který služba běží na.
> 
> Skript musí také být v umístění, které je přístupný pro účet služby spouští jako a musíte zadat spustit oprávnění. Například můžete chtít uložit skripty v `/usr/local/bin` a použít `chmod go+rx /usr/local/bin/filename.sh` pro čtení a oprávnění ke spouštění.

## <a name="using-ambari"></a>Pomocí nástroje Ambari

Pokud chcete upravit konfiguraci pro službu, postupujte následovně:

1. Otevřete webové uživatelské rozhraní Ambari pro váš cluster. Adresa URL je https://YOURCLUSTERNAME.azurehdinsight.net.

    Po zobrazení výzvy ověřování k webu pomocí názvu účtu HTTP (výchozí: správce) a heslo pro váš cluster.

   > [!NOTE]
   > Můžete být vyzváni podruhé pomocí Ambari pro uživatelské jméno a heslo. Pokud ano, zadejte stejný název účtu a heslo

2. Pomocí seznamu na levé straně vyberte oblast služeb, které chcete upravit. Například **HDFS**. V oblasti System center, vyberte **Configs** kartu.

    ![Obrázek Ambari web s vybranou kartou HDFS Configs](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Použití **filtr...**  položku, zadejte **požádá o**. Se zobrazují pouze položky obsahující tento text.

    ![Filtrovaný seznam](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Najít  **\* \_OPTS** záznam pro službu, kterou chcete povolení výpisů paměti haldy pro a přidat tyto možnosti, které si přejete povolit. Na následujícím obrázku jsme přidali `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` k **HADOOP\_NAMENODE\_OPTS** položky:

    ![HADOOP_NAMENODE_OPTS s - XX: + HeapDumpOnOutOfMemoryError - XX: = HeapDumpPath/tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Při povolení haldy vypíše mapy nebo snižte podřízený proces, hledejte pro pole s názvem **mapreduce.admin.map.child.java.opts** a **mapreduce.admin.reduce.child.java.opts**.

    Použití **Uložit** tlačítko Uložit změny. Můžete zadat krátký poznámka popisující změny.

5. Jakmile změny se použily, **vyžaduje restartování** Ikona vedle jednu nebo víc služeb.

    ![Restartujte požadované ikonu a restartujte tlačítko](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Vyberte každou službu, která potřebuje restartovat počítač a použijte **akce služby** tlačítko **zapnout v režimu údržby**. Režim údržby zabraňuje výstrahy při opětovném generování ze služby.

    ![Zapnout nabídky režim údržby](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Po povolení režimu údržby použít **restartovat** tlačítko pro službu **restartujte všechny to týká**

    ![Restartujte všechny ovlivněné položky](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > položky **restartovat** tlačítko se může lišit pro ostatní služby.

8. Po restartování služby použijte **akce služby** tlačítko **zapnout vypnout režim údržby**. Chcete-li pokračovat v monitorování výstrah pro službu tento Ambari.

