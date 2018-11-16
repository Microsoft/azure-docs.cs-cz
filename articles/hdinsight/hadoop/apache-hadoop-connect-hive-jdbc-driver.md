---
title: Dotazy Apache Hive pomocí ovladače JDBC – Azure HDInsight
description: Pomocí ovladač JDBC z aplikace Java můžete odesílat dotazy Apache Hive ke clusteru Hadoop v HDInsight. Připojení prostřednictvím kódu programu a klienta SQuirrel SQL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: hrasheed
ms.openlocfilehash: 577f36f53bfad79af968a4c34f4b6ab0b49527c7
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633440"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Dotazy Apache Hive pomocí ovladače JDBC v HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Zjistěte, jak použít ovladač JDBC z aplikace Java k zadávání dotazů na Apache Hadoop v Azure HDInsight s Apache Hive. Informace v tomto dokumentu ukazuje, jak se připojit prostřednictvím kódu programu a klienta SQuirrel SQL.

Další informace o rozhraní Hive JDBC, naleznete v tématu [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Požadavky

* Hadoop v clusteru HDInsight.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight 3.3 vyřazení](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL je JDBC klientské aplikace.

* [Java Developer Kit (JDK) verze 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) nebo vyšší.

* [Apache Maven](https://maven.apache.org) Maven je projekt sestavovacího systému pro projekty Java, který se používá v projektu spojený s tímto článkem.

## <a name="jdbc-connection-string"></a>Připojovací řetězec JDBC

JDBC připojení ke clusteru služby HDInsight v Azure jsou vytvořena více než 443, a provoz je zabezpečená pomocí protokolu SSL. Veřejnou brány, který clustery se nacházejí za provoz přesměruje na port, který HiveServer2 skutečně naslouchá. Následující připojovací řetězec znázorňuje formátu pro HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Parametr `CLUSTERNAME` nahraďte názvem vašeho clusteru HDInsight.

## <a name="authentication"></a>Authentication

Při navazování připojení, musíte použít jména správce clusteru HDInsight a heslo k ověření clusteru brány. Při připojování klientů JDBC, jako je například SQuirreL SQL, musíte zadat uživatelské jméno admin a heslo v nastavení klienta.

Z aplikace v Javě musíte použít uživatelské jméno a heslo při navazování připojení. Například následující kód Java se otevře nové připojení pomocí připojovacího řetězce, jméno správce a hesla:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Spojte se s klienta SQuirreL SQL

SQuirreL SQL je klient JDBC, který slouží ke vzdálené spouštění dotazů Hive pomocí clusteru HDInsight. Následující postup předpokládá, že jste již nainstalovali SQuirreL SQL.

1. Vytvoření adresáře, který obsahuje soubory. Například, `mkdir hivedriver`.

2. Z příkazového řádku použijte následující příkazy pro kopírování souborů z clusteru HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    ```

    Nahraďte `USERNAME` s název uživatelského účtu SSH pro cluster. Nahraďte `CLUSTERNAME` s názvem clusteru HDInsight.

3. Spusťte aplikaci SQuirreL SQL. V levé části okna, vyberte **ovladače**.

    ![Karta ovladače na levé straně okna](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Z ikon v horní části **ovladače** dialogového okna, vyberte **+** ikona vytvoření ovladač.

    ![Ikony ovladače](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. V dialogovém okně Přidat ovladač, přidejte následující informace:

    * **Název**: Hive
    * **Příklad adresy URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Další třídy cestu**: pomocí tlačítka Přidat přidáte všechny dříve stáhli soubory jar
    * **Název třídy**: org.apache.hive.jdbc.HiveDriver

   ![Přidat dialog ovladače](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Klikněte na tlačítko **OK** nastavení uložte.

6. Na levé straně okna SQuirreL SQL vyberte **aliasy**. Klikněte **+** ikona vytvoření aliasu připojení.

    ![Přidat nový alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Použijte následující hodnoty **přidat Alias** dialogového okna.

    * **Název**: Hive s HDInsight

    * **Ovladač**: pomocí rozevíracího seznamu můžete vybrat **Hive** ovladače

    * **ADRESA URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

    * **Uživatelské jméno**: název účtu přihlášení clusteru pro HDInsight cluster. Výchozí formát je `admin`.

    * **Heslo**: heslo pro účet přihlášení clusteru.

 ![Přidat alias dialog](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Použití **Test** tlačítko ověřit, že připojení funguje. Když **připojení k: Hive s HDInsight** se zobrazí dialogové okno, vyberte **připojit** k provedení testu. Pokud je test úspěšný, zobrazí se **úspěšné připojení** dialogového okna. Pokud dojde k chybě, přečtěte si téma [Poradce při potížích s](#troubleshooting).

    Chcete-li uložit alias připojení, použijte **Ok** tlačítko v dolní části **přidat Alias** dialogového okna.

8. Z **připojit k** vyberte rozevírací seznam v horní části SQuirreL SQL **Hive s HDInsight**. Po zobrazení výzvy vyberte **připojit**.

    ![Dialogové okno připojení](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Jakmile budete připojeni, zadejte následující dotaz do dialogu dotazu SQL a pak vyberte **spustit** ikonu. V oblasti výsledky by měly zobrazit výsledky dotazu.

        select * from hivesampletable limit 10;

    ![Dialogové okno dotazu SQL, včetně výsledků](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Připojení z příkladu aplikace v Javě

Příklad použití klientskou sadou Java k dotazu Hive v HDInsight je k dispozici na [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postupujte podle pokynů v úložišti pro sestavení a spuštění ukázky.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Došlo k neočekávané chybě při pokusu o otevření připojení SQL

**Příznaky**: při připojování ke clusteru HDInsight, který je verze 3.3 nebo novější, může se zobrazit chyba, ke které došlo k neočekávané chybě. Trasování zásobníku pro tuto chybu začíná s následujícími řádky:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Příčina**: Tato chyba je způsobena starší verzi commons codec.jar aplikace součástí SQuirreL.

**Rozlišení**: Chcete-li tuto chybu opravit, postupujte následovně:

1. Stažení souboru jar commons kodek z vašeho clusteru HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Ukončete SQuirreL a pak přejděte do adresáře, kde je nainstalován SQuirreL ve vašem systému. V adresáři SquirreL pod `lib` adresář, nahraďte existující codec.jar commons s tím se stáhne z clusteru HDInsight.

3. Restartujte SQuirreL. Chyba by už není k dispozici při připojování k Hive v HDInsight.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak použít pro práci se Hive JDBC, použijte následující odkazy a prozkoumejte další možnosti, jak pracovat s Azure HDInsight.

* [Vizualizace dat Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Hadoopu pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md)
* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
