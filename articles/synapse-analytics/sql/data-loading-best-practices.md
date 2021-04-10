---
title: Osvědčené postupy načítání dat
description: Doporučení a optimalizace výkonu pro načítání dat do vyhrazeného fondu SQL Azure synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 33212d44fcb6be3f01cf968d00751d55e3bd7b8f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585447"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Osvědčené postupy načítání dat do vyhrazeného fondu SQL Azure synapse Analytics

V tomto článku najdete doporučení a optimalizace výkonu pro načítání dat.

## <a name="prepare-data-in-azure-storage"></a>Příprava dat v Azure Storage

Pokud chcete minimalizovat latenci, najděte vrstvu úložiště a vyhrazený fond SQL.

Při exportu dat do formátu souboru ORC může dojít k chybám s nedostatkem paměti Java, pokud se zde nacházejí velké textové sloupce. Toto omezení můžete obejít tím, že importujete jen podmnožinu sloupců.

Základová databáze nemůže načíst řádky, které mají více než 1 000 000 bajtů dat. Když vkládáte data do textových souborů v úložišti Azure Blob nebo ve službě Azure Data Lake Store, musí tyto soubory obsahovat méně než 1 000 000 bajtů dat. Toto omezení platí bez ohledu na schéma tabulky.

Všechny formáty souborů mají jiné výkonové charakteristiky. Pokud chcete docílit nejrychlejšího načtení, použijte komprimované textové soubory s oddělovači. Rozdíl mezi výkonem kódování UTF-8 a UTF-16 je minimální.

Velké komprimované soubory rozdělte do menších komprimovaných souborů.

## <a name="run-loads-with-enough-compute"></a>Spuštění zátěže s dostatečnými výpočetními prostředky

Největší rychlosti při načítání dosáhnete, když budete spouštět vždy jen jednu úlohu načtení dat. Pokud to není možné, spouštějte souběžně co nejmenší počet úloh. Pokud očekáváte velkou úlohu načítání, zvažte, zda před zatížením škálovat vyhrazený fond SQL.

Pokud chcete spouštět načítání s odpovídajícími výpočetními prostředky, vytvořte uživatele načítání vyhrazené pro spouštění načítání. Přiřaďte každého uživatele načítání do konkrétní třídy prostředku nebo skupiny úloh. Pokud chcete spustit zátěž, přihlaste se jako jeden z uživatelů načítání a potom spusťte načtení. Načítání se spustí s využitím třídy prostředků tohoto uživatele.  Tato metoda je jednodušší než se pokoušet o změnu třídy prostředků uživatele podle aktuálních potřeb třídy prostředků.

### <a name="create-a-loading-user"></a>Vytvořit uživatele načítání

Tento příklad vytvoří uživatele načítání pro třídu prostředků staticrc20. Prvním krokem je **připojení k předloze** a vytvoření přihlášení.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Připojte se k datovému skladu a vytvořte uživatele. Následující kód předpokládá, že jste připojeni k databázi mySampleDataWarehouse. Ukazuje způsob vytvoření uživatele jménem LoaderRC20 a udělení oprávnění k databázi. Poté přidá uživatele jako člena databázové role staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Pokud chcete spustit zatížení s prostředky pro třídy prostředků staticRC20, přihlaste se jako LoaderRC20 a spusťte zátěž.

Spouštějte načítání v rámci statických, a ne dynamických, tříd prostředků. Použití statických tříd prostředků garantuje stejné prostředky bez ohledu na [jednotky datového skladu](resource-consumption-models.md). Pokud použijete dynamickou třídu prostředků, budou se prostředky lišit v závislosti na vaší úrovni služby. V případě dynamických tříd znamená nižší úroveň služby, že pro vašeho uživatele načítání pravděpodobně musíte použít větší třídu prostředků.

## <a name="allow-multiple-users-to-load"></a>Povolení načtení více uživatelů

Často je potřeba, aby data do datového skladu načítalo více uživatelů. Načítání pomocí [Create Table jako Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) vyžaduje oprávnění k řízení databáze.  Oprávnění CONTROL poskytuje přístup pro řízení ke všem schématům. Pravděpodobně ale nebudete chtít, aby všichni uživatelé, kteří načítají data, měli oprávnění CONTROL pro přístup ke všem schématům. K omezení oprávnění slouží příkaz DENY CONTROL.

Představte si například schémata databáze schema_A pro oddělení A a schema_B pro oddělení B. Uživatelé databáze user_A a user_B budou uživateli pro načítání PolyBase v oddělení A, respektive oddělení B. Oba uživatelé mají k databázi udělená oprávnění CONTROL. Autoři schémat A a B nyní svá schémata uzamknou pomocí příkazu DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A a user_B jsou nyní uzamčeny ze schématu jiné oddělení.

## <a name="load-to-a-staging-table"></a>Načítání do pracovní tabulky

Pokud chcete při přesunu dat do tabulky datového skladu dosáhnout největší načítací rychlosti, načtěte data do pracovní tabulky.  Pracovní tabulku definujte jako haldu a jako způsob distribuce použijte kruhové dotazování (round robin).

Myslete na to, že načítání je většinou dvoufázový proces, kdy napřed načtete data do pracovní tabulky a pak je vložíte do tabulky v provozním datovém skladu. Pokud provozní tabulka používá k distribuci algoritmus hash, může být celková doba načtení a vložení dat kratší, než když k definici pracovní tabulky použijete distribuci hash. Načítání do pracovní tabulky trvá déle, ale druhý krok, který spočívá ve vkládání řádků do provozní tabulky, nepřesouvá data prostřednictvím distribuce.

## <a name="load-to-a-columnstore-index"></a>Načíst do indexu columnstore

Indexy columnstore vyžadují hodně paměti, aby mohly komprimovat data do vysoce kvalitních skupin řádků. Kvůli zajištění co nejlepší účinnosti komprese a indexování musí index columnstore do každé skupiny řádků zkomprimovat maximální počet 1 048 576 řádků. V případě nedostatku paměti nemusí index columnstore dosahovat maximální míry komprese. Tato dopad na výkon dotazů. Podrobné informace najdete v tématu [Optimalizace paměti pro columnstore](data-load-columnstore-compression.md).

- Pokud chcete zajistit, aby měl nahrávající uživatel dostatek paměti pro dosažení maximální míry komprese, použijte uživatele načítání, kteří jsou členy střední nebo velké třídy prostředků.
- Načtěte dostatek dat pro úplně naplnění nových skupin řádků. Při hromadném načítání se každých 1 048 576 řádků komprimuje přímo do columnstore jako úplné skupiny řádků. Při načítání méně než 102 400 řádků se řádky odesílají do tabulky deltastore, kde se řádky uchovávají v indexu B-stromu. Pokud načtete příliš málo řádků, můžou se všechny dostat do indexu deltastore, a nebudou se okamžitě komprimovat do formátu columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zvýšit velikost dávky při použití rozhraní SQLBulkCopy API nebo BCP

Jak už bylo zmíněno dříve, načítající se s využitím základny budou poskytovat nejvyšší propustnost s synapse fondem SQL. Pokud nemůžete použít základnu pro načtení a musí používat rozhraní SQLBulkCopy API (nebo BCP), měli byste zvážit zvýšení propustnosti pro lepší propustnost – dobré pravidlo je velikost dávky mezi 100 tisíc a 1M řádky.

## <a name="manage-loading-failures"></a>Spravovat selhání načítání

Načtení s použitím externí tabulky může selhat s chybou *Query aborted-- the maximum reject threshold was reached while reading from an external source* (Dotaz byl přerušen – při čtení z externího zdroje byla dosažena maximální prahová hodnota pro odmítnutí). Tato zpráva znamená, že vaše externí data obsahují nezapsané záznamy. Datový záznam se považuje za nezapsaný, pokud se typy dat nebo čísla sloupců neshodují s definicemi sloupců externí tabulky nebo pokud data neodpovídají zadanému formátu externího souboru.

Pokud chcete nezapsané záznamy opravit, ujistěte se, že jsou definice formátů externí tabulky a externího souboru správné a že externí data těmto definicím odpovídají. V případě nezapsané podmnožiny externích datových záznamů se můžete rozhodnout, že ve svých dotazech, které využívají možnosti zamítnutí v příkazu CREATE EXTERNAL TABLE, chcete tyto záznamy zamítnout.

## <a name="insert-data-into-a-production-table"></a>Vložení dat do produkční tabulky

Při jednorázovém načtení malé tabulky [příkazem INSERT](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) nebo i při pravidelně se opakujícím načítání funkcí look-up pravděpodobně vystačíte s následujícím příkazem: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Vkládání typu Singleton ale není tak efektivní jako provádění hromadného zatížení.

Pokud máte za den tisíce nebo více samostatných vložení, vytvořte z nich dávku, abyste je mohli načíst hromadně.  Vyvíjejte své procesy tak, aby samostatná vkládání připojovaly do souboru, a pak vytvořte další proces, který tento soubor bude pravidelně načítat.

## <a name="create-statistics-after-the-load"></a>Vytvoření statistiky po načtení

Chcete-li zlepšit výkon dotazů, je důležité vytvořit statistiku pro všechny sloupce všech tabulek po prvním načtení nebo v datech dojde k významným změnám. Vytváření statistik můžete provést ručně nebo můžete povolit [Automatické vytváření statistik](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Podrobné vysvětlení statistiky najdete v tématu [Statistika](develop-tables-statistics.md). Následující příklad ukazuje, jak ručně vytvořit statistiku pro pět sloupců Customer_Speed tabulky.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Obměna klíčů úložiště

Osvědčeným postupem zabezpečení je pravidelně měnit přístupový klíč k úložišti objektů blob. Ke svému účtu úložiště objektů Blob máte dva klíče úložiště, abyste je mohli střídat.

Obměna klíčů účtu služby Azure Storage:

Pro každý účet úložiště, jehož klíč se změnil, vydejte příkaz [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true).

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

- Další informace o základu a navrhování procesu extrakce, načítání a transformace (ELT) najdete v tématu [design ELT for Azure synapse Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- V rámci tohoto kurzu načítání [dat z úložiště objektů BLOB v Azure do služby Azure synapse Analytics použijte základnu](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
- Informace o monitorování datové zátěže najdete v tématu [Monitorování úlohy pomocí zobrazení dynamické správy](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).