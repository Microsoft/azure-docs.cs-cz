---
title: Apache Phoenix v HDInsight – Azure HDInsight
description: ''
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: d86600dd000d3e9c71a38b632aa75e82239401dd
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104575"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix ve službě HDInsight

[Apache Phoenix](http://phoenix.apache.org/) je vrstva masivně paralelní relační databáze založená na open source [HBase](hbase/apache-hbase-overview.md). Phoenix umožňuje používat dotazy na podobném SQL nad HBase. Phoenix používá ovladače JDBC pod umožňující uživatelům vytvořit, odstranit, změnit tabulek, indexů, zobrazení a pořadí a upsert řádky SQL jednotlivě a hromadně. Phoenix používá nativní kompilace noSQL místo použití prostředí MapReduce ke kompilaci dotazů, umožňují vytvářet aplikace s nízkou latencí nad HBase. Phoenix přidá coprocessors k podpoře spouštění v adresním prostoru serveru klientem poskytnutý kód spouští kód umístěny společně s daty. Tento přístup minimalizuje přenosu dat klienta nebo serveru.

Apache Phoenix otevře velkých objemů dat pro nevývojáře, kteří můžou využívat syntaxe pro SQL místo programování. Phoenix je vysoce optimalizovaných pro HBase, na rozdíl od jiných nástrojů, jako [Hive](hadoop/hdinsight-use-hive.md) a Spark SQL. Výhoda pro vývojáře je zápis vysoce výkonných dotazů s mnohem menším množstvím kódu.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Když odešlete dotaz SQL, Phoenix zkompiluje dotaz pro nativní volání HBase a spustí skener (nebo plán) paralelní optimalizace. Tato vrstva abstrakce uvolní vývojářům od vytváření úloh MapReduce, místo toho se soustředit na obchodní logiku a pracovní postup jejich aplikaci kolem Phoenix pro velké objemy dat úložiště.

## <a name="query-performance-optimization-and-other-features"></a>Optimalizace výkonu dotazů a další funkce

Apache Phoenix přidá několik vylepšení výkonu a funkcí do dotazů HBase.

### <a name="secondary-indexes"></a>Sekundární indexy

HBase obsahuje jeden index, který je lexikograficky seřazená podle klíče řádku primární. Tyto záznamy jsou přístupné pouze prostřednictvím klíč řádku. Přístup k záznamy přes všechny sloupce kromě klíč řádku vyžaduje prohledávání všech dat při použití požadovaný filtr. V sekundárním indexu sloupce nebo výrazy, které jsou indexované formou klíčem řádku alternativní umožňuje vyhledávání a rozsah kontroly indexu.

Vytvořit sekundární index s `CREATE INDEX` příkaz:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Tento přístup může přinést zvýšení výkonu nad spouštěním dotazů jedním indexovat. Tento typ sekundární index je **pokrývající index**, který obsahuje všechny sloupce v dotazu. Proto není potřeba prohledávání tabulky a index splňuje celý dotaz.

### <a name="views"></a>Zobrazení

Phoenix zobrazení poskytují způsob, jak překonat omezení HBase, kde výkon začne snižovat, když vytvoříte více než 100 fyzické tabulky. Povolit více zobrazení Phoenix *virtuální tabulky* sdílení jedné podkladové tabulky HBase fyzické.

Vytvoření zobrazení Phoenix je podobný pomocí standardní syntaxe zobrazení SQL. Jedním rozdílem je, že můžete definovat sloupce pro zobrazení, kromě sloupců, zděděno z jeho základní tabulky. Můžete také přidat nové `KeyValue` sloupce.

Například tady je fyzický tabulku s názvem `product_metrics` s následující definice:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definujte zobrazení v této tabulce při další sloupce:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Chcete-li později přidat další sloupce, použijte `ALTER VIEW` příkazu.

### <a name="skip-scan"></a>Přeskočit kontrolu

Přeskočit kontrolu používá k nalezení jedinečných hodnot jednoho nebo více sloupců složeném indexu. Na rozdíl od kontrolu rozsahu přeskočit kontroly implementuje uvnitř řádku skenování, což má za následek [vyšší výkon](http://phoenix.apache.org/performance.html#Skip-Scan). Při hledání, se přeskočí první odpovídající hodnotu spolu s index dokud není nalezena hodnota dalšího.

Přeskočit kontrolu používá `SEEK_NEXT_USING_HINT` výčtu filtru HBase. Pomocí `SEEK_NEXT_USING_HINT`, kontrolu přeskočit uchovává informace o které sadu klíčů nebo rozsahy klíčů, jsou prohledávána v každém sloupci. Přeskočení kontroly potom trvá klíč, který byl předán během vyhodnocení filtru a určuje, zda je jeden z kombinace. Pokud ne, vyhodnotí další nejvyšší klíč pro přechod na kontrolu přeskočit.

### <a name="transactions"></a>Transakce

HBase poskytuje transakcí na úrovni řádků, Phoenix integruje [Tephra](http://tephra.io/) přidává různé řádků a křížovou tabulku transakce s plnou [kyseliny](https://en.wikipedia.org/wiki/ACID) sémantiku.

Jako s tradiční SQL transakce, transakce, které jsou k dispozici prostřednictvím Správce transakcí Phoenix umožňují Ujistěte se, že atomickou jednotku dat se úspěšně upserted, transakce vrácení zpět, pokud operace upsert je neúspěšná na jakoukoli tabulku podporou transakcí.

K povolení Phoenix transakcí, najdete v článku [dokumentaci Apache Phoenix transakce](http://phoenix.apache.org/transactions.html).

Chcete-li vytvořit novou tabulku s transakcemi povolena, nastavte `TRANSACTIONAL` vlastnost `true` v `CREATE` – příkaz:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Změnit existující tabulku využívat transakce, použijte stejnou vlastnost v `ALTER` – příkaz:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Transakční tabulku nelze přepnout zpět na právě není transakční.

### <a name="salted-tables"></a>Solené tabulky

*Oblast server hotspotting* může dojít, pokud zápis záznamů s sekvenční klíči pro HBase. Když jste více oblastní servery v clusteru, se zápisů vyskytnou u jen jeden. Toto spojení vytvoří hotspotting problém, kde místo distribuována na všech serverech k dispozici oblast zápisu zátěže jen jeden zpracovává zatížení. Protože každá oblast má předdefinovanou maximální velikost, oblast dosáhne tohoto limitu velikosti, rozdělí se na dvě oblasti malé. Pokud k tomu dojde, přijímá mezi tyto nové oblasti všechny nové záznamy, stane hotspot nové.

K zmírnění tohoto problému a dosahovat lepšího výkonu, předem rozdělení tabulky tak, aby všechny servery oblasti se používají stejnou měrou. Phoenix poskytuje *řetězce Salt tabulky*, transparentně přidání "solení" bajtů do klíče řádku pro konkrétní tabulku. V tabulce je předem rozdělit na hranice řetězce salt bajtů do stejné rozložení zatížení mezi servery oblasti v počáteční fázi tabulky. Tento přístup distribuuje zatížení zápisu na všech serverech k dispozici oblast, zlepšení zápisu a čtení výkonu. Salt tabulku, zadejte `SALT_BUCKETS` tabulky vlastnosti, když se vytvoří v tabulce:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-ambari"></a>Povolit a vyladění Phoenixu s Ambari

Cluster HDInsight HBase obsahuje [uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) k provádění změn konfigurace.

1. K povolení nebo zakázání Phoenix a řídit nastavení časového limitu dotazu pro Phoenix, přihlaste se k webovému uživatelskému rozhraní Ambari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) pomocí svých přihlašovacích údajů uživatele Hadoop.

2. Vyberte **HBase** ze seznamu služeb v nabídce vlevo vyberte **Configs** kartu.

    ![Konfigurace Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Najít **Phoenix SQL** konfiguračního oddílu pro povolení nebo zakázání phoenix a nastavte časový limit dotazu.

    ![Oddíl konfigurace Ambari Phoenix SQL](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Další informace najdete v tématech

* [Použití Apache Phoenixu s clustery se systémem Linux HBase v HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
