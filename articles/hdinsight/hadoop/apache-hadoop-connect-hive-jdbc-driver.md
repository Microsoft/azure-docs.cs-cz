---
title: Dotazování Apache Hive prostřednictvím ovladače JDBC – Azure HDInsight
description: Pomocí ovladače JDBC z aplikace Java můžete odesílat Apache Hive dotazy do Hadoop v HDInsight. Připojte se prostřednictvím kódu programu a z klienta SQL SQuirrel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 2250e41bffc26bd9ae59dfc652a06d08016d227a
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053795"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Dotazování Apache Hive prostřednictvím ovladače JDBC v HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Naučte se používat ovladač JDBC z aplikace Java k odesílání dotazů Apache Hive do Apache Hadoop ve službě Azure HDInsight. Informace v tomto dokumentu ukazují, jak se připojit prostřednictvím kódu programu a z klienta SQL SQuirreL.

Další informace o rozhraní JDBC podregistru najdete v tématu [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Předpoklady

* An HDInsight clusteru Hadoop. Pokud ho chcete vytvořit, přečtěte si téma Začínáme [se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* [Sada Java Developer Kit (JDK) verze 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) nebo vyšší.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL je klientská aplikace JDBC.

## <a name="jdbc-connection-string"></a>Připojovací řetězec JDBC

Připojení JDBC k clusteru HDInsight v Azure se provádí přes port 443 a přenos se zabezpečuje pomocí protokolu SSL. Veřejná brána, kterou cluster zaznamená, přesměruje provoz na port, na kterém HiveServer2 skutečně naslouchá. Následující připojovací řetězec ukazuje formát, který se má použít pro HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Parametr `CLUSTERNAME` nahraďte názvem vašeho clusteru HDInsight.

## <a name="authentication"></a>Ověření

Při navazování připojení musíte k ověření brány clusteru použít název a heslo správce clusteru HDInsight. Při připojování z JDBC klientů, jako je SQuirreL SQL, je nutné v nastavení klienta zadat jméno správce a heslo.

Z aplikace Java je nutné použít při navazování připojení jméno a heslo. Například následující kód Java otevírá nové připojení pomocí připojovacího řetězce, jména správce a hesla:

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

    ![Karta ovladače na levé straně okna](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Z ikon v horní části dialogového okna **ovladače** vyberte ikonu **+** pro vytvoření ovladače.

    ![SQuirreL – ikona ovladačů aplikace SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. V dialogovém okně Přidat ovladač přidejte následující informace:

    |Vlastnost | Hodnota |
    |---|---|
    |Name (Název)|Hive|
    |Příklad adresy URL|JDBC: hive2://localhost: 443/výchozí; transportMode = http; SSL = true; httpPath =/hive2|
    |Cesta k nadbytečné třídě|Pomocí tlačítka **Přidat** přidejte všechny soubory jar stažené dříve.|
    |Název třídy|org. Apache. podregistr. JDBC. HiveDriver|

   ![Dialog Přidat ovladač s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Kliknutím na **tlačítko OK** tato nastavení uložte.

6. Na levé straně okna SQuirreL SQL vyberte **aliasy**. Pak vyberte ikonu **+** pro vytvoření aliasu připojení.

    ![Dialog SQuirreL SQL přidat nový alias](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. V dialogovém okně **Přidat alias** použijte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Name (Název)|Podregistr v HDInsight|
    |Ovladač|Pomocí rozevíracího seznamu vyberte ovladač **podregistru** .|
    |Adresa URL|JDBC: hive2://CLUSTERNAME.azurehdinsight.net: 443/výchozí; transportMode = http; SSL = true; httpPath =/hive2. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.|
    |Uživatelské jméno|Název přihlašovacího účtu clusteru pro cluster HDInsight. Výchozí hodnota je **admin (správce**).|
    |Heslo|Heslo pro přihlašovací účet clusteru|

    ![Dialog Přidat alias s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > K ověření, že připojení funguje, použijte tlačítko **test** . Když se připojíte k: zobrazí se dialogové okno pro zobrazení **podregistru v HDInsight** , vyberte **připojit** a proveďte test. Pokud je test úspěšný, zobrazí se dialogové okno **připojení bylo úspěšné** . Pokud dojde k chybě, přečtěte si téma [Poradce při potížích](#troubleshooting).

    Pokud chcete uložit alias připojení, použijte tlačítko **OK** v dolní části dialogu **Přidat alias** .

8. Z rozevíracího seznamu **připojit k** v horní části SQuirreL SQL vyberte **podregistr v HDInsight**. Po zobrazení výzvy vyberte **připojit**.

    ![Dialogové okno připojení s parametry](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Po připojení zadejte následující dotaz do dialogového okna dotazu SQL a pak vyberte ikonu **Spustit** (spuštěná osoba). V oblasti výsledků by se měly zobrazit výsledky dotazu.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![Dialogové okno dotazu SQL, včetně výsledků](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Připojení z ukázkové aplikace Java

Příklad použití klienta Java k dotazování na podregistr v HDInsight je k dispozici na adrese [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Podle pokynů v úložišti Sestavte a spusťte ukázku.

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

1. Ukončete SQuirreL a potom v systému vyhledejte adresář, ve kterém je nainstalováno SQuirreL, případně `C:\Program Files\squirrel-sql-4.0.0\lib`. V adresáři SquirreL v adresáři `lib` nahraďte existující Commons-Codec. jar jedním staženým z clusteru HDInsight.

1. Restartujte SQuirreL. Tato chyba by se už neměla vyskytnout při připojování k podregistru v HDInsight.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat JDBC k práci s podregistrem, použijte následující odkazy k prozkoumání dalších způsobů, jak pracovat s Azure HDInsight.

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md)
* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
