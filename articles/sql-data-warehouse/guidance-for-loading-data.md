---
title: Osvědčené postupy načítání dat – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení a optimalizace výkonu pro načítání dat do služby Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 48b0f0300ab563e8388c9e99f4f90cd24c56678d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Osvědčené postupy načítání dat do služby Azure SQL Data Warehouse
Doporučení a optimalizace výkonu pro načítání dat do služby Azure SQL Data Warehouse. 

- Další informace o PolyBase a návrhu procesu ELT (extrakce, načítání a transformace) najdete v tématu [Návrh ELT pro službu SQL Data Warehouse](design-elt-data-loading.md).
- Kurz načítání najdete v tématu [Použití PolyBase k načítání dat z úložiště objektů blob v Azure do služby Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="preparing-data-in-azure-storage"></a>Příprava dat v Azure Storage
Vrstvu úložiště i datový sklad uložte společně do stejného umístění, abyste minimalizovali latenci.

Při exportu dat do formátu souboru ORC může dojít k chybám s nedostatkem paměti Java, pokud se zde nacházejí velké textové sloupce. Toto omezení můžete obejít tím, že importujete jen podmnožinu sloupců.

Technologie PolyBase nedokáže načíst řádky, které obsahují více než 1 000 000 bajtů dat. Když vkládáte data do textových souborů v úložišti Azure Blob nebo ve službě Azure Data Lake Store, musí tyto soubory obsahovat méně než 1 000 000 bajtů dat. Toto omezení platí bez ohledu na schéma tabulky.

Všechny formáty souborů mají jiné výkonové charakteristiky. Pokud chcete docílit nejrychlejšího načtení, použijte komprimované textové soubory s oddělovači. Rozdíl mezi výkonem kódování UTF-8 a UTF-16 je minimální.

Velké komprimované soubory rozdělte do menších komprimovaných souborů.

## <a name="running-loads-with-enough-compute"></a>Dostatečné výpočetní prostředky pro načítání dat

Největší rychlosti při načítání dosáhnete, když budete spouštět vždy jen jednu úlohu načtení dat. Pokud to není možné, spouštějte souběžně co nejmenší počet úloh. Při rozsáhlé úloze načítání dat byste před načítáním měli zvážit možnost rozšíření datového skladu škálováním.

Pokud chcete spouštět načítání s odpovídajícími výpočetními prostředky, vytvořte uživatele načítání vyhrazené pro spouštění načítání. Každého uživatele načítání přiřaďte ke konkrétní třídě prostředků. Pokud chcete spustit načítání, přihlaste se jako jeden z uživatelů načítání a pak spusťte načítání. Načítání se spustí s využitím třídy prostředků tohoto uživatele.  Tato metoda je jednodušší než se pokoušet o změnu třídy prostředků uživatele podle aktuálních potřeb třídy prostředků.

### <a name="example-of-creating-a-loading-user"></a>Příklad vytvoření uživatele načítání
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
Pokud chcete spustit načítání s prostředky pro třídy prostředků staticRC20, stačí se přihlásit jako LoaderRC20 a spustit načítání.

Spouštějte načítání v rámci statických, a ne dynamických, tříd prostředků. Použití tříd statické prostředků zaručuje stejné prostředky bez ohledu na to vaše [datového skladu jednotky](what-is-a-data-warehouse-unit-dwu-cdwu.md). Pokud použijete dynamickou třídu prostředků, budou se prostředky lišit v závislosti na vaší úrovni služby. V případě dynamických tříd znamená nižší úroveň služby, že pro vašeho uživatele načítání pravděpodobně musíte použít větší třídu prostředků.

## <a name="allowing-multiple-users-to-load"></a>Povolení načítání více uživatelům

Často je potřeba, aby data do datového skladu načítalo více uživatelů. Načtení se [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) vyžaduje oprávnění pro řízení databáze.  Oprávnění CONTROL poskytuje přístup pro řízení ke všem schématům. Pravděpodobně ale nebudete chtít, aby všichni uživatelé, kteří načítají data, měli oprávnění CONTROL pro přístup ke všem schématům. K omezení oprávnění slouží příkaz DENY CONTROL.

Představte si například schémata databáze schema_A pro oddělení A a schema_B pro oddělení B. Uživatelé databáze user_A a user_B budou uživateli pro načítání PolyBase v oddělení A, respektive oddělení B. Oba uživatelé mají k databázi udělená oprávnění CONTROL. Autoři schémat A a B nyní svá schémata uzamknou pomocí příkazu DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

Uživatelé user_A a user_B jsou nyní vyloučení ze schématu příslušného druhého oddělení.


## <a name="loading-to-a-staging-table"></a>Načítání do pracovní tabulky

Pokud chcete při přesunu dat do tabulky datového skladu dosáhnout největší načítací rychlosti, načtěte data do pracovní tabulky.  Pracovní tabulku definujte jako haldu a jako způsob distribuce použijte kruhové dotazování (round robin). 

Myslete na to, že načítání je většinou dvoufázový proces, kdy napřed načtete data do pracovní tabulky a pak je vložíte do tabulky v provozním datovém skladu. Pokud provozní tabulka používá k distribuci algoritmus hash, může být celková doba načtení a vložení dat kratší, než když k definici pracovní tabulky použijete distribuci hash. Načítání do pracovní tabulky trvá déle, ale druhý krok, který spočívá ve vkládání řádků do provozní tabulky, nepřesouvá data prostřednictvím distribuce.

## <a name="loading-to-a-columnstore-index"></a>Načítání do indexu columnstore

Indexy columnstore vyžadují hodně paměti, aby mohly komprimovat data do vysoce kvalitních skupin řádků. Kvůli zajištění co nejlepší účinnosti komprese a indexování musí index columnstore do každé skupiny řádků zkomprimovat maximální počet 1 048 576 řádků. V případě nedostatku paměti nemusí index columnstore dosahovat maximální míry komprese. To zase ovlivňuje výkon dotazů. Podrobné informace najdete v tématu [Optimalizace paměti pro columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Pokud chcete zajistit, aby měl nahrávající uživatel dostatek paměti pro dosažení maximální míry komprese, použijte uživatele načítání, kteří jsou členy střední nebo velké třídy prostředků. 
- Načtěte dostatek dat pro úplně naplnění nových skupin řádků. Při hromadném načítání dat se každých 1 048 576 řádků zkomprimuje přímo do indexu columnstore jako kompletní skupina řádků. Při načítání méně než 102 400 řádků se řádky odesílají do tabulky deltastore, kde se řádky uchovávají v indexu B-stromu. Pokud načtete příliš málo řádků, můžou se všechny dostat do indexu deltastore, a nebudou se okamžitě komprimovat do formátu columnstore.


## <a name="handling-loading-failures"></a>Zpracování chyb načítání

Načtení s použitím externí tabulky může selhat s chybou *Query aborted-- the maximum reject threshold was reached while reading from an external source* (Dotaz byl přerušen – při čtení z externího zdroje byla dosažena maximální prahová hodnota pro odmítnutí). Tato zpráva znamená, že vaše externí data obsahují nezapsané záznamy. Datový záznam se považuje za nezapsaný, pokud se typy dat nebo čísla sloupců neshodují s definicemi sloupců externí tabulky nebo pokud data neodpovídají zadanému formátu externího souboru. 

Pokud chcete nezapsané záznamy opravit, ujistěte se, že jsou definice formátů externí tabulky a externího souboru správné a že externí data těmto definicím odpovídají. V případě nezapsané podmnožiny externích datových záznamů se můžete rozhodnout, že ve svých dotazech, které využívají možnosti zamítnutí v příkazu CREATE EXTERNAL TABLE, chcete tyto záznamy zamítnout.

## <a name="inserting-data-into-a-production-table"></a>Vložení dat do provozní tabulky
Při jednorázovém načtení malé tabulky [příkazem INSERT](/sql/t-sql/statements/insert-transact-sql) nebo i při pravidelně se opakujícím načítání funkcí look-up pravděpodobně vystačíte s následujícím příkazem: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Vkládání jednotlivých záznamů ale není tak účinné jako hromadné načtení. 

Pokud máte za den tisíce nebo více samostatných vložení, vytvořte z nich dávku, abyste je mohli načíst hromadně.  Vyvíjejte své procesy tak, aby samostatná vkládání připojovaly do souboru, a pak vytvořte další proces, který tento soubor bude pravidelně načítat.

## <a name="creating-statistics-after-the-load"></a>Vytvoření statistiky po načtení

Pro zlepšení výkonu dotazů je důležité vytvořit statistiku pro všechny sloupce všech tabulek po prvním načtení, nebo když v datech dojde k zásadnějším změnám.  Podrobné vysvětlení statistiky najdete v tématu [Statistika](sql-data-warehouse-tables-statistics.md). Následující příklad vytvoří statistiku pro pět sloupců tabulky Customer_Speed.

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

Pro každý účet úložiště, jehož klíč se změnil, vydejte příkaz [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql).

Příklad:

Původní klíč je vytvořený.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
    ``` 

Proveďte otočení klíče 1 do klíče 2.

    ```sq;
    ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2' 
    ```

V příslušných externích zdrojích dat se nevyžadují žádné další změny.


## <a name="next-steps"></a>Další postup
Informace o monitorování datové zátěže najdete v tématu [Monitorování úlohy pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md).



