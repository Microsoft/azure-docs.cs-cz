---
title: Použití Apache Phoenixu a SQLLine s HBase v Azure HDInsight
description: Další informace o použití Apache Phoenixu v HDInsight. Také zjistěte, jak nainstalovat a nastavit SQLLine ve vašem počítači pro připojení ke clusteru služby HBase v HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jasonh
ms.openlocfilehash: 349f1680cf754a44e2e4217ebde9d0d60479ebcf
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597459"
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Použití Apache Phoenixu s clustery se systémem Linux HBase v HDInsight
Další informace o použití [Apache Phoenix](http://phoenix.apache.org/) v Azure HDInsight a jak používat SQLLine. Další informace o Phoenix, naleznete v tématu [Phoenix za 15 minut nebo i rychleji](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Gramatika Phoenix, naleznete v tématu [Phoenix gramatiky](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix verze informace o HDInsight najdete v tématu [co je nového ve verzích clusterů Hadoop poskytovaných službou HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Použití SQLLine
[SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku ke spuštění SQL.

### <a name="prerequisites"></a>Požadavky
Než budete moct použít SQLLine, musíte mít následující položky:

* **Cluster HBase v HDInsight**. Pokud chcete jeden vytvořit, přečtěte si téma [Začínáme s Apache HBase v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Když se připojíte ke clusteru služby HBase, musíte připojit k jednomu z virtuálních počítačů ZooKeeper. Každý cluster HDInsight má tři virtuální počítače ZooKeeper.

**Chcete-li získat název hostitele ZooKeeper**

1. Otevření Ambari procházením **https://\<název clusteru\>. azurehdinsight.net**.
2. Pro přihlášení, zadejte protokol HTTP (clusteru) uživatelského jména a hesla.
3. V nabídce vlevo vyberte **ZooKeeper**. Tři **ZooKeeper Server** instance patří.
4. Vyberte jednu z **ZooKeeper Server** instancí. Na **Souhrn** podokně vyhledejte **název hostitele**. Vypadá podobně jako *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Chcete-li použít SQLLine**

1. Připojení ke clusteru pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. V SSH použijte následující příkazy ke spuštění SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. K vytvoření tabulky HBase a vložit nějaká data, spusťte následující příkazy:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Další informace najdete v tématu [SQLLine ruční](http://sqlline.sourceforge.net/#manual) a [Phoenix gramatiky](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat Apache Phoenix v HDInsight. Další informace najdete v těchto článcích:

* [Přehled HDInsight HBase][hdinsight-hbase-overview].
  HBase je databáze NoSQL open source Apache postavená na Hadoop poskytující náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat.
* [Zřizování clusterů HBase ve službě Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Integrace virtuální sítě je možné nasadit clustery HBase používané ke stejné virtuální síti jako vaše aplikace, takže aplikace můžou komunikovat přímo s HBase.
* [Konfigurace replikace HBase v HDInsight](apache-hbase-replication.md). Zjistěte, jak nastavit replikace HBase ve dvou datacentrech Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


