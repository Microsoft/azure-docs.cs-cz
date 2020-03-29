---
title: Apache Phoenix v HDInsight - Azure HDInsight
description: Přehled Apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435502"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix v Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) je open source, masivně paralelní relační databázová vrstva postavená na [Apache HBase](hbase/apache-hbase-overview.md). Phoenix umožňuje používat dotazy podobné SQL přes HBase. Phoenix používá ovladače JDBC pod umožnit uživatelům vytvářet, mazat, měnit sql tabulky, indexy, zobrazení a sekvence a upsert řádky jednotlivě a hromadně. Phoenix používá noSQL nativní kompilaci spíše než pomocí MapReduce ke kompilaci dotazů, což umožňuje vytváření aplikací s nízkou latencí nad HBase. Phoenix přidává koprocesory pro podporu spuštění kódu dodaného klientem v adresním prostoru serveru, provádění kódu spoluumístěné s daty. Tento přístup minimalizuje přenos dat klient/server.

Apache Phoenix otevírá dotazy na velké objemy dat pro nevývojáře, kteří mohou používat syntaxi podobné SQL, spíše než programování. Phoenix je vysoce optimalizovaný pro HBase, na rozdíl od jiných nástrojů, jako je [Apache Hive](hadoop/hdinsight-use-hive.md) a Apache Spark SQL. Výhodou pro vývojáře je psaní vysoce výkonných dotazů s mnohem méně kódu.

Při odeslání dotazu SQL Phoenix zkompiluje dotaz na nativní volání HBase a spustí prohledávací (nebo plán) paralelně pro optimalizaci. Tato vrstva abstrakce osvobozuje vývojáře od psaní mapreduce úloh, zaměřit se místo toho na obchodní logiku a pracovní postup jejich aplikace kolem phoenix je velké úložiště dat.

## <a name="query-performance-optimization-and-other-features"></a>Optimalizace výkonu dotazů a další funkce

Apache Phoenix přidává několik vylepšení výkonu a funkcí do dotazů HBase.

### <a name="secondary-indexes"></a>Sekundární indexy

HBase má jeden index, který je lexicographically seřazené na primární řádek klíč. Tyto záznamy lze přistupovat pouze prostřednictvím klíče řádku. Přístup k záznamům prostřednictvím libovolného sloupce kromě klíče řádku vyžaduje skenování všech dat při použití požadovaného filtru. V sekundárním indexu tvoří sloupce nebo výrazy, které jsou indexovány, klíč alternativního řádku, který umožňuje vyhledávání a prohledávací rozsah v tomto indexu.

Vytvořte sekundární index `CREATE INDEX` pomocí příkazu:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Tento přístup může přinést významné zvýšení výkonu oproti provádění dotazů s jedním indexem. Tento typ sekundárního indexu je **krycí index**obsahující všechny sloupce zahrnuté v dotazu. Proto není vyžadováno vyhledávání v tabulce a index splňuje celý dotaz.

### <a name="views"></a>Zobrazení

Phoenix zobrazení poskytují způsob, jak překonat omezení HBase, kde výkon začne degradovat při vytvoření více než asi 100 fyzických tabulek. Phoenix zobrazení umožňují více *virtuálních tabulek* sdílet jednu základní fyzické HBase tabulky.

Vytvoření zobrazení Phoenix je podobné použití standardní syntaxe zobrazení SQL. Jeden rozdíl je, že můžete definovat sloupce pro zobrazení, kromě sloupců zděděných ze základní tabulky. Můžete také přidat `KeyValue` nové sloupce.

Zde je například fyzická `product_metrics` tabulka s názvem s následující definicí:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definujte zobrazení nad touto tabulkou s dalšími sloupci:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Chcete-li později přidat `ALTER VIEW` další sloupce, použijte příkaz.

### <a name="skip-scan"></a>Přeskočit prohledávku

Přeskočit prohledání používá jeden nebo více sloupců složeného indexu k nalezení odlišných hodnot. Na rozdíl od rozsahu skenování, přeskočit skenování implementuje prohledávání v rámci řádku, což přináší [lepší výkon](https://phoenix.apache.org/performance.html#Skip-Scan). Při skenování je první odpovídající hodnota přeskočena spolu s indexem, dokud není nalezena další hodnota.

Přeskočení prohledávání používá `SEEK_NEXT_USING_HINT` výčet filtru HBase. Pomocí `SEEK_NEXT_USING_HINT`funkce přeskočit prohledává a sleduje, které sady klíčů nebo rozsahy klíčů jsou v každém sloupci vyhledávány. Přeskočit prohledání pak trvá klíč, který byl předán k němu během vyhodnocení filtru a určuje, zda je to jedna z kombinací. Pokud ne, přeskočení vyhodnotí další nejvyšší klíč, na který chcete přejít.

### <a name="transactions"></a>Transakce

Zatímco HBase poskytuje transakce na úrovni řádků, Phoenix integruje s [Tephra](https://tephra.io/) přidat cross-row a cross-table transakční podporu s plnou [acid](https://en.wikipedia.org/wiki/ACID) sémantiku.

Stejně jako u tradičních transakcí SQL transakce transakce poskytované prostřednictvím správce transakcí Phoenix umožňují zajistit atomická jednotka dat je úspěšně upserted, vrácení transakce, pokud operace upsert selže na libovolné tabulky s povolenou transakcí.

Chcete-li povolit transakce Phoenix, naleznete v [dokumentaci k transakcím Apache Phoenix](https://phoenix.apache.org/transactions.html).

Chcete-li vytvořit novou tabulku s `TRANSACTIONAL` povolenými `true` transakcemi, nastavte vlastnost v příkazu: `CREATE`

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Chcete-li změnit existující tabulku tak, aby byla `ALTER` transakční, použijte ve výpisu stejnou vlastnost:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Transakční tabulku nelze přepnout zpět na netransakční.

### <a name="salted-tables"></a>Slané stoly

*Oblast server hotspotting* může dojít při zápisu záznamů s sekvenční klíče HBase. I když můžete mít více serverů oblasti v clusteru, vaše zápisy jsou všechny dochází pouze na jeden. Tato koncentrace vytváří hotspotting problém, kde namísto úlohy zápisu distribuované napříč všechny servery dostupné oblasti, pouze jeden je zpracování zatížení. Vzhledem k tomu, že každá oblast má předdefinovanou maximální velikost, když oblast dosáhne tohoto limitu velikosti, je rozdělena do dvou malých oblastí. Když k tomu dojde, jedna z těchto nových oblastí převezme všechny nové záznamy a stane se novým hotspotem.

Chcete-li tento problém zmírnit a dosáhnout lepšího výkonu, předrozdělené tabulky tak, aby všechny servery oblasti jsou stejně používány. Phoenix poskytuje *slané tabulky*, transparentně přidáním solného bajtu do řádku klíče pro konkrétní tabulku. Tabulka je předem rozdělena na hranice bajtu soli, aby bylo zajištěno rovnoměrné rozložení zatížení mezi servery oblasti během počáteční fáze tabulky. Tento přístup distribuuje úlohy zápisu napříč všemi dostupnými oblastmi serverů, zlepšení výkonu zápisu a čtení. Chcete-li solit `SALT_BUCKETS` tabulku, zadejte vlastnost tabulky při vytvoření tabulky:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Povolit a naladit Phoenix s Apache Ambari

Cluster HDInsight HBase obsahuje [ui Ambari](hdinsight-hadoop-manage-ambari.md) pro provádění změn konfigurace.

1. Chcete-li povolit nebo zakázat phoenix a řídit phoenix dotaz nastavení časového limitu,`https://YOUR_CLUSTER_NAME.azurehdinsight.net`přihlaste se k ambari webové uživatelské rozhraní ( ) pomocí přihlašovacích údajů uživatele Hadoop.

2. Vyberte **HBase** ze seznamu služeb v levé nabídce a pak vyberte kartu **Configs.**

    ![Konfigurace Apache Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Najít **Phoenix SQL** konfigurační části povolit nebo zakázat phoenix a nastavte časový limit dotazu.

    ![Konfigurační oddíl Ambari Phoenix pro SQL](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Viz také

* [Používejte Apache Phoenix s linuxovými clustery HBase v HDInsightu](hbase/apache-hbase-query-with-phoenix.md)

* [Použití Apache Zeppelin ke spouštění dotazů Apache Phoenix přes Apache HBase v Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
