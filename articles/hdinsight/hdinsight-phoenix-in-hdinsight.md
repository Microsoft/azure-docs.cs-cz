---
title: Apache Phoenix ve službě HDInsight – Azure HDInsight
description: Přehled Apache Phoenix
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: f98021d1e94b3796b2aeb6ba2e883e4e1380b8ca
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504328"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix ve službě Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) je open source vysoce paralelní relační databázová vrstva založená na [Apache HBA](hbase/apache-hbase-overview.md). Phoenix umožňuje používat dotazy podobné SQL přes HBA. V Phoenixu používá ovladače JDBC pod tím, že uživatelům umožňuje vytvářet, odstraňovat, měnit tabulky, indexy, zobrazení a sekvence a Upsert řádky, a to samostatně a hromadně. Phoenixu používá ke kompilaci dotazů nativní kompilaci noSQL a nepoužívá MapReduce k vytváření aplikací s nízkou latencí nad adaptéry HBA. Phoenix přidává spoluprocesory k podpoře spouštění kódu zadaného klientem v adresním prostoru serveru a provádění kódu společně umístěného s daty. Tento přístup minimalizuje přenos dat mezi klientem a serverem.

Apache Phoenix otevře v nevývojářích dotazy na velké objemy dat, které místo programování můžou použít syntaxi podobnou SQL. Systém Phoenix je vysoce optimalizovaný pro adaptéry HBA, na rozdíl od jiných nástrojů, jako je [Apache Hive](hadoop/hdinsight-use-hive.md) a Apache Spark SQL. Výhodou pro vývojáře je psaní vysoce výkonných dotazů s mnohem menším kódem.

Když odešlete dotaz SQL, Phoenix zkompiluje dotaz do HBA nativních volání a spustí vyhledávání (nebo plán) paralelně pro optimalizaci. Tato vrstva abstrakce uvolňuje vývojáře z psaní úloh MapReduce, aby se místo toho zaměřil na obchodní logiku a pracovní postup jejich aplikace v oblasti velkých objemů dat v Phoenixu.

## <a name="query-performance-optimization-and-other-features"></a>Optimalizace výkonu dotazů a další funkce

Apache Phoenix přidává do HBA dotazy několik vylepšení výkonu a funkcí.

### <a name="secondary-indexes"></a>Sekundární indexy

HBA mají jeden index, který je lexikograficky seřazený podle primárního klíče řádku. K těmto záznamům lze přistupovat pouze prostřednictvím klíče řádku. Přístup k záznamům přes libovolný jiný sloupec, než je klíč řádku, vyžaduje při použití požadovaného filtru kontrolu všech dat. V sekundárním indexu jsou sloupce nebo výrazy indexované na klíč alternativního řádku a umožňují vyhledávání a prohledávání rozsahu tohoto indexu.

Pomocí příkazu vytvořte sekundární index `CREATE INDEX` :

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Tento přístup může přinést výrazné zvýšení výkonu při provádění jednoduchých indexovaných dotazů. Tento typ sekundárního indexu je **pokrývá index**, který obsahuje všechny sloupce zahrnuté v dotazu. Proto není vyhledávání v tabulce vyžadováno a index vyhovuje celému dotazu.

### <a name="views"></a>Zobrazení

Zobrazení v Phoenixu nabízí způsob, jak překonat omezení HBA, kde výkon začne snižovat při vytváření více než přibližně 100 fyzických tabulek. Zobrazení v Phoenixu umožňuje více *virtuálním tabulkám* sdílet jednu základní tabulku fyzických adaptérů HBA.

Vytvoření zobrazení v Phoenixu se podobá použití standardní syntaxe zobrazení SQL. Jedním rozdílem je, že můžete definovat sloupce pro zobrazení kromě sloupců zděděných z jeho základní tabulky. Můžete také přidat nové `KeyValue` sloupce.

Tady je například fyzická tabulka s názvem `product_metrics` s následující definicí:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definujte v této tabulce zobrazení s dalšími sloupci:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Chcete-li přidat další sloupce později, použijte `ALTER VIEW` příkaz.

### <a name="skip-scan"></a>Přeskočit kontrolu

Funkce Přeskočit kontrolu používá jeden nebo více sloupců složeného indexu k vyhledání jedinečných hodnot. Na rozdíl od prohledávání rozsahu je přeskočení kontroly implementovat kontrolu uvnitř řádku a výsledkem je [vyšší výkon](https://phoenix.apache.org/performance.html#Skip-Scan). Při kontrole se první odpovídající hodnota přeskočí spolu s indexem, dokud se nenajde další hodnota.

Přeskočení vyhledávání používá `SEEK_NEXT_USING_HINT` výčet filtru HBA. Pomocí funkce `SEEK_NEXT_USING_HINT` Přeskočit kontrolu uchovává informace o tom, která sada klíčů nebo rozsahy klíčů je prohledávána v jednotlivých sloupcích. Přeskočení vyhledávání pak převezme klíč, který byl předán během vyhodnocování filtru, a určí, zda se jedná o jednu z kombinací. V takovém případě vyhledávání Skip vyhodnocuje další nejvyšší klíč, na který se má přejít.

### <a name="transactions"></a>Transakce

I když adaptéry HBA poskytují transakce na úrovni řádků, v Phoenixu se integruje s [Tephra](https://tephra.io/) , aby bylo možné přidat Meziřádková a mezitabulková podpora transakcí s plnou sémantikou [kyselosti](https://en.wikipedia.org/wiki/ACID) .

Stejně jako u tradičních transakcí SQL vám transakce poskytované prostřednictvím Správce transakcí v Phoenixu umožňují zajistit úspěšné upserted atomické jednotky dat a vracet transakce, pokud se operace Upsert nezdaří v jakékoli tabulce s povolenými transakcemi.

Pokud chcete povolit transakce v Phoenixu, přečtěte si [dokumentaci Apache Phoenix transakce](https://phoenix.apache.org/transactions.html).

Chcete-li vytvořit novou tabulku s povolenými transakcemi, nastavte `TRANSACTIONAL` vlastnost na hodnotu `true` v `CREATE` příkazu:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Chcete-li změnit existující tabulku na transakční, použijte stejnou vlastnost v `ALTER` příkazu:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Transakční tabulku nejde přepnout zpátky na netransakční.

### <a name="salted-tables"></a>Nasolené tabulky

*Oblast serveru hotspotting* může nastat při zápisu záznamů s sekvenčními klíči do adaptérů HBA. I když máte ve vašem clusteru více serverů oblastí, budou se všechny zápisy objevovat pouze v jednom z nich. Tato koncentrace vytvoří problém hotspotting, kdy se místo úlohy zápisu do všech dostupných serverů oblastí zpracovává zatížení jenom jednou. Vzhledem k tomu, že každá oblast má předdefinovanou maximální velikost, když oblast dosáhne omezení velikosti, rozdělí se na dvě malé oblasti. Pokud k tomu dojde, jedna z těchto nových oblastí vezme všechny nové záznamy, stane se nové hotspoty.

Pro zmírnění tohoto problému a dosažení lepšího výkonu, předem rozdělené tabulky, aby se všechny servery oblastí používaly stejně. Phoenix poskytuje *nasolené tabulky*, transparentně přidávají dvoubajtové bajty do klíče řádku pro konkrétní tabulku. Tabulka je předem rozdělena na hranice bajtových bajtů, aby bylo zajištěno stejné rozdělení zatížení mezi servery oblastí během počáteční fáze tabulky. Tento přístup distribuuje zátěž pro zápis napříč všemi dostupnými servery oblastí a zlepšuje výkon při zápisu a čtení. Chcete-li nasoleit tabulku, určete `SALT_BUCKETS` vlastnost Table při vytvoření tabulky:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Povolení a ladění v Phoenixu s Apache Ambari

Cluster An HDInsight HBA zahrnuje [uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) pro provádění změn konfigurace.

1. Pokud chcete povolit nebo zakázat Phoenix a řídit nastavení časového limitu dotazů v Phoenixu, přihlaste se k webovému uživatelskému rozhraní Ambari ( `https://YOUR_CLUSTER_NAME.azurehdinsight.net` ) pomocí vašich uživatelských přihlašovacích údajů Hadoop.

2. V seznamu služeb v nabídce na levé straně vyberte **HBA** a pak vyberte kartu **Konfigurace** .

    ![Konfigurace pro adaptéry Apache Ambari](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Pokud chcete povolit nebo zakázat Phoenix a nastavit časový limit dotazu, najděte oddíl konfigurace **SQL pro Phoenix** .

    ![Oddíl konfigurace SQL pro Ambari Phoenix](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Viz také

* [Použití Apache Phoenix s clustery se systémem Linux v HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Použití Apache Zeppelin ke spouštění dotazů Apache Phoenix dotazů přes Apache HBA ve službě Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
