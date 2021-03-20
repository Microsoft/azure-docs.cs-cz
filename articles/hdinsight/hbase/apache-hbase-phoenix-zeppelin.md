---
title: Spouštění dotazů Apache Base v Azure HDInsight s Apache Phoenix
description: Naučte se používat Apache Zeppelin ke spouštění dotazů na bázi Apache Base v Phoenixu.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944775"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Použití Apache Zeppelin ke spouštění dotazů Apache Phoenix dotazů přes Apache HBA ve službě Azure HDInsight

Apache Phoenix je open source vrstva s vysokou paralelní relační databází postavená na adaptérech HBA. Phoenix umožňuje používat dotazy podobné SQL přes HBA. V Phoenixu používá ovladače JDBC pod tím, že vám umožní vytvářet, odstraňovat, měnit tabulky, indexy, zobrazení a sekvence SQL.  Pomocí Phoenix můžete také aktualizovat řádky jednotlivě a hromadně. Phoenix používá nativní kompilaci NOSQL namísto použití MapReduce ke kompilaci dotazů, což umožňuje vytváření aplikací s nízkou latencí nad adaptéry HBA.

Apache Zeppelin je open source webový Poznámkový blok, který umožňuje vytvářet dokumenty orientované na spolupráci pomocí interaktivních datových analýz a jazyků, jako jsou SQL a Scala. Pomáhá vývojářům dat &ch vědeckých pracovníků dat vyvíjet, organizovat, spouštět a sdílet kód pro manipulaci s daty. Umožňuje vizualizovat výsledky bez odkazování na příkazový řádek nebo nepotřebujete podrobnosti o clusteru.

Uživatelé HDInsight můžou použít Apache Zeppelin k dotazování na tabulky v Phoenixu. Apache Zeppelin je integrováno do clusteru HDInsight a neexistuje žádný další postup pro jeho použití. Jednoduše vytvoříte Zeppelin Poznámkový blok s překladačem JDBC a začnete psát dotazy SQL pro Phoenix.

## <a name="prerequisites"></a>Předpoklady

Cluster Apache HBA v HDInsight. Přečtěte si téma Začínáme [s Apache HBA](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření poznámky Apache Zeppelin

1. Nahraďte `CLUSTERNAME` názvem vašeho clusteru v následující adrese URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Pak zadejte adresu URL do webového prohlížeče. Zadejte své uživatelské jméno a heslo pro přihlášení ke clusteru.

1. Na stránce Zeppelin vyberte **vytvořit novou poznámku**.

    ![Zeppelin interaktivní dotazy HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. V dialogovém okně **vytvořit novou poznámku** zadejte nebo vyberte následující hodnoty:

    - Poznámka název: zadejte název poznámky.
    - Výchozí Interpret: v rozevíracím seznamu vyberte **JDBC** .

    Pak vyberte **vytvořit poznámku**.

1. Ujistěte se, že se v hlavičce poznámkového bloku zobrazuje stav připojeno. Je označený zelenou tečkou v pravém horním rohu.

    ![Stav poznámkového bloku Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stav poznámkového bloku Zeppelin")

1. Vytvořte tabulku HBA. Zadejte následující příkaz a stiskněte klávesu **SHIFT + ENTER**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Příkaz **% JDBC (Phoenix)** v prvním řádku oznamuje poznámkovému bloku, aby používal Interpret JDBC v Phoenixu.

1. Zobrazení vytvořených tabulek.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Vloží hodnoty do tabulky.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Dotaz na tabulku

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Odstraní záznam.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Přetáhněte tabulku.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Další kroky

- [Apache Phoenix teď podporuje Zeppelin ve službě Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Gramatika Apache Phoenix](https://phoenix.apache.org/language/index.html)