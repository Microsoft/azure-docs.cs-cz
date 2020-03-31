---
title: Spouštění dotazů Apache Base v Azure HDInsight u Apache Phoenixu
description: Přečtěte si, jak používat Apache Zeppelin ke spouštění dotazů Apache Base s Phoenixem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392238"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Použití Apache Zeppelin ke spouštění dotazů Apache Phoenix přes Apache HBase v Azure HDInsight

Apache Phoenix je open source, masivně paralelní relační databázová vrstva postavená na HBase. Phoenix umožňuje používat SQL jako dotazy přes HBase. Phoenix používá jdbc ovladače pod vám umožní vytvářet, mazat, měnit SQL tabulky, indexy, zobrazení a sekvence.  Můžete také použít Phoenix aktualizovat řádky jednotlivě a hromadně. Phoenix používá nativní kompilaci NOSQL spíše než pomocí MapReduce ke kompilaci dotazů, což umožňuje vytváření aplikací s nízkou latencí nad HBase.

Apache Zeppelin je otevřený webový notebook, který umožňuje vytvářet dokumenty založené na datech a spolupráci pomocí interaktivní analýzy dat a jazyků, jako jsou SQL a Scala. Pomáhá vývojářům dat & datových vědců vyvíjet, organizovat, spouštět a sdílet kód pro manipulaci s daty. Umožňuje vizualizovat výsledky bez odkazu na příkazový řádek nebo potřebujete podrobnosti clusteru.

Uživatelé HDInsight mohou používat Apache Zeppelin k dotazování tabulek Phoenix. Apache Zeppelin je integrován s HDInsight clusterem a neexistují žádné další kroky k jeho použití. Jednoduše vytvořte notebook Zeppelin s interpretem JDBC a začněte psát své dotazy Phoenix SQL

## <a name="prerequisites"></a>Požadavky

Cluster Apache HBase na HDInsight. Viz [Začínáme s Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření poznámky Apache Zeppelin

1. Nahraďte `CLUSTERNAME` název clusteru v `https://CLUSTERNAME.azurehdinsight.net/zeppelin`následující adrese URL . Poté zadejte adresu URL do webového prohlížeče. Zadejte své přihlašovací uživatelské jméno a heslo pro přihlášení k clusteru.

1. Na stránce Zeppelin vyberte **Vytvořit novou poznámku**.

    ![HDInsight Interaktivní dotaz zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. V dialogovém okně **Vytvořit novou poznámku** zadejte nebo vyberte následující hodnoty:

    - Poznámka název: Zadejte název poznámky.
    - Výchozí překladač: V rozevíracím seznamu vyberte **jdbc.**

    Pak vyberte **Vytvořit poznámku**.

1. Ujistěte se, že záhlaví poznámkového bloku zobrazuje připojený stav. Je označena zelenou tečkou v pravém horním rohu.

    ![Stav notebooku Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stav notebooku Zeppelin")

1. Vytvořte tabulku HBase. Zadejte následující příkaz a stiskněte **Shift + Enter**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Příkaz **%jdbc(phoenix)** v prvním řádku říká poznámkovému bloku, aby používal překladač Phoenix JDBC.

1. Zobrazení vytvořených tabulek.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Vložte hodnoty do tabulky.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Dotaz na tabulku.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Odstranit záznam.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Odhoďte stůl.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Další kroky

- [Apache Phoenix teď podporuje Zeppelin v Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix gramatika](https://phoenix.apache.org/language/index.html)
