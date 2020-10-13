---
title: Konektor skladiště pro podregistr – Apache Zeppelin využívající Livy – Azure HDInsight
description: Naučte se integrovat konektory ve skladu s Apache Zeppelin v Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6ee1c70ec02af2a24f7867a6e6b06593361612b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86083113"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrace Apache Zeppelin s použitím konektoru datového skladu pro podregistr ve službě Azure HDInsight

Clustery HDInsight Spark obsahují poznámkové bloky Apache Zeppelin s různými interprety. V tomto článku se zaměříme jenom na překladač Livy, který umožňuje přístup k tabulkám podregistru z Sparku pomocí konektoru skladu z podregistru.

## <a name="prerequisite"></a>Požadavek

Dokončete kroky pro [instalaci konektoru skladu podregistru](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Začínáme

1. Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru Apache Spark. Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte následující příkaz, který poznamenejte verze pro `hive-warehouse-connector-assembly` a `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Při konfiguraci Apache Zeppelin uložte výstup pro pozdější použití.

## <a name="configure-livy"></a>Konfigurace Livy

Pro přístup k tabulkám podregistru z Zeppelin pomocí překladače Livy jsou vyžadovány následující konfigurace.

### <a name="interactive-query-cluster"></a>Cluster interaktivních dotazů

1. Ve webovém prohlížeči přejděte na místo, `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` kde LLAPCLUSTERNAME je název vašeho clusteru interaktivních dotazů.

1. Přejděte k **rozšířenému**  >  **vlastnímu základnímu serveru**. Vyberte **Přidat vlastnost...** a přidejte následující konfigurace:

    | Konfigurace                 | Hodnota |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. Livy. Groups  | *     |
    | Hadoop. proxyuser. Livy. Hosts   | *     |

1. Uložte změny a restartujte všechny ovlivněné součásti.

### <a name="spark-cluster"></a>Cluster Spark

1. Ve webovém prohlížeči přejděte na místo, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` kde název_clusteru je název vašeho clusteru Apache Spark.

1. Rozbalte **vlastní livy2-conf**. Vyberte **Přidat vlastnost...** a přidejte následující konfiguraci:

    | Konfigurace                 | Hodnota                                      |
    | ----------------------------- |------------------------------------------  |
    | Livy. soubor. Local-dir-seznam povolených | /usr/HDP/Current/hive_warehouse_connector/ |

1. Uložte změny a restartujte všechny ovlivněné součásti.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Konfigurace překladače Livy v uživatelském rozhraní Zeppelin (cluster Spark)

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` místo, kde `CLUSTERNAME` je název vašeho clusteru Apache Spark.

1. Přejděte na **livy2**.

1. Přidejte následující konfigurace:

    | Konfigurace                 | Hodnota                                      |
    | ----------------------------- |:------------------------------------------:|
    | Livy. spark. Hadoop. podregistr. llap. démon. Service. Hosts | @llap0 |
    | Livy. spark. Security. Credentials. hiveserver2. Enabled | true |
    | Livy. spark. SQL. podregistr. llap | true |
    | Livy. spark. příze. Security. Credentials. hiveserver2. Enabled | true |
    | Livy. prousers | Livy, Zeppelin |
    | Livy. spark. jar | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Nahraďte verzi hodnotou, kterou jste získali v části [Začínáme](#getting-started), dříve. |
    | Livy. spark. Submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Nahraďte verzi hodnotou, kterou jste získali v části [Začínáme](#getting-started), dříve. |
    | Livy. spark. SQL. podregistr. hiveserver2. JDBC. URL | Nastavte ji na HiveServer2 interaktivní JDBC adresu URL clusteru interaktivních dotazů. |
    | Spark. Security. Credentials. hiveserver2. Enabled | true |

1. Pouze pro clustery ESP přidejte následující konfiguraci:

    | Konfigurace| Hodnota|
    |---|---|
    | Livy. spark. SQL. podregistr. hiveserver2. JDBC. URL. Principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * Ve webovém prohlížeči přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` umístění název_clusteru, kde je název clusteru interaktivních dotazů. Klikněte na **HiveServer2 Interactive (interaktivní**). Zobrazí se plně kvalifikovaný název domény (FQDN) hlavního uzlu, na kterém běží LLAP, jak je znázorněno na snímku obrazovky. Nahraďte `<llap-headnode>` touto hodnotou.

        ![Hlavní uzel konektoru skladu podregistru](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru interaktivních dotazů. Vyhledejte `default_realm` parametr v `/etc/krb5.conf` souboru. Nahraďte `<AAD-DOMAIN>` touto hodnotou jako řetězec velkými písmeny, jinak se přihlašovací údaje nenaleznou.

        ![Doména AAD konektoru skladu v podregistru](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Například `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .

1. Uložte změny a restartujte překladač Livy.

Pokud není překladač Livy dostupný, upravte `shiro.ini` soubor obsažený v součásti Zeppelin v Ambari. Další informace najdete v tématu [Konfigurace zabezpečení Apache Zeppelin](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Spouštění dotazů v Zeppelin 

Spusťte Poznámkový blok Zeppelin pomocí překladače Livy a spusťte následující

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Další kroky

* [Provoz Apache Sparku a HWC](./apache-hive-warehouse-connector-operations.md)
* [Integrace HWC s Apache Sparkem a Apache Hivem](./apache-hive-warehouse-connector.md)
* [Použití Interactive Query se službou HDInsight](./apache-interactive-query-get-started.md).
