---
title: Osvědčené postupy načítání dat pro synapse fond SQL
description: Doporučení a optimalizace výkonu pro načítání dat pomocí synapse fondu SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 34a536ea535fa222340bd004253ee54b9c13bea9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441217"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Osvědčené postupy načítání dat pomocí synapse fondu SQL

V tomto článku se seznámíte s doporučeními a optimalizacemi výkonu pro načítání dat pomocí fondu SQL.

## <a name="preparing-data-in-azure-storage"></a>Příprava dat v Azure Storage

Pokud chcete minimalizovat latenci, najděte vrstvu úložiště a váš fond SQL.

Při exportu dat do formátu souboru ORC může dojít k chybám s nedostatkem paměti Java, pokud se zde nacházejí velké textové sloupce. Toto omezení můžete obejít tím, že importujete jen podmnožinu sloupců.

Všechny formáty souborů mají jiné výkonové charakteristiky. Pokud chcete docílit nejrychlejšího načtení, použijte komprimované textové soubory s oddělovači. Rozdíl mezi výkonem kódování UTF-8 a UTF-16 je minimální.

Velké komprimované soubory rozdělte do menších komprimovaných souborů.

## <a name="running-loads-with-enough-compute"></a>Dostatečné výpočetní prostředky pro načítání dat

Největší rychlosti při načítání dosáhnete, když budete spouštět vždy jen jednu úlohu načtení dat. Pokud to není proveditelné, spouštějte současně minimální počet zatížení. Pokud očekáváte velkou úlohu načítání, zvažte možnost škálovat svůj fond SQL před zatížením.

Pokud chcete spouštět načítání s odpovídajícími výpočetními prostředky, vytvořte uživatele načítání vyhrazené pro spouštění načítání. Klasifikujte každého uživatele načítání do konkrétní skupiny úloh. Pokud chcete spustit zátěž, přihlaste se jako jeden z uživatelů načítání a potom spusťte načtení. Zatížení se spouští se skupinou úloh uživatele.  

### <a name="example-of-creating-a-loading-user"></a>Příklad vytvoření uživatele načítání

Tento příklad vytvoří uživatele načítání klasifikovaného na konkrétní skupinu úloh. Prvním krokem je **připojení k předloze** a vytvoření přihlášení.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Připojte se ke fondu SQL a vytvořte uživatele. Následující kód předpokládá, že jste připojeni k databázi s názvem mySampleDataWarehouse. Ukazuje, jak vytvořit uživatele s názvem Loader a přidělí uživateli oprávnění k vytváření tabulek a načtení pomocí [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Pak klasifikuje uživatele na skupinu úloh dataloads s maximálními prostředky. 

```sql
   -- Connect to the SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
      MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```

Pokud chcete spustit zatížení s prostředky pro načtení skupiny úloh, přihlaste se jako zavaděč a spusťte zátěž.

## <a name="allowing-multiple-users-to-load-polybase"></a>Povolení načtení více uživatelů (základ)

Je často potřeba, aby data načetla více uživatelů do fondu SQL. Načítání s [Create Table jako Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (základ) vyžaduje oprávnění k řízení databáze.  Oprávnění CONTROL poskytuje přístup pro řízení ke všem schématům.

Pravděpodobně ale nebudete chtít, aby všichni uživatelé, kteří načítají data, měli oprávnění CONTROL pro přístup ke všem schématům. K omezení oprávnění slouží příkaz DENY CONTROL.

Představte si například schémata databáze schema_A pro oddělení A a schema_B pro oddělení B. Uživatelé databáze user_A a user_B budou uživateli pro načítání PolyBase v oddělení A, respektive oddělení B. Oba uživatelé mají k databázi udělená oprávnění CONTROL. Autoři schémat A a B nyní svá schémata uzamknou pomocí příkazu DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A a user_B jsou nyní uzamčeny ze schématu jiné oddělení.

## <a name="loading-to-a-staging-table"></a>Načítání do pracovní tabulky

Chcete-li dosáhnout nejrychlejší rychlosti načítání pro přesun dat do tabulky fondu SQL, načtěte data do pracovní tabulky.  Pracovní tabulku definujte jako haldu a jako způsob distribuce použijte kruhové dotazování (round robin).

Vezměte v úvahu, že načítání je obvykle proces se dvěma kroky, ve kterém jste nejprve načetli pracovní tabulku a pak do ní vložíte data do provozní tabulky fondu SQL. Pokud provozní tabulka používá k distribuci algoritmus hash, může být celková doba načtení a vložení dat kratší, než když k definici pracovní tabulky použijete distribuci hash.

Načítání do pracovní tabulky trvá déle, ale druhý krok, který spočívá ve vkládání řádků do provozní tabulky, nepřesouvá data prostřednictvím distribuce.

## <a name="loading-to-a-columnstore-index"></a>Načítání do indexu columnstore

Indexy columnstore vyžadují hodně paměti, aby mohly komprimovat data do vysoce kvalitních skupin řádků. Kvůli zajištění co nejlepší účinnosti komprese a indexování musí index columnstore do každé skupiny řádků zkomprimovat maximální počet 1 048 576 řádků.

V případě nedostatku paměti nemusí index columnstore dosahovat maximální míry komprese. V tomto scénáři se následně projeví výkon dotazů na efekty. Podrobné informace najdete v tématu [Optimalizace paměti pro columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Pokud chcete zajistit, aby měl nahrávající uživatel dostatek paměti pro dosažení maximální míry komprese, použijte uživatele načítání, kteří jsou členy střední nebo velké třídy prostředků.
- Načtěte dostatek dat pro úplně naplnění nových skupin řádků. Při hromadném načítání dat se každých 1 048 576 řádků zkomprimuje přímo do indexu columnstore jako kompletní skupina řádků. Při načítání méně než 102 400 řádků se řádky odesílají do tabulky deltastore, kde se řádky uchovávají v indexu B-stromu.

> [!NOTE]
> Pokud načtete příliš málo řádků, můžou se všechny směrovat do indexu deltastore a Nekomprimovat okamžitě do formátu columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zvýšit velikost dávky při použití rozhraní SqLBulkCopy API nebo BCP

Načtení pomocí příkazu COPY poskytne nejvyšší propustnost s fondem SQL. Pokud nemůžete použít kopírování pro načtení a musí používat [rozhraní SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), měli byste zvážit zvýšení propustnosti zvětšením velikosti dávky.

> [!TIP]
> Pro určení optimální kapacity velikosti dávky je velikost dávky mezi 100 až 1 milion řádků doporučeným směrným plánem.

## <a name="handling-loading-failures"></a>Zpracování chyb načítání

Načtení s použitím externí tabulky může selhat s chybou *Query aborted-- the maximum reject threshold was reached while reading from an external source* (Dotaz byl přerušen – při čtení z externího zdroje byla dosažena maximální prahová hodnota pro odmítnutí). Tato zpráva znamená, že vaše externí data obsahují nezapsané záznamy.

Záznam dat se považuje za nečistý, pokud splňuje jednu z následujících podmínek:

- Datové typy a počet sloupců se neshodují s definicemi sloupců externí tabulky.
- Data neodpovídají zadanému formátu externího souboru.

Pokud chcete nezapsané záznamy opravit, ujistěte se, že jsou definice formátů externí tabulky a externího souboru správné a že externí data těmto definicím odpovídají.

Pokud je podmnožina záznamů externích dat špinavá, můžete se rozhodnout tyto záznamy pro své dotazy odmítnout pomocí možností odmítnutí v části [vytvořit externí tabulku (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Vložení dat do provozní tabulky

Při jednorázovém načtení malé tabulky [příkazem INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nebo i při pravidelně se opakujícím načítání funkcí look-up pravděpodobně vystačíte s následujícím příkazem: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Vkládání jednotlivých záznamů ale není tak účinné jako hromadné načtení.

Pokud máte za den tisíce nebo více samostatných vložení, vytvořte z nich dávku, abyste je mohli načíst hromadně.  Vyvíjejte své procesy tak, aby samostatná vkládání připojovaly do souboru, a pak vytvořte další proces, který tento soubor bude pravidelně načítat.

## <a name="creating-statistics-after-the-load"></a>Vytvoření statistiky po načtení

Pro zlepšení výkonu dotazů je důležité vytvořit statistiku pro všechny sloupce všech tabulek po prvním načtení, nebo když v datech dojde k zásadnějším změnám. Vytváření statistik můžete provést ručně nebo můžete povolit [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Podrobné vysvětlení statistiky najdete v tématu [Statistika](sql-data-warehouse-tables-statistics.md). Následující příklad ukazuje, jak ručně vytvořit statistiku pro pět sloupců Customer_Speed tabulky.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Otočení klíčů úložiště (základ)

Osvědčeným postupem zabezpečení je pravidelně měnit přístupový klíč k úložišti objektů blob. Ke svému účtu úložiště objektů Blob máte dva klíče úložiště, abyste je mohli střídat.

Obměna klíčů účtu služby Azure Storage:

Pro každý účet úložiště, jehož klíč se změnil, vydejte příkaz [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Příklad:

Původní klíč je vytvořený.

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Proveďte otočení klíče 1 do klíče 2.

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

V příslušných externích zdrojích dat se nevyžadují žádné další změny.

## <a name="next-steps"></a>Další kroky

- Další informace o příkazu COPY nebo o základu při navrhování procesu extrakce, načítání a transformace (ELT) najdete v tématu [design ELT for Azure synapse Analytics](design-elt-data-loading.md).
- Pro kurz načítání [použijte příkaz Copy k načtení dat z úložiště objektů BLOB v Azure do synapse SQL](load-data-from-azure-blob-storage-using-polybase.md).
- Informace o monitorování datové zátěže najdete v tématu [Monitorování úlohy pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md).
