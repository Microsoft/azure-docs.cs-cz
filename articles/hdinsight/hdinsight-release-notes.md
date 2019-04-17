---
title: Zpráva k vydání verze pro Azure HDInsight
description: Nejnovější zpráva k vydání verze pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, Hive a další.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6ba9e77faeb297f9862b39384d397b478dc7cf36
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617729"
---
# <a name="release-notes-for-azure-hdinsight"></a>Zpráva k vydání verze pro Azure HDInsight

Tento článek obsahuje informace o **nejnovější** Azure HDInsight distribuovatelných oprav. Informace o předchozích vydaných verzích najdete v tématu [archivu poznámky k verzi HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [článku Správa verzí HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jedním z nejoblíbenějších služeb mezi podnikovými zákazníky pro open source Apache Hadoop a Apache Sparku a analýzy v Azure.

## <a name="new-features"></a>Nové funkce

Další informace o důležitých změn s HDInsight 4.0., naleznete v tématu [co je nového ve službě Hdinsight 4.0?](../hdinsight/hdinsight-version-release.md#whats-new-in-hdi-40).

## <a name="component-versions"></a>Verze součástí

Oficiální Apache verze všech součástí HDInsight 4.0 jsou uvedeny níže. Uvedené součásti jsou verze nejnovější stabilní verze.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Novější verze komponent Apache jsou někdy spojeny v distribuci HDP kromě verzí uvedených výše. V tomto případě tyto novější verze jsou uvedeny v tabulce verze Technical Preview a by neměl nahradit verzí komponent Apache z výše uvedeného seznamu v produkčním prostředí.

## <a name="apache-patch-information"></a>Informace o Apache oprav

Další informace o opravy, které jsou k dispozici v HDInsight 4.0 naleznete v tématu patch výpis pro jednotlivé produkty v následující tabulce.

| Název produktu | Informace o opravě |
|---|---|
| Ambari | [Informace o opravě Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informace o opravě Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informace o opravě HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Tato verze poskytuje 3.1.0 s další opravy, Apache Hivu.  |
| Kafka | Tato verze poskytuje 1.1.1 s další opravy, Apache Kafka. |
| Oozie | [Informace o opravě Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informace o opravě Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informace o opravě pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informace o opravě ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informace o opravě Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Tato verze poskytuje 1.4.7 s další opravy, Apache Sqoop. |
| Tez | Tato verze poskytuje 0.9.1 s další opravy, Apache Tez. |
| Zeppelin | Tato verze poskytuje Zeppelin 0.8.0 s další opravy, Apache. |
| Zookeeper | [Informace o opravě zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Oprava Common Vulnerabilities and Exposures

Další informace o zabezpečení problémy vyřešené v této verzi najdete v tématu Hortonworks' [pevné Common Vulnerabilities and Exposures pro HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Známé problémy

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikace je porušený pro zabezpečení HBase s výchozí instalace

HDInsight 4.0 proveďte následující kroky:

1. Povolte komunikaci mezi clustery.
1. Přihlaste se k hlavnímu uzlu aktivní.
1. Stáhněte si skript pro povolení replikace pomocí následujícího příkazu:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Zadejte příkaz `sudo kinit <domainuser>`.
1. Zadejte následující příkaz pro spuštění skriptu:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Pro HDInsight 3.6 postupujte takto:

1. Přihlaste se k aktivní HMaster ZK.
1. Stáhněte si skript pro povolení replikace pomocí následujícího příkazu:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Zadejte příkaz `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Zadejte následující příkaz:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline přestane pracovat po migraci clusteru HBase pro HDInsight 4.0

Proveďte následující kroky:

1. Vyřaďte v následujících tabulkách Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Pokud nemůžete odstranit kterékoli z tabulek, restartujte HBase zrušte všechna připojení k tabulkám.
1. Spusťte `sqlline.py` znovu. Phoenix znovu vytvořit všechny tabulky, které byly odstraněny v kroku 1.
1. Znovu vygenerovat zobrazení dat HBase a Phoenix tabulky.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline přestane pracovat po replikaci metadat HBase Phoenix v HDInsight 3.6 do 4.0

Proveďte následující kroky:

1. Než přistoupíte k replikaci, přejděte na cílovém 4.0 clusteru a provedení `sqlline.py`. Tento příkaz vygeneruje Phoenix tabulky jako `SYSTEM.MUTEX` a `SYSTEM.LOG` , které existují pouze ve 4.0.
1. Vyřaďte v následujících tabulkách:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Spuštění replikace HBase

## <a name="deprecation"></a>Vyřazení

Apache Storm a ML služby nejsou dostupné v HDInsight 4.0.
