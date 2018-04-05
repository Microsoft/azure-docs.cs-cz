---
title: Dotaz Hive prostřednictvím ovladač JDBC - Azure HDInsight | Microsoft Docs
description: Ovladač JDBC z aplikace Java použijte k odesílání dotazů Hive k systému Hadoop v HDInsight. Připojte prostřednictvím kódu programu a z klienta SQuirrel SQL.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/02/2018
ms.author: larryfr
ms.openlocfilehash: 876d6169f1ecb2f9cdecc59f3f7c8d0a82a8fe7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Dotaz Hive prostřednictvím ovladač JDBC v HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Naučte se používat k odesílání dotazů Hive k systému Hadoop v prostředí Azure HDInsight ovladač JDBC z aplikace Java. Informace v tomto dokumentu ukazuje, jak se připojit prostřednictvím kódu programu a z klienta SQuirrel SQL.

Další informace o rozhraní JDBC Hive naleznete v tématu [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Požadavky

* Hadoop v clusteru HDInsight.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight 3.3 vyřazení](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL je JDBC klientské aplikace.

* [Java Developer Kit (JDK) verze 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) nebo vyšší.

* [Apache Maven](https://maven.apache.org). Maven je systém pro projekty Java, který se používá v projektu spojené s tímto článkem sestavení projektu.

## <a name="jdbc-connection-string"></a>Připojovací řetězec JDBC

JDBC připojení ke clusteru služby HDInsight v Azure jsou vytvářeny více než 443, a provoz se zabezpečené pomocí protokolu SSL. Veřejné brány, kterou clustery nacházejí za přesměruje na port, který HiveServer2 ve skutečnosti naslouchá na provoz. Následující připojovací řetězec ukazuje formát, který má používat pro HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Parametr `CLUSTERNAME` nahraďte názvem vašeho clusteru HDInsight.

## <a name="authentication"></a>Authentication

Při vytváření připojení, musíte použít název správce clusteru HDInsight a heslo k ověření clusteru brány. Při připojení z klientů JDBC, jako je SQuirreL SQL, musíte zadat jméno správce a heslo v nastavení klienta.

Z aplikace Java musíte použít přihlašovací jméno a heslo při navazování připojení. Například následující kód Java otevře nové připojení pomocí připojovacího řetězce, jméno správce a heslo:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Spojte se s klienta SQuirreL SQL

SQuirreL SQL je klient JDBC, který umožňuje vzdáleně spouštět dotazy Hive k vašemu clusteru HDInsight. Následující postup předpokládá, že jste již nainstalovali SQuirreL SQL.

1. Vytvořte adresář, který obsahuje soubory. Například, `mkdir hivedriver`.

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

    Nahraďte `USERNAME` s názvem účtu uživatele SSH pro cluster. Nahraďte `CLUSTERNAME` s názvem clusteru HDInsight.

3. Spusťte aplikaci SQuirreL SQL. V levé části okna vyberte **ovladače**.

    ![Karta ovladače na levé straně okna](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Z ikony v horní části **ovladače** dialogovém okně, vyberte **+** vytvořte ovladač.

    ![Ikony ovladače](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. V dialogovém okně Přidat ovladač přidejte následující informace:

    * **Název**: Hive
    * **Příklad adresy URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Navíc cesty tříd**: přidání všech souborů jar předtím stáhli pomocí tlačítka Přidat
    * **Class Name**: org.apache.hive.jdbc.HiveDriver

   ![Přidat dialogové okno ovladače](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Klikněte na tlačítko **OK** uložit tato nastavení.

6. Na levé straně okna SQuirreL SQL, vyberte **aliasy**. Klikněte **+** ikonu vytvořit alias připojení.

    ![Přidat nový alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Použijte následující hodnoty pro **přidat Alias** dialogové okno.

    * **Název**: Hive v HDInsight

    * **Ovladač**: pomocí rozevíracího seznamu vyberte **Hive** ovladačů

    * **ADRESA URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

    * **Uživatelské jméno**: název clusteru přihlašovací účet pro váš cluster HDInsight. Výchozí hodnota je `admin`.

    * **Heslo**: heslo pro účet přihlášení clusteru.

 ![alias dialogové okno Přidání](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Použití **Test** tlačítko ověřte, že připojení funguje. Když **připojit k: Hive v HDInsight** otevře se dialogové okno, vyberte **Connect** k provedení testu. Pokud je test úspěšný, zobrazí **úspěšné připojení** dialogové okno. Pokud dojde k chybě, projděte si téma [Poradce při potížích s](#troubleshooting).

    Chcete-li uložit alias připojení, použijte **Ok** tlačítko v dolní části **přidat Alias** dialogové okno.

8. Z **připojit k** rozevírací seznam v horní části SQuirreL SQL, vyberte **Hive v HDInsight**. Po zobrazení výzvy vyberte **Connect**.

    ![Dialogové okno připojení](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Po připojení, zadejte následující dotaz do dialogu dotaz SQL a potom vyberte **spustit** ikonu. Oblasti výsledky by měl zobrazit výsledky dotazu.

        select * from hivesampletable limit 10;

    ![Dialogové okno dotazu SQL, včetně výsledků](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Připojení z příklad aplikace Java

Příklad použití Java klienta tak, aby dotaz Hive v HDInsight je k dispozici na [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postupujte podle pokynů v úložišti sestavení a spuštění ukázky.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Došlo k neočekávané chybě při pokusu o otevření připojení k SQL došlo k chybě.

**Příznaky**: při připojování ke clusteru služby HDInsight, který je verze 3.3 nebo novější, můžete obdržet chybu, ke které došlo k neočekávané chybě. Trasování zásobníku pro tuto chybu začíná následující řádky:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Příčina**: Tato chyba je způsobená starší verzi commons codec.jar aplikace součástí SQuirreL.

**Řešení**: odstranění této chyby, použijte následující postup:

1. Stáhněte si soubor jar commons kodeků z clusteru HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Ukončete SQuirreL a pak přejděte do adresáře, kde je nainstalován SQuirreL ve vašem systému. V adresáři SquirreL pod `lib` directory nahradit existující codec.jar commons s ten stažený z clusteru HDInsight.

3. Restartujte SQuirreL. Chyba provedeno už při připojování k Hive v HDInsight.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití JDBC pro práci s Hive, pomocí následujících odkazů a prozkoumejte další způsoby, jak pracovat s Azure HDInsight.

* [Vizualizovat data Hive s Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace interaktivní dotazu Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Zeppelin ke spouštění dotazů Hive v Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení aplikace Excel do HDInsight pomocí ovladače ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení aplikace Excel k systému Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md)
* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
