---
title: Použití Apache Phoenix a SQLLine s HBase v Azure HDInsight | Microsoft Docs
description: Další informace o použití nástrojů Apache Phoenix v HDInsight. Také zjistěte, jak nainstalovat a nastavit SQLLine ve vašem počítači pro připojení ke clusteru HBase v HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 64700567b8acf816f42e6bf8cdc5386b6c65fe3f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Použití nástrojů Apache Phoenix s clustery se systémem Linux HBase v HDInsight
Další informace o použití [Apache Phoenix](http://phoenix.apache.org/) v Azure HDInsight a jak používat SQLLine. Další informace o Phoenix najdete v tématu [Phoenix za 15 minut nebo méně](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Gramatika Phoenix, najdete v části [Phoenix gramatika](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix verze informace o HDInsight naleznete v tématu [co je nového ve verzích clusterů systému Hadoop poskytovaných v HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Použití SQLLine
[SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku k provedení SQL.

### <a name="prerequisites"></a>Požadavky
Než budete moci použít SQLLine, musíte mít následující položky:

* **Cluster HBase v HDInsight**. Chcete-li vytvořit, přečtěte si téma [začít pracovat s Apache HBase v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Jakmile se připojíte ke clusteru HBase, budete muset připojit k ZooKeeper virtuálních počítačů. Každý cluster HDInsight má tři ZooKeeper virtuálních počítačů.

**Chcete-li získat název hostitele ZooKeeper**

1. Otevření Ambari procházením **https://\<název clusteru\>. azurehdinsight.net**.
2. Pokud chcete přihlásit, zadejte uživatelské jméno protokolu HTTP (cluster) a heslo.
3. V nabídce vlevo vyberte **ZooKeeper**. Tři **ZooKeeper Server** jsou uvedeny instance.
4. Vyberte jednu z **ZooKeeper Server** instance. Na **Souhrn** podokně najít **název hostitele**. Podobná *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Chcete-li použít SQLLine**

1. Připojte se ke clusteru pomocí protokolu SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. V SSH použijte následující příkazy ke spuštění SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. K vytvoření tabulky HBase a vložte některá data, spusťte následující příkazy:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Další informace najdete v tématu [SQLLine ruční](http://sqlline.sourceforge.net/#manual) a [Phoenix gramatika](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat Apache Phoenix v HDInsight. Další informace najdete v těchto článcích:

* [Přehled HDInsight HBase][hdinsight-hbase-overview].
  HBase je databáze NoSQL open source Apache postavená na Hadoop poskytující náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat.
* [Zřídit clustery HBase v Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Integrace virtuální sítě může být nasazena clustery HBase na stejné virtuální síti jako vaše aplikace, takže aplikace mohou komunikovat přímo s HBase.
* [Konfigurace replikace HBase v HDInsight](apache-hbase-replication.md). Zjistěte, jak vytvořit HBase replikace mezi dvěma datových centrech Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


