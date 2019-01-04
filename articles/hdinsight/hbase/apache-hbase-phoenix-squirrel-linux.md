---
title: Použití Apache Phoenixu a SQLLine s HBase v Azure HDInsight
description: Další informace o použití Apache Phoenixu v HDInsight. Také zjistěte, jak nainstalovat a nastavit SQLLine ve vašem počítači pro připojení ke clusteru služby HBase v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 51707baeba4a511d2766161afacd512a4338cf24
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653981"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Použití Apache Phoenixu s clustery založené na Linuxu Apache HBase v HDInsight
Další informace o použití [Apache Phoenix](https://phoenix.apache.org/) v Azure HDInsight a jak používat SQLLine. Další informace o Phoenix, naleznete v tématu [Apache Phoenix za 15 minut nebo i rychleji](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Gramatika Phoenix, naleznete v tématu [Apache Phoenix gramatiky](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Phoenix verze informace o HDInsight najdete v tématu [co je nového ve verzích clusterů systému Apache Hadoop poskytovaných službou HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Použití SQLLine
[SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku ke spuštění SQL.

### <a name="prerequisites"></a>Požadavky
Než budete moct použít SQLLine, musíte mít následující položky:

* **Clustery Apache HBase v HDInsight**. Pokud chcete jeden vytvořit, přečtěte si téma [Začínáme s Apache HBase v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Když se připojíte ke clusteru služby HBase, budete muset připojit k jednomu z [Apache ZooKeeper](https://zookeeper.apache.org/) virtuálních počítačů. Každý cluster HDInsight má tři virtuální počítače ZooKeeper.

**Chcete-li získat název hostitele ZooKeeper**

1. Otevřít [Apache Ambari](https://ambari.apache.org/) procházením **https://\<název clusteru\>. azurehdinsight.net**.
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

Další informace najdete v tématu [SQLLine ruční](http://sqlline.sourceforge.net/#manual) a [Apache Phoenix gramatiky](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat Apache Phoenix v HDInsight. Další informace najdete v těchto článcích:

* [Přehled HDInsight HBase][hdinsight-hbase-overview].
  Apache HBase je databáze NoSQL open source Apache postavená na Apache Hadoopu, která umožňuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat.
* [Apache HBase zřizování clusterů na Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Integrace virtuální sítě je možné nasadit clustery Apache HBase ke stejné virtuální síti jako vaše aplikace, takže aplikace můžou komunikovat přímo s HBase.
* [Konfigurace replikace Apache HBase v HDInsight](apache-hbase-replication.md). Zjistěte, jak nastavit replikaci Apache HBase ve dvou datacentrech Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


