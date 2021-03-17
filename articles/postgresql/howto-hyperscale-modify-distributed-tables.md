---
title: Úprava distribuovaných tabulek – Citus (škálování) – Azure Database for PostgreSQL
description: Příkazy SQL pro vytváření a úpravu distribuovaných tabulek – Citus () pomocí Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026433"
---
# <a name="distribute-and-modify-tables"></a>Distribuce a úprava tabulek

## <a name="distributing-tables"></a>Distribuce tabulek

Chcete-li vytvořit distribuovanou tabulku, je nutné nejprve definovat schéma tabulky. K tomu můžete definovat tabulku pomocí příkazu [Create Table](http://www.postgresql.org/docs/current/static/sql-createtable.html) stejným způsobem jako s normální tabulkou PostgreSQL.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Dále můžete pomocí \_ funkce Vytvořit distribuovanou \_ tabulku () zadat sloupec distribuce tabulky a vytvořit pracovní proces horizontálních oddílů.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

Volání funkce informuje měřítko (Citus), že \_ tabulka událostí GitHubu by měla být distribuována do \_ sloupce ID úložiště (pomocí hodnoty hash hodnoty sloupce). Funkce také vytvoří horizontálních oddílů na pracovních uzlech pomocí \_ \_ \_ hodnot konfigurace faktoru citus. horizontálních oddílů Count a citus. horizontálních oddílů.

Vytvoří celkový počet citus. horizontálních oddílů \_ Count horizontálních oddílů, kde každý horizontálních oddílů vlastní část hodnoty hash a získá replikaci na základě výchozí \_ \_ hodnoty konfigurace faktoru replikace citus. horizontálních oddílů. Repliky horizontálních oddílů vytvořené v pracovním procesu mají stejné schéma tabulky, indexu a definice omezení jako tabulka v koordinátorovi. Po vytvoření replik uloží funkce v koordinátoru všechna distribuovaná metadata.

Každému vytvořenému horizontálních oddílů je přiřazeno jedinečné ID horizontálních oddílů a všechny jeho repliky mají stejné ID horizontálních oddílů. Horizontálních oddílů jsou reprezentovány v pracovním uzlu jako běžné tabulky PostgreSQL s názvem \' TableName \_ shardid \' , kde TableName je název distribuované tabulky a horizontálních oddílů ID je přiřazeno jedinečné ID. Můžete se připojit k Postgres instancí pracovního procesu a zobrazit nebo spustit příkazy u jednotlivých horizontálních oddílů.

Teď jste připraveni vložit data do distribuované tabulky a na ni spustit dotazy. Další informace o systému souborů UDF používaném v této části najdete v tématu [Table and horizontálních oddílů DDL](reference-hyperscale-functions.md#table-and-shard-ddl) reference.

### <a name="reference-tables"></a>Referenční tabulky

Výše uvedená metoda distribuuje tabulky do více vodorovných horizontálních oddílů.  Další možností je distribuovat tabulky do jednoho horizontálních oddílů a replikovat horizontálních oddílů do všech pracovních uzlů. Tabulky distribuované tímto způsobem se nazývají *referenční tabulky.* Slouží k ukládání dat, ke kterým musí být často přistupované více uzly v clusteru.

Mezi běžné kandidáty pro referenční tabulky patří:

-   Menší tabulky, které se musí spojit s většími distribuovanými tabulkami.
-   Tabulky v aplikacích s více klienty, které neobsahují sloupec ID tenanta, nebo které \' nesouvisí s klientem. (Nebo během migrace i u některých tabulek přidružených k tenantovi.)
-   Tabulky, které vyžadují jedinečná omezení napříč více sloupci a jsou dostatečně malé.

Předpokládejme například, že elektronického obchodování web s více klienty potřebuje Vypočítat daň z prodeje pro transakce v libovolném z jeho obchodů. Daňové informace \' nespecifické pro každého tenanta Dává smysl umístit ho do sdílené tabulky. Referenční tabulka orientované na nás může vypadat takto:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Nyní se dotazy, jako je například jedna z výpočetních daní pro nákupní košík, mohou připojit k `states` tabulce bez režie sítě a mohou do kódu stavu přidat cizí klíč pro lepší ověřování.

Kromě distribuce tabulky jako jediné replikované horizontálních oddílů se systém `create_reference_table` souborů UDF označí jako referenční tabulka v tabulkách metadat (Citus). Citus () automaticky provádí dvoufázové zápisy ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) pro úpravy tabulek označených tímto způsobem, které poskytují silné záruky konzistence.

Pokud máte distribuovanou tabulku s horizontálních oddílů počtem, můžete ji upgradovat tak, aby byla rozpoznanou referenční tabulkou, jako je tato:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Další příklad použití referenčních tabulek naleznete v [kurzu víceklientské databáze](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Distribuce dat koordinátora

Pokud je existující databáze PostgreSQL převedena na uzel koordinátora pro cluster s Citus (), je možné data v jejich tabulkách efektivně distribuovat a s minimálním přerušením na aplikaci.

`create_distributed_table`Funkce popsaná výše funguje na prázdných i neprázdných tabulkách a pro druhé IT automaticky distribuuje řádky tabulky v celém clusteru. Dozvíte se, že pokud kopíruje data podle přítomnosti zprávy, \" Všimněte si: kopírování dat z místní tabulky \. .. \" Například:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Zápisy v tabulce jsou při migraci dat zablokované a nevyřízené zápisy jsou zpracovávány jako distribuované dotazy po potvrzení funkce. (Pokud se funkce nezdařila, dotazy se znovu stanou místními.) Čtení může v normálním případě pokračovat a po potvrzení funkce se stanou distribuované dotazy.

Při distribuci tabulek a a B, kde A má cizí klíč na B, předá nejprve klíč cílové tabulky B. V nesprávném pořadí způsobí chybu:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Pokud není možné distribuovat ve správném pořadí, pak tyto cizí klíče vyřaďte, distribuujte tabulky a znovu vytvořte cizí klíče.

Při migraci dat z externí databáze, jako je například z Amazon RDS na Citus (Cloud), vytvořte nejprve distribuované tabulky Citus () `create_distributed_table` a potom data zkopírujte do tabulky.
Kopírování do distribuovaných tabulek zabraňuje nedostatku volného místa v uzlu Coordinator.

## <a name="colocating-tables"></a>Vyhledávání tabulek

Společné umístění znamená umístění uchovávání souvisejících informací na stejných počítačích. Umožňuje efektivní dotazy a přitom využívá horizontální škálovatelnost pro celou datovou sadu. Další informace najdete v tématu společné [umístění](concepts-hyperscale-colocation.md).

Tabulky se společně nacházejí ve skupinách. Chcete-li ručně řídit přiřazování skupin se stejným umístěním v tabulce, použijte volitelný `colocate_with` parametr `create_distributed_table` . Pokud nebudete \' se starat o společné \' umístění tabulky, tento parametr vynechejte. Použije se výchozí hodnota `'default'` , která seskupí tabulku do jiné výchozí společné tabulky, která má stejný typ distribučního sloupce, horizontálních oddílů počet a faktor replikace. Pokud chcete toto implicitní umístění přerušit nebo aktualizovat, můžete použít `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Pokud nová tabulka není v relaci s ostatními uživateli ve vaší implicitní skupině souběžného umístění, zadejte `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Rozdělení nesouvisejících tabulek do jejich vlastních skupin kolokace bude zlepšit [horizontálních oddílů](howto-hyperscale-scale-rebalance.md) výkon, protože horizontálních oddílů ve stejné skupině musí být přesunuty dohromady.

Pokud se tabulky skutečně vztahují (například když budou spojeny), může být vhodné je explicitně vyhledat. Přínosy vhodného umístění jsou důležitější než jakákoli režie na nové vyrovnávání zatížení.

Chcete-li explicitně vyhledat více tabulek, rozdělte je a potom umístěte ostatní do své skupiny kolocation. Například:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Informace o skupinách kolokace jsou uloženy v tabulce [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) , zatímco [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) odhalí, které tabulky jsou přiřazeny k jednotlivým skupinám.

## <a name="dropping-tables"></a>Rušení tabulek

Distribuované tabulky můžete odebrat pomocí příkazu standardní PostgreSQL DROP TABLE. Stejně jako u běžných tabulek odstraní odkládací tabulka všechny indexy, pravidla, triggery a omezení, která existují pro cílovou tabulku. Kromě toho také horizontálních oddílů na pracovních uzlech a vyčistí svá metadata.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Úprava tabulek

Citus (AutoScale) automaticky šíří mnoho druhů příkazů DDL.
Změna distribuované tabulky v uzlu koordinátora bude aktualizovat horizontálních oddílů na pracovních procesech. Jiné příkazy DDL vyžadují ruční šíření a některé jiné jsou zakázané, například ty, které by změnily distribuční sloupec.
Pokus o spuštění příkazu DDL, který není způsobilý pro automatické šíření, vyvolá chybu a ponechá tabulky v uzlu koordinátora beze změny.

Tady je odkaz na kategorie příkazů DDL, které se šíří.
Automatické šíření lze povolit nebo zakázat s [parametrem konfigurace](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean) .

### <a name="addingmodifying-columns"></a>Přidávání a změny sloupců

Citus) šíří většinu příkazů [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) automaticky. Přidávání sloupců nebo změna jejich výchozích hodnot funguje stejně jako v databázi PostgreSQL v jednom počítači:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Významné změny stávajícího sloupce, jako je přejmenování nebo změna jeho datového typu, jsou velmi přesné. Datový typ [distribučního sloupce](concepts-hyperscale-nodes.md#distribution-column) však nelze změnit.
V tomto sloupci se určuje, jak se budou data tabulky distribuovat přes cluster Citus (), a změna jejího datového typu by vyžadovala přesun dat.

Pokus o provedení této akce způsobí chybu:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Přidávání a odebírání omezení

Používání Citus () vám umožňuje nadále využívat zabezpečení relační databáze, včetně omezení databáze (viz [dokumentace](https://www.postgresql.org/docs/current/static/ddl-constraints.html)k PostgreSQL).
Vzhledem k povaze distribuovaných systémů nebude Citus) křížově odkazovat omezení jedinečnosti ani referenční integritu mezi pracovními uzly.

Pokud chcete nastavit cizí klíč mezi společně umístěnými distribuovanými tabulkami, vždy do klíče zahrňte distribuční sloupec. Zahrnutím distribučního sloupce může být vytvoření složeného klíče.

Cizí klíče mohou být vytvořeny v těchto situacích:

-   mezi dvěma místními (nedistribuovanými) tabulkami,
-   mezi dvěma referenčními tabulkami,
-   mezi dvěma společně [umístěnými](concepts-hyperscale-colocation.md) distribuovanými tabulkami, pokud klíč zahrnuje distribuční sloupec, nebo
-   jako distribuovaná tabulka odkazující na [referenční tabulku](concepts-hyperscale-nodes.md#type-2-reference-tables)

Cizí klíče z referenčních tabulek do distribuovaných tabulek nejsou podporovány.

> [!NOTE]
>
> Primární klíče a omezení jedinečnosti musí zahrnovat sloupec distribuce. Přidáním do nedistribučního sloupce se vygeneruje chyba.

Tento příklad ukazuje, jak vytvořit primární a cizí klíče v distribuovaných tabulkách:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Podobně zahrňte distribuční sloupec v omezeních jedinečnosti:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Omezení not-null lze použít pro libovolný sloupec (Distribution nebo not), protože nevyžadují žádná vyhledávání mezi zaměstnanci.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Použití neplatných omezení

V některých situacích může být užitečné vynutit omezení pro nové řádky, zatímco stávající nevyhovující řádky zůstanou beze změny. Citus) podporuje tuto funkci pro kontrolní omezení a cizí klíče pomocí PostgreSQL \' s, \" které není platné \" označení omezení.

Zvažte například aplikaci, která ukládá profily uživatelů v [referenční tabulce](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

V průběhu času si představte, že do tabulky se nekopírují některé jiné než adresy.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Doporučujeme, abyste adresy ověřili, ale PostgreSQL obvykle neumožňuje přidat omezení CHECK, které se pro existující řádky nezdařilo. Nicméně omezení *does* neplatí jako neplatné:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Nové řádky jsou nyní chráněny.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Později se během období mimo špičku může správce databáze pokusit opravit chybné řádky a omezení znovu ověřit.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

V dokumentaci k PostgreSQL najdete další informace o omezení neplatného a ověřit v části [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Přidávání/odebírání indexů

Citus podporuje přidávání a odebírání [indexů](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Přidání indexu zabere zámek proti zápisu, což může být nežádoucí v rámci záznamu s více klienty \" . \" Chcete-li minimalizovat prostoje aplikace, vytvořte místo toho index [současně](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Tato metoda vyžaduje více celkové práce než standardní sestavení indexu a dokončení trvá déle. Vzhledem k tomu, že umožňuje normální operace pokračovat v době, kdy je index sestavený, je tato metoda užitečná pro přidávání nových indexů v produkčním prostředí.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
