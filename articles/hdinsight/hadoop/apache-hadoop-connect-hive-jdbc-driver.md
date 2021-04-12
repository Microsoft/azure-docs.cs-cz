---
title: Dotazování Apache Hive prostřednictvím ovladače JDBC – Azure HDInsight
description: Pomocí ovladače JDBC z aplikace Java můžete odesílat Apache Hive dotazy do Hadoop v HDInsight. Připojte se prostřednictvím kódu programu a z klienta SQL SQuirrel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 6073000f2f14f835e2bfbd91b41619101c36b10f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866841"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Dotazování Apache Hivu prostřednictvím ovladače JDBC v HDInsightu

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Naučte se používat ovladač JDBC z aplikace Java. Odeslání dotazů Apache Hive do Apache Hadoop ve službě Azure HDInsight. Informace v tomto dokumentu ukazují, jak se připojit prostřednictvím kódu programu a z klienta SQL SQuirreL.

Další informace o rozhraní JDBC podregistru najdete v tématu [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Požadavky

* An HDInsight clusteru Hadoop. Pokud ho chcete vytvořit, přečtěte si téma Začínáme [se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Ujistěte se, že je služba HiveServer2 spuštěná.
* [Sada Java Developer Kit (JDK) verze 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) nebo vyšší.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL je klientská aplikace JDBC.

## <a name="jdbc-connection-string"></a>Připojovací řetězec JDBC

Připojení JDBC k clusteru HDInsight v Azure se provádí přes port 443. Přenosy se zabezpečují pomocí protokolu TLS/SSL. Veřejná brána, kterou cluster zaznamená, přesměruje provoz na port, na kterém HiveServer2 skutečně naslouchá. Následující připojovací řetězec ukazuje formát, který se má použít pro HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Parametr `CLUSTERNAME` nahraďte názvem vašeho clusteru HDInsight.

Nebo můžete získat připojení prostřednictvím **uživatelského rozhraní Ambari > > konfiguracích registru > Upřesnit**.

:::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png" alt-text="Získání připojovacího řetězce JDBC prostřednictvím Ambari" border="true":::

### <a name="host-name-in-connection-string"></a>Název hostitele v připojovacím řetězci

Název hostitele CLUSTERNAME.azurehdinsight.net v připojovacím řetězci je stejný jako adresa URL clusteru. Můžete ji získat prostřednictvím Azure Portal.

### <a name="port-in-connection-string"></a>Port v připojovacím řetězci

**Port 443** můžete použít jenom pro připojení ke clusteru z některých míst mimo virtuální síť Azure. HDInsight je spravovaná služba, která znamená, že všechna připojení ke clusteru se spravují přes zabezpečenou bránu. K HiveServer 2 se nemůžete připojit přímo na portech 10001 nebo 10000. Tyto porty nejsou vystavené vně.

## <a name="authentication"></a>Authentication

Při navazování připojení použijte k ověření název správce clusteru HDInsight a heslo. Z JDBC klientů, jako je SQuirreL SQL, zadejte do nastavení klienta jméno správce a heslo.

Z aplikace Java je nutné použít při navazování připojení jméno a heslo. Například následující kód Java otevírá nové připojení:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Připojení pomocí klienta SQL SQuirreL

SQuirreL SQL je klient JDBC, který se dá použít pro vzdálenou spouštění dotazů na podregistry v clusteru HDInsight. V následujících krocích se předpokládá, že jste už nainstalovali SQuirreL SQL.

1. Vytvořte adresář, který bude obsahovat určité soubory, které se mají zkopírovat z clusteru.

2. V následujícím skriptu nahraďte `sshuser` názvem uživatelského účtu SSH pro daný cluster.  Nahraďte `CLUSTERNAME` názvem clusteru HDInsight.  Z příkazového řádku změňte pracovní adresář na ten vytvořený v předchozím kroku a potom zadejte následující příkaz ke zkopírování souborů z clusteru HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Spusťte aplikaci SQuirreL SQL. V levé části okna vyberte možnost **ovladače**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png" alt-text="Karta ovladače na levé straně okna" border="true":::

4. Z ikon v horní části dialogového okna **ovladače** vyberte **+** ikonu pro vytvoření ovladače.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png" alt-text="SQuirreL – ikona ovladačů aplikace SQL" border="true":::

5. V dialogovém okně Přidat ovladač přidejte následující informace:

    |Vlastnost | Hodnota |
    |---|---|
    |Název|Hive|
    |Příklad adresy URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Cesta k nadbytečné třídě|Pomocí tlačítka **Přidat** přidejte všechny soubory jar stažené dříve.|
    |Název třídy|org. Apache. podregistr. JDBC. HiveDriver|

   :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png" alt-text="Dialog Přidat ovladač s parametry" border="true":::

   Kliknutím na **tlačítko OK** tato nastavení uložte.

6. Na levé straně okna SQuirreL SQL vyberte **aliasy**. Pak vyberte **+** ikonu pro vytvoření aliasu připojení.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png" alt-text="Dialog SQuirreL SQL přidat nový alias" border="true":::

7. V dialogovém okně **Přidat alias** použijte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Název|Podregistr v HDInsight|
    |Ovladač|Pomocí rozevíracího seznamu vyberte ovladač **podregistru** .|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.|
    |Uživatelské jméno|Název přihlašovacího účtu clusteru pro cluster HDInsight. Výchozí hodnota je **admin (správce**).|
    |Heslo|Heslo pro přihlašovací účet clusteru|

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png" alt-text="Dialog Přidat alias s parametry" border="true":::

    > [!IMPORTANT]
    > K ověření, že připojení funguje, použijte tlačítko **test** . Když se připojíte k: zobrazí se dialogové okno pro zobrazení **podregistru v HDInsight** , vyberte **připojit** a proveďte test. Pokud je test úspěšný, zobrazí se dialogové okno **připojení bylo úspěšné** . Pokud dojde k chybě, přečtěte si téma [Poradce při potížích](#troubleshooting).

    Pokud chcete uložit alias připojení, použijte tlačítko **OK** v dolní části dialogu **Přidat alias** .

8. Z rozevíracího seznamu **připojit k** v horní části SQuirreL SQL vyberte **podregistr v HDInsight**. Po zobrazení výzvy vyberte **připojit**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png" alt-text="Dialogové okno připojení s parametry" border="true":::

9. Po připojení zadejte následující dotaz do dialogového okna dotazu SQL a pak vyberte ikonu **Spustit** (spuštěná osoba). V oblasti výsledků by se měly zobrazit výsledky dotazu.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png" alt-text="Dialogové okno dotazu SQL, včetně výsledků" border="true":::

## <a name="connect-from-an-example-java-application"></a>Připojení z ukázkové aplikace Java

Příklad použití klienta Java k dotazování na podregistr v HDInsight je k dispozici na adrese [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Podle pokynů v úložišti Sestavte a spusťte ukázku.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Při pokusu o otevření připojení SQL došlo k neočekávané chybě.

**Příznaky**: při připojování ke clusteru HDInsight, který má verzi 3,3 nebo vyšší, se může zobrazit chyba, že došlo k neočekávané chybě. Trasování zásobníku pro tuto chybu začíná na následujících řádcích:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Příčina**: Tato chyba je způsobená starším souborem. jar Commons-Codec verze, který je součástí SQuirreL.

**Řešení**: Pokud chcete tuto chybu opravit, použijte následující postup:

1. Ukončete SQuirreL a potom v systému vyhledejte adresář, ve kterém je SQuirreL nainstalovaný, třeba `C:\Program Files\squirrel-sql-4.0.0\lib` . V adresáři SquirreL v `lib` adresáři v adresáři nahraďte existující Commons-Codec. jar jedním staženým z clusteru HDInsight.

1. Restartujte SQuirreL. Tato chyba by se už neměla vyskytnout při připojování k podregistru v HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Připojení odpojené službou HDInsight

**Příznaky**: při pokusu o stažení obrovských objemů dat (například několik GB) prostřednictvím JDBC/ODBC se při stahování neočekávaně odpojí připojení ke službě HDInsight.

**Příčina**: Tato chyba je způsobená omezením u uzlů brány. Při získávání dat z JDBC/ODBC musí všechna data projít uzlem brány. Brána však není navržena tak, aby stahoval velké množství dat, takže brána může připojení ukončit, pokud nemůže zpracovat provoz.

**Řešení**: Vyhněte se použití ovladače JDBC/ODBC ke stahování velkých objemů dat. Místo toho zkopírujte data přímo z úložiště objektů BLOB.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat JDBC k práci s podregistrem, použijte následující odkazy k prozkoumání dalších způsobů, jak pracovat s Azure HDInsight.

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](../use-pig.md)
* [Použijte úlohy MapReduce s HDInsight](hdinsight-use-mapreduce.md)
