---
title: Povolit výpisů paměti haldy pro služby Hadoop v HDInsight - Azure | Microsoft Docs
description: Povolte výpisů paměti haldy pro služby Hadoop z clusterů HDInsight se systémem Linux pro ladění a analýzu.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: cd906736f2642d764c2b72a0572f63d675613c81
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Povolit výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisy haldy obsahovat snímek paměti aplikace, včetně hodnot proměnných v době, kdy byla vytvořena výpis. Takže se hodí pro diagnostiku problémů, ke kterým dochází za běhu.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight, které používají systém Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Služby

Můžete povolit výpisů paměti haldy pro následující služby:

* **hcatalog** -tempelton
* **Hive** -hiveserver2, metaúložiště, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode, secondarynamenode, namenode

Můžete také povolit výpisů paměti haldy mapy a snížit procesy spuštěné prostřednictvím HDInsight.

## <a name="configuration"></a>Principy haldy výpis konfigurace

Výpisy haldy jsou povolené předáním možnosti (někdy označuje jako požádá, nebo parametry) na JVM při spuštění služby. Pro většinu služby Hadoop můžete upravit skript prostředí používá ke spuštění služby předávání tyto možnosti.

Do každého skriptu, je exportu pro  **\* \_OPTS**, který obsahuje možnosti, které předaný systém JVM. Například v **hadoop env.sh** skriptu, řádek, který začíná `export HADOOP_NAMENODE_OPTS=` obsahuje možnosti pro službu NameNode.

Mapování a snížit procesy jsou mírně liší, jsou tyto operace podřízený proces služby MapReduce. Každý mapování, nebo snižte proces běží v kontejneru podřízené a existují dvě položky, které obsahují možnosti JVM. Obě obsažené v **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Doporučujeme pomocí nástroje Ambari k úpravě skripty a mapred-site.xml nastavení, jako popisovač Ambari replikace změn mezi uzly v clusteru. Najdete v článku [pomocí Ambari](#using-ambari) konkrétní postup.

### <a name="enable-heap-dumps"></a>Povolení výpisů paměti haldy

Tato možnost umožňuje haldy výpisy, když dojde OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** Označuje, že tato možnost je povolená. Ve výchozím nastavení je tato možnost zakázána.

> [!WARNING]
> Jako souborů výpisu paměti může být velký haldy výpisy nejsou povolené pro služby Hadoop v HDInsight. Pokud povolíte je pro řešení potíží, nezapomeňte po reprodukovat problému a shromáždění souborů výpisu paměti je zakázat.

### <a name="dump-location"></a>Výpis umístění

Výchozí umístění pro soubor výpisu je aktuální pracovní adresář. Můžete řídit tam, kde je soubor uložený pomocí následující možnosti:

    -XX:HeapDumpPath=/path

Například pomocí `-XX:HeapDumpPath=/tmp` způsobí, že výpisy má být uložen v adresáři TMP.

### <a name="scripts"></a>Scripts

Můžete také spustit skript při **OutOfMemoryError** dojde. Například spouštěcí oznámení, abyste věděli, že došlo k chybě. Použijte možnost pro spuštění skriptu na __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Vzhledem k tomu, že Hadoop je distribuovaný systém, musí být umístěny všech skriptů používá na všech uzlech v clusteru, který spouští službu na.
> 
> Skript musí také být v umístění, které je přístupný pro účet služby spouští jako a zadejte oprávnění ke spouštění. Například můžete chtít uložit skripty v `/usr/local/bin` a používat `chmod go+rx /usr/local/bin/filename.sh` oprávnění ke spouštění a udělte pro čtení.

## <a name="using-ambari"></a>Pomocí nástroje Ambari

Pokud chcete upravit konfiguraci pro službu, použijte následující kroky:

1. Otevřete webovému uživatelskému rozhraní Ambari pro váš cluster. Adresa URL je https://YOURCLUSTERNAME.azurehdinsight.net.

    Po zobrazení výzvy ověřování k webu pomocí názvu účtu HTTP (výchozí: správce) a heslo pro váš cluster.

   > [!NOTE]
   > Můžete být vyzváni podruhé pomocí Ambari pro uživatelské jméno a heslo. Pokud ano, zadejte stejný název účtu a heslo

2. Pomocí seznamu na levé straně vyberte oblast služby, kterou chcete upravit. Například **HDFS**. V oblasti center, vyberte **konfigurací** kartě.

    ![Obrázek Ambari web s vybranou kartou HDFS konfigurací](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Pomocí **filtru...**  položku, zadejte **požádá**. Zobrazí se pouze položky obsahující tento text.

    ![Filtrovaný seznam](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Najít  **\* \_OPTS** záznam pro službu, kterou chcete povolit výpisů paměti haldy pro a přidání možnosti, které chcete povolit. Na následujícím obrázku, byly přidány `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` k **HADOOP\_NAMENODE\_OPTS** položku:

    ![HADOOP_NAMENODE_OPTS s - XX: + HeapDumpOnOutOfMemoryError - XX: = HeapDumpPath/tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Při povolení haldy výpisy mapy nebo snižte podřízeného procesu, vyhledejte pole s názvem **mapreduce.admin.map.child.java.opts** a **mapreduce.admin.reduce.child.java.opts**.

    Použití **Uložit** tlačítko a uložte změny. Můžete zadat krátký poznámka popisující změny.

5. Po použití změny **je vyžadován Restart** zobrazí ikona vedle jednu nebo více služeb.

    ![Restartujte vyžaduje ikonu a restartujte tlačítko](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Vyberte každá služba, která vyžaduje restartování a pomocí **služby akce** tlačítko pro **zapnout v režimu údržby**. Režim údržby zabrání výstrahy generován ze služby, pokud byste ji restartovat.

    ![Zapnout nabídky režim údržby](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Po povolení režimu údržby pomocí **restartujte** tlačítko pro službu, kterou chcete **restartujte všechny uskutečněn**

    ![Restartujte všechny ovlivněné položky](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > položky pro **restartujte** tlačítko se může lišit pro jiné služby.

8. Až po restartování služby, pomocí **služby akce** tlačítko pro **zapnout vypnout režimu údržby**. Tato Ambari obnovení monitorování pro výstrahy pro službu.

