---
title: Dotaz apache hive prostřednictvím ovladače JDBC - Azure HDInsight
description: Pomocí ovladače JDBC z aplikace Java odešlete dotazy Apache Hive hadoopu na HDInsight. Připojte se programově a z klienta SQuirrel SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7d1a77800093ae01bc4eb1e1269d1e9a60f9ce26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616648"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Dotazování Apache Hivu prostřednictvím ovladače JDBC v HDInsightu

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Přečtěte si, jak pomocí ovladače JDBC z aplikace Java odesílat dotazy Apache Hive apache hadoopu v Azure HDInsight. Informace v tomto dokumentu ukazuje, jak se připojit programově a z klienta SQuirreL SQL.

Další informace o rozhraní JDBC hive naleznete v [tématu HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight Hadoop. Pokud chcete ho vytvořit, [přečtěte si, že začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Ujistěte se, že je spuštěna služba HiveServer2.
* [Java Developer Kit (JDK) verze 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) nebo vyšší.
* [SquirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL je klientská aplikace JDBC.

## <a name="jdbc-connection-string"></a>Připojovací řetězec JDBC

Připojení JDBC k clusteru HDInsight v Azure se provádí přes port 443 a provoz je zabezpečen pomocí ssl. Veřejná brána, za kterou clustery sedí, přesměruje přenos na port, na který HiveServer2 ve skutečnosti naslouchá. Následující připojovací řetězec zobrazuje formát, který se má použít pro HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Parametr `CLUSTERNAME` nahraďte názvem vašeho clusteru HDInsight.

Nebo můžete získat připojení prostřednictvím **Ambari UI > Hive > Configs > Advanced**.

![Získat připojovací řetězec JDBC přes Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Název hostitele v připojovacím řetězci

Název hostitele "CLUSTERNAME.azurehdinsight.net" v připojovacím řetězci je stejný jako název adresy URL clusteru. Můžete ji získat prostřednictvím portálu Azure. 

### <a name="port-in-connection-string"></a>Port v připojovacím řetězci

**Port 443** můžete použít jenom k připojení ke clusteru z některých míst mimo virtuální síť Azure. HDInsight je spravovaná služba, což znamená, že všechna připojení ke clusteru jsou spravována prostřednictvím zabezpečené brány. Nelze připojit k HiveServer 2 přímo na portech 10001 nebo 10000, protože tyto porty nejsou vystaveny vnější. 

## <a name="authentication"></a>Ověřování

Při navazování připojení je nutné k ověření brány clusteru použít název a heslo správce clusteru HDInsight. Při připojování z klientů JDBC, jako je SQuirreL SQL, musíte zadat název správce a heslo v nastavení klienta.

Z aplikace Java, musíte použít název a heslo při navazování připojení. Například následující kód Jazyka Java otevře nové připojení pomocí připojovacího řetězce, názvu správce a hesla:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Spojte se s klientem SQuirreL SQL

SQuirreL SQL je klient JDBC, který lze použít ke vzdálenému spuštění dotazů Hive s clusterem HDInsight. Následující kroky předpokládají, že jste již nainstalovali SQuirreL SQL.

1. Vytvořte adresář obsahující určité soubory, které mají být zkopírovány z clusteru.

2. V následujícím skriptu `sshuser` nahraďte názvem uživatelského účtu SSH pro cluster.  Nahraďte `CLUSTERNAME` název clusteru HDInsight.  Z příkazového řádku změňte svůj pracovní adresář na adresář vytvořený v předchozím kroku a zadejte následující příkaz pro kopírování souborů z clusteru HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Spusťte aplikaci SQuirreL SQL. Vlevo od okna vyberte **Ovladače**.

    ![Karta Ovladače na levé straně okna](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Z ikon v horní **Drivers** části dialogového **+** okna Ovladače vyberte ikonu pro vytvoření ovladače.

    ![Ikona ovladačů aplikací SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. V dialogovém okně Přidat ovladač přidejte následující informace:

    |Vlastnost | Hodnota |
    |---|---|
    |Name (Název)|Hive|
    |Příklad adresy URL|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Cesta k extra třídě|Pomocí tlačítka **Přidat** přidejte všechny dříve stažené soubory jar.|
    |Název třídy|org.apache.hive.jdbc.HiveDriver|

   ![přidat dialogové okno ovladače s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Chcete-li tato nastavení uložit, vyberte **OK.**

6. Na levé straně okna SQuirreL SQL vyberte **Aliasy**. Pak vyberte **+** ikonu a vytvořte alias připojení.

    ![SQuirreL SQL přidat nový alias dialog](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Pro dialogové okno **Přidat alias** použijte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Name (Název)|Hive na HDInsight|
    |Ovladač|Pomocí rozevíracího výběru vyberte ovladač **Hive.**|
    |zprostředkovatele identity|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.|
    |Uživatelské jméno|Název přihlašovacího účtu clusteru pro váš cluster HDInsight. Výchozí hodnota je **admin**.|
    |Heslo|Heslo pro přihlašovací účet clusteru.|

    ![dialogové okno přidání aliasu s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Pomocí tlačítka **Test** ověřte, zda připojení funguje. Když se zobrazí dialogové okno **Připojit k: Hive na HDInsight,** vyberte **Připojit** a proveďte test. Pokud je test úspěšný, zobrazí se dialogové **okno Connection úspěšné.** Pokud dojde k chybě, [přečtěte si článek Poradce při potížích](#troubleshooting).

    Chcete-li uložit alias připojení, použijte tlačítko **Ok** v dolní části dialogového okna **Přidat alias.**

8. V rozevíracím souboru **Připojit k** horní části squirrel SQL vyberte **Možnost Hive na hdinsightu**. Po zobrazení výzvy vyberte **Připojit**.

    ![dialogové okno připojení s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Po připojení zadejte následující dotaz do dialogového okna dotazu SQL a pak vyberte ikonu **Spustit** (spuštěná osoba). Oblast výsledků by měla zobrazit výsledky dotazu.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![dialogové okno dotazu SQL, včetně výsledků](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Připojení z ukázkové aplikace Java

Příklad použití klienta Java k dotazování Hive [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)na HDInsight je k dispozici na adrese . Podle pokynů v úložišti vytvořte a spusťte ukázku.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Při pokusu o otevření připojení SQL došlo k neočekávané chybě.

**Příznaky**: Při připojování ke clusteru HDInsight, který je verze 3.3 nebo vyšší, se může zobrazit chyba, že došlo k neočekávané chybě. Trasování zásobníku pro tuto chybu začíná následujícířádky:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Příčina**: Tato chyba je způsobena starší verzí souboru commons-codec.jar, který je součástí squirrelu.

**Řešení**: Chcete-li tuto chybu opravit, postupujte takto:

1. Ukončit SQuirreL, a pak přejděte do adresáře, kde squirrel je nainstalován na vašem systému, možná `C:\Program Files\squirrel-sql-4.0.0\lib`. V adresáři SquirreL `lib` pod adresářem nahraďte existující soubor commons-codec.jar souborem staženým z clusteru HDInsight.

1. Restartujte squirrel. K chybě by již nemělo dojít při připojování k Hive na HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Připojení odpojeno hdinsightem

**Příznaky**: Při pokusu o stažení obrovské množství dat (řekněme několik GBs) prostřednictvím JDBC/ODBC, připojení je odpojen HDInsight neočekávaně při stahování. 

**Příčina**: Tato chyba je způsobena omezením uzlů brány. Při získávání dat z JDBC/ODBC, všechna data musí projít uzlu brány. Brána však není určena ke stažení obrovské množství dat, takže připojení může být uzavřen bránou, pokud nemůže zpracovat provoz.

**Rozlišení**: Vyhněte se použití ovladače JDBC/ODBC ke stažení velkého množství dat. Místo toho zkopírujte data přímo z úložiště objektů blob.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat JDBC pro práci s Hive, použijte následující odkazy k prozkoumání dalších způsobů práce s Azure HDInsight.

* [Vizualizujte data Apache Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizujte data interactive query hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Pomocí Apache Zeppelin můžete ve službě Azure HDInsight spouštět dotazy Apache Hive](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k hdinsightu pomocí ovladače Microsoft Hive ODBC .](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Připojte Excel k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake Tools pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md).
* [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md)
* [Použití Apache Hive s HDInsight](hdinsight-use-hive.md)
* [Použijte Apache Pig s HDInsight](hdinsight-use-pig.md)
* [Použijte úlohy MapReduce s HDInsight](hdinsight-use-mapreduce.md)
