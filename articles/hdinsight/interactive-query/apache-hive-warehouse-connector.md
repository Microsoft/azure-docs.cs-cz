---
title: Apache Spark & – konektor pro datový sklad podregistru – Azure HDInsight
description: Přečtěte si, jak integrovat Apache Spark a Apache Hive pomocí konektoru skladu pro podregistr v Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869884"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrace Apache Spark a Apache Hive pomocí konektoru skladu s podregistru v Azure HDInsight

Konektor Apache Hive Warehouse (umožní) je knihovna, která umožňuje snadnější práci s Apache Spark a Apache Hive. Podporuje úlohy, jako je přesun dat mezi datovými rámečky Sparku a tabulkami podregistru. Také nasměrováním datových proudů Sparku do tabulek podregistru. Konektor datového skladu pro podregistr funguje jako most mezi Sparkem a podregistrem. Podporuje také Scala, Java a Python jako programovací jazyky pro vývoj.

Konektor skladiště pro podregistr umožňuje využívat jedinečné funkce podregistru a Sparku k vytváření výkonných aplikací pro velké objemy dat.

Apache Hive nabízí podporu pro databázové transakce, které jsou atomické, konzistentní, izolované a trvalé (KYSELé). Další informace o KYSELINě a transakcích v podregistru najdete v tématu [transakce podregistru](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Podregistr také nabízí podrobné kontrolní mechanismy zabezpečení prostřednictvím Apache Ranger a LLAP (Analytical Processing Analytical Processing), které nejsou k dispozici v Apache Spark.

Apache Spark má strukturované rozhraní API pro streamování, které poskytuje možnosti streamování, které nejsou dostupné v Apache Hive. Počínaje HDInsight 4,0 Apache Spark 2.3.1 a Apache Hive 3.1.0 mít samostatné metaúložiště. Samostatné metaúložiště může zajistit obtížnou interoperabilitu. Konektor pro skladiště podregistru usnadňuje používání Sparku a úlů společně. Knihovna umožní načítá data z procesů LLAP démonů do paralelního vykonavatele. Díky tomuto procesu je efektivnější a přizpůsobitelnější než standardní připojení JDBC z Sparku do podregistru.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="Architektura konektoru skladu podregistru" border="true":::

Mezi operace podporované konektorem skladu podregistru patří:

* Popis tabulky
* Vytvoření tabulky pro data ve formátu ORC
* Výběr dat podregistru a načítání datového rámce
* Zápis datového rámce do podregistru ve Batch
* Spuštění příkazu aktualizace podregistru
* Čtení dat tabulky z podregistru, transformace v Sparku a její zápis do nové tabulky podregistru
* Zápis datového rámce nebo datového proudu Sparku do podregistru pomocí HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Nastavení konektoru pro skladiště v podregistru

> [!IMPORTANT]
> - HiveServer2 Interactive instance, která je nainstalovaná na Spark 2,4 Balíček zabezpečení podniku clustery, se nepodporuje pro použití s konektorem skladu pro podregistr. Místo toho musíte nakonfigurovat samostatný HiveServer2 interaktivní cluster pro hostování HiveServer2 interaktivních úloh. Konfigurace konektoru skladu podregistru, která využívá jeden cluster Spark 2,4, není podporována.
> - Knihovna umožní (podregistr Warehouse Connector) není podporována pro použití s clustery interaktivních dotazů, kde je povolena funkce Správa úloh (WLM). <br>
V případě, že máte jenom úlohy Sparku a chcete použít knihovnu umožní, ujistěte se, že cluster interaktivních dotazů nemá povolenou funkci správy úloh ( `hive.server2.tez.interactive.queue` konfigurace není nastavená v konfiguracích podregistru). <br>
V případě scénáře, kdy existuje jak úlohy Spark (umožní), tak nativní úlohy LLAP, je třeba vytvořit dva samostatné clustery interaktivních dotazů se sdílenou databází metastore. Jeden cluster pro nativní úlohy LLAP, ve kterých se dá povolit funkce WLM, a další cluster pro umožní úlohy, kde by se neměla konfigurovat funkce WLM.
Je důležité si uvědomit, že plány prostředků WLM můžete zobrazit z obou clusterů i v případě, že jsou povolené jenom v jednom clusteru. Neprovádějte žádné změny v plánech prostředků v clusteru, kde je funkce WLM zakázaná, protože by mohla ovlivnit funkčnost WLM v jiném clusteru.

Konektor Warehouse pro podregistr potřebuje samostatné clustery pro úlohy Spark a interaktivní dotazy. Pomocí těchto kroků nastavte tyto clustery ve službě Azure HDInsight.

### <a name="create-clusters"></a>Vytváření clusterů

1. Vytvořte cluster HDInsight Spark **4,0** s účtem úložiště a vlastní virtuální sítí Azure. Informace o vytvoření clusteru ve virtuální síti Azure najdete v tématu [Přidání HDInsight do existující virtuální sítě](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Vytvořte cluster HDInsight Interactive Query (LLAP) **4,0** se stejným účtem úložiště a Azure Virtual Network jako cluster Spark.

### <a name="configure-hwc-settings"></a>Konfigurace nastavení umožní

#### <a name="gather-preliminary-information"></a>Shromáždit předběžné informace

1. Ve webovém prohlížeči přejděte na místo, `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` kde LLAPCLUSTERNAME je název vašeho clusteru interaktivních dotazů.

1. Přejděte na **summary**  >  **HiveServer2 Interactive JDBC URL** a poznamenejte si hodnotu. Hodnota může být podobná: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Přejděte do části **Konfigurace**  >  **Upřesnit**  >  **Upřesnit podregistr podregistr-site**  >  **. Zookeeper. kvorum** a poznamenejte si hodnotu. Hodnota může být podobná: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Přejděte na **Konfigurace**  >  **Upřesnit**  >  **Obecné**  >  **podregistru. metastore. URI** a poznamenejte si hodnotu. Hodnota může být podobná: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Přejděte na **Konfigurace**  >  **Upřesnit**  >  **pokročilý podregistr-Interactive-site**  >  **podregistr. llap. démon. Service. Hosts** a poznamenejte si hodnotu. Hodnota může být podobná: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Konfigurace nastavení clusteru Spark

1. Ve webovém prohlížeči přejděte na místo, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` kde název_clusteru je název vašeho clusteru Apache Spark.

1. Rozbalte **vlastní spark2 – výchozí**.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Konfigurace Spark2 Apache Ambari" border="true":::

1. Vyberte **Přidat vlastnost...** a přidejte následující konfigurace:

    | Konfigurace | Hodnota |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Nastavte vhodný přípravný adresář kompatibilní s HDFS. Pokud máte dva různé clustery, pracovní adresář by měl být složka v pracovním adresáři účtu úložiště LLAP clusteru, aby k němu měl přístup HiveServer2.  Nahraďte `STORAGE_ACCOUNT_NAME` názvem účtu úložiště použitým clusterem a `STORAGE_CONTAINER_NAME` názvem kontejneru úložiště. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Hodnota, kterou jste získali dříve z **HiveServer2 Interactive JDBC URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| Hodnota, kterou jste získali dříve z **podregistru. metastore. URI**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` pro režim clusteru PŘÍZe a `false` pro režim přízového klienta. |
    |`spark.hadoop.hive.zookeeper.quorum`| Hodnota, kterou jste získali dříve z **podregistru. Zookeeper. kvora**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| Hodnota, kterou jste získali dříve z **podregistru. llap. démon. Service. Hosts**. |

1. Uložte změny a restartujte všechny ovlivněné součásti.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Konfigurace clusterů umožní for Balíček zabezpečení podniku (ESP)

Balíček zabezpečení podniku (ESP) poskytuje podnikové funkce, jako je ověřování založené na službě Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro Apache Hadoop clustery ve službě Azure HDInsight. Další informace o protokolu ESP najdete v tématu [použití balíček zabezpečení podniku ve službě HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Kromě konfigurací uvedených v předchozí části přidejte následující konfiguraci pro použití umožní v clusterech ESP.

1. V Ambari webovém uživatelském rozhraní clusteru Spark přejděte do **Spark2**  >  **Configurations**  >  **Custom Spark2-Defaults**.

1. Aktualizujte následující vlastnost.

    | Konfigurace | Hodnota |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Ve webovém prohlížeči přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` umístění název_clusteru, kde je název clusteru interaktivních dotazů. Klikněte na **HiveServer2 Interactive (interaktivní**). Zobrazí se plně kvalifikovaný název domény (FQDN) hlavního uzlu, na kterém běží LLAP, jak je znázorněno na snímku obrazovky. Nahraďte `<llap-headnode>` touto hodnotou.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hlavní uzel konektoru skladu podregistru" border="true":::

    * Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru interaktivních dotazů. Vyhledejte `default_realm` parametr v `/etc/krb5.conf` souboru. Nahraďte `<AAD-DOMAIN>` touto hodnotou jako řetězec velkými písmeny, jinak se přihlašovací údaje nenaleznou.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Doména AAD konektoru skladu v podregistru" border="true":::

    * Například `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Podle potřeby uložte změny a restartujte součásti.

## <a name="hive-warehouse-connector-usage"></a>Využití konektoru skladu v podregistru

Můžete si vybrat mezi několika různými způsoby, jak se připojit ke clusteru interaktivních dotazů a provádět dotazy pomocí konektoru skladu z podregistru. Mezi podporované metody patří následující nástroje:

* [Spark – prostředí/PySpark](../spark/apache-spark-shell.md)
* [Spark – odeslání](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Níže jsou uvedeny některé příklady, jak se připojit k umožní ze Sparku.

### <a name="spark-shell"></a>Spark – prostředí

1. Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru Apache Spark. Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte následující příkaz, abyste si poznamenali `hive-warehouse-connector-assembly` verzi:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Níže uvedený kód upravte s `hive-warehouse-connector-assembly` výše uvedenou verzí. Pak spusťte příkaz pro spuštění prostředí Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Po spuštění prostředí Spark se dá instance konektoru skladu podregistru spustit pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark – odeslání

Po sestavení kódu Scala/Java společně se závislostmi na jar sestavení použijte následující příkaz ke spuštění aplikace Spark. Nahraďte `<VERSION>` a `<APP_JAR_PATH>` skutečnými hodnotami.

* Model PŘÍZového klienta
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Režim clusteru PŘÍZe
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Pro Python přidejte taky následující konfiguraci. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Spouštění dotazů v clusterech Balíček zabezpečení podniku (ESP)

Použijte `kinit` před spuštěním Spark-Shell nebo Spark-Submit. Nahraďte uživatelské jméno názvem účtu domény s oprávněním k přístupu ke clusteru a spusťte následující příkaz:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpečení dat v clusterech Spark ESP

1. Vytvořte tabulku `demo` s ukázkovými daty zadáním následujících příkazů:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zobrazte obsah tabulky pomocí následujícího příkazu. Před použitím této zásady `demo` tabulka zobrazuje celý sloupec.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="Ukázková tabulka před použitím zásad Ranger" border="true":::

1. Použijte zásadu maskování sloupců, která zobrazuje jenom poslední čtyři znaky sloupce.  
    1. Přejít do uživatelského rozhraní správce Ranger na adrese `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Klikněte na podregistr Service pro váš cluster v **podregistru**.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="Ranger Service Manager" border="true":::
    1. Klikněte na kartu **maskování** a pak **přidejte nové zásady** .

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="seznam zásad podregistru Ranger konektoru skladu podregistru" border="true":::

    1. Zadejte požadovaný název zásad. Vyberte databázi: **výchozí**, tabulka podregistru: **Ukázka**, sloupec podregistru: **název**, uživatel: **Rsadmin2**, typy přístupu: **Vybrat** a **částečná maska: Zobrazit poslední 4** v nabídce **možností výběru maskování** . Klikněte na **Přidat**.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="vytvořit zásadu" border="true":::
1. Znovu zobrazte obsah tabulky. Po použití zásad Ranger uvidíme jenom poslední čtyři znaky sloupce.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="Ukázková tabulka po použití zásad Ranger" border="true":::

## <a name="next-steps"></a>Další kroky

* [Provoz Apache Sparku a HWC](./apache-hive-warehouse-connector-operations.md)
* [Použití Interactive Query se službou HDInsight](./apache-interactive-query-get-started.md).
* [Integrace HWC s Apache Zeppelinem](./apache-hive-warehouse-connector-zeppelin.md)
* [Příklady interakce s konektorem skladiště v podregistru pomocí Zeppelin, Livy, Spark-Submit a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
