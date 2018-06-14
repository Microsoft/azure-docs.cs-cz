---
title: Apache Phoenix v HDInsight - Azure HDInsight | Microsoft Docs
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5d96b5656881815a82c89e0d159ba2bf556946b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407727"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix ve službě HDInsight

[Apache Phoenix](http://phoenix.apache.org/) je open source, vrstva massively parallel relační databáze založená na [HBase](hbase/apache-hbase-overview.md). Phoenix umožňuje používat dotazy podobné jazyku SQL nad HBase. Phoenix používá JDBC ovladače pod umožňuje uživatelům vytvořit, odstranit, změnit tabulky, indexy, zobrazení a pořadí a upsert řádky SQL jednotlivě a hromadně. Phoenix používá nativní kompilace noSQL, místo použití prostředí MapReduce zkompilovat dotazy, povolení vytváření aplikací s nízkou latencí nad HBase. Phoenix přidá coprocessors k podpoře spouštění klienta zadaný kód v adresním prostoru serveru, provádění kódu společně umístěné s daty. Tento postup minimalizuje přenos dat klienta nebo serveru.

Apache Phoenix otevře dotazy velkých objemů dat na jiný vývojáře, kteří můžete použít SQL syntaxe místo programování. Phoenix je vysoce optimalizovaný pro HBase, na rozdíl od jiných nástrojů, jako [Hive](hadoop/hdinsight-use-hive.md) a Spark SQL. Výhodou pro vývojáře je vysoce zápis dotazů původce s mnohem méně kódu.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Při odesílání dotazu SQL Phoenix zkompiluje dotaz pro nativní volání HBase a spustí kontrolu (nebo plán) paralelní k optimalizaci. Tuto vrstvu abstrakce uvolní vývojáři z zápis úloh MapReduce, místo toho se soustřeďte na obchodní logiky a pracovní postup jejich používání v celém Phoenix na velkých objemů dat úložiště.

## <a name="query-performance-optimization-and-other-features"></a>Optimalizace výkonu dotazu a další funkce

Apache Phoenix přidá několik vylepšení výkonu a funkce na dotazy, HBase.

### <a name="secondary-indexes"></a>Sekundární indexy

HBase obsahuje jeden index, který je lexicographically seřadit na řádek primární klíč. Tyto záznamy jsou přístupné pouze prostřednictvím klíč řádku. Přístup k záznamy prostřednictvím žádný sloupec než klíč řádku vyžaduje kontrolu všechna data při použití požadovaný filtr. V sekundární index sloupce nebo výrazy, které jsou indexované formou kontroluje klíčem alternativní řádek umožňuje vyhledávání a rozsah na tento index.

Vytvořit sekundární index s `CREATE INDEX` příkaz:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Tento přístup přispět k výraznému zvýšení výkonu přes zpracování indexované jedním dotazů. Tento typ sekundárního indexu **pokrývajících index**, obsahující všechny sloupce obsažena v dotazu. Proto vyhledávací tabulka není vyžadován a index splňuje celý dotaz.

### <a name="views"></a>Zobrazení

Phoenix zobrazení poskytují způsob, jak předcházet omezení HBase, kde výkon začne snižovat, když vytvoříte více než 100 tabulek fyzické. Phoenix zobrazení povolit více *virtuální tabulky* sdílení jedné podkladové fyzické tabulky HBase.

Vytvoření zobrazení Phoenix je podobná pomocí standardní syntaxe zobrazení SQL. Jeden rozdíl je, že můžete definovat sloupce pro zobrazení, kromě sloupce zděděno z jeho základní tabulky. Můžete také přidat nové `KeyValue` sloupce.

Například je zde fyzické tabulku s názvem `product_metrics` s následující definice:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definujte přes tuto tabulku se sloupci Další zobrazení:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Chcete-li přidat další sloupce později, použijte `ALTER VIEW` příkaz.

### <a name="skip-scan"></a>Přeskočit kontrolu

Přeskočit kontrolu používá jeden nebo více sloupců složený index k nalezení jedinečných hodnot. Na rozdíl od kontrolu rozsahu Přeskočit kontrolu implementuje intra řádek skenování, získávání [zvýšení výkonu](http://phoenix.apache.org/performance.html#Skip-Scan). Při kontrole, bylo přeskočeno první odpovídající hodnotu společně s index, dokud nebude nalezen další hodnotu.

Přeskočit kontrolu používá `SEEK_NEXT_USING_HINT` výčtu filtru HBase. Pomocí `SEEK_NEXT_USING_HINT`, Přeskočit kontrolu uchovává informace o, která sada klíče nebo rozsahy klíčů, jsou vyhledána v jednotlivých sloupcích. Možnost přeskočení kontroly pak provede klíč, který byl předán během vyhodnocení filtru a určuje, zda je jedna z kombinací. Pokud ne, Přeskočit kontrolu vyhodnotí přejít na další nejvyšší klíč.

### <a name="transactions"></a>Transakce

Zatímco HBase poskytuje transakce na úrovni řádků, Phoenix integruje s [Tephra](http://tephra.io/) přidání podpory transakce mezi řádků a křížové tabulky s úplným [kyseliny](https://en.wikipedia.org/wiki/ACID) sémantiku.

Jako s transakcemi tradiční SQL, transakcí, které jsou poskytované prostřednictvím Správce transakcí Phoenix umožňují Ujistěte se, že atomické jednotky dat úspěšně upserted, transakce vrácení zpět, pokud se upsert nezdaří na libovolnou povolenou transakce tabulku.

Pokud chcete povolit Phoenix transakce, najdete [dokumentaci Apache Phoenix transakce](http://phoenix.apache.org/transactions.html).

Chcete-li vytvořit novou tabulku s transakcemi povoleno, nastavte `TRANSACTIONAL` vlastnost `true` v `CREATE` příkaz:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Změna existující tabulky jako transakční, použijte stejnou vlastnost v `ALTER` příkaz:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Transakční tabulku nelze přepnout zpět na právě netransakční.

### <a name="salted-tables"></a>Solené tabulky

*Oblast serveru hotspotting* může dojít, když zápis záznamů s po sobě jdoucích klíče do HBase. Když jste více oblast serverů v clusteru, se vaše zápisy vyskytnou u pouze jeden. Tato koncentrace vytvoří hotspotting problém, kde místo zápisu úlohy probíhá distribuce napříč všemi servery oblasti k dispozici, pouze jeden, zpracovává zatížení. Vzhledem k tomu, že každá oblast má předdefinované maximální velikost, při oblast dosáhne tento limit pro velikost, je rozdělit na dvě malé oblasti. Pokud k tomu dojde, jednu z těchto nových oblastí trvá všechny nové záznamy vzniku nové aktivní bod.

K zmírnění tohoto problému a dosáhnout lepší výkon, předem rozdělení tabulky tak, aby všechny servery oblast stejně používají. Phoenix poskytuje *řetězce Salt tabulky*, transparentně přidávání solení bajtů do klíč řádku pro konkrétní tabulku. Tabulka je předem rozdělené na hranicích řetězce salt bajtů na stejné rozložení zatížení mezi servery oblast během počáteční fáze tabulky. Tento přístup rozděluje zatížení zápisu na všechny servery dostupné oblasti, vylepšení zápisu a čtení výkonu. Chcete-li salt tabulku, zadejte `SALT_BUCKETS` tabulky vlastnosti, když je vytvořen v tabulce:

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

## <a name="enable-and-tune-phoenix-with-ambari"></a>Povolení a ladit Phoenix pomocí Ambari

Zahrnuje clusteru služby HDInsight HBase [uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) pro provádění změn konfigurace.

1. Chcete povolit nebo zakázat Phoenix a řídit nastavení časového limitu dotazu na Phoenix, přihlaste se k webové uživatelské rozhraní Ambari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) pomocí svých přihlašovacích údajů uživatele Hadoop.

2. Vyberte **HBase** ze seznamu služeb v levé nabídce pak vyberte **konfigurací** kartě.

    ![Konfigurace Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Najít **Phoenix SQL** konfigurační oddíl povolit nebo zakázat phoenix a nastavit časový limit dotazu.

    ![Oddíl konfigurace Ambari Phoenix SQL](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Další informace najdete v tématech

* [Použití nástrojů Apache Phoenix s clustery se systémem Linux HBase v HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
