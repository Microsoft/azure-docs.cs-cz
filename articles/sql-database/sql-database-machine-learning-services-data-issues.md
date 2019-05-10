---
title: Práce s R a SQL datových typů a objekty
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Zjistěte, jak pracovat s datovými typy a datové objekty v R s Azure SQL Database pomocí Machine Learning Services (preview), včetně obecných problémů, které můžete narazit.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232612"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Práce s daty R a SQL v Azure SQL Database Machine Learning Services (preview)

Tento článek popisuje některé běžné problémy, kterým může dojít při přesouvání dat mezi R a SQL Database v [Machine Learning Services (s jazykem R) ve službě Azure SQL Database](sql-database-machine-learning-services-overview.md). Prostředí, na které můžete získat prostřednictvím tohoto cvičení poskytuje základní na pozadí při práci s daty v vlastního skriptu.

Běžné problémy, na které můžete zahrnout:

- Datové typy v některých případech se neshodují
- Implicitní převod může proběhnout
- Operace přetypování a převést jsou někdy nutné
- R a SQL použít různé datové objekty

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte nejprve mít databázi Azure SQL Machine Learning Services (R) povolena. Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale v tomto rychlém startu budete používat SSMS.

## <a name="working-with-a-data-frame"></a>Práce s datového rámce

Pokud váš skript vrátí výsledky z jazyka R do SQL, musí vracet data jako **data.frame**. Jakéhokoli jiného typu objektu, který vygenerujete v skript – jestli seznam, faktor, vector nebo binární data - musí převést na datový rámec, pokud chcete výstup jako součást výsledků uloženou proceduru. Naštěstí se více funkcí R pro podporu Změna datového rámce jiné objekty. Dokonce je možné serializovat binárnímu modelu a vrátí v datového rámce, které můžete udělat později v tomto článku.

Nejprve můžeme experimentovat s některé základní objekty R - vektorů, maticích a na seznamy – a podívejte se, jak převod na datový rámec změní výstup předat SQL.

Porovnání těchto dvou skriptů "Hello World" v jazyce R. Skripty vypadat téměř identické, ale první vrátí jeden sloupec ze tří hodnot, zatímco druhá vrátí tři sloupce z jedné hodnoty každého.

**Příklad 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Příklad 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Výsledky se tak liší

Odpověď možné obvykle najít pomocí R `str()` příkazu. Přidejte funkci `str(object_name)` kdekoli ve skriptu R se data schématu zadaného objektu R vrácena jako informační zpráva. Můžete zobrazit zprávy v **zprávy** kartě v aplikaci SSMS.

Chcete-li zjistit, proč Příklad 1 a 2 příkladu mají tyto různé výsledky, vložte řádku `str(OutputDataSet)` na konci `@script` definice proměnné v každém výroku takto:

**Příklad 1 s funkcí str přidán**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Příklad 2 s funkcí str přidán**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Nyní, přečtěte si text v **zprávy** zobrazíte, proč se liší ve výstupu.

**Výsledky – Příklad 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Výsledky – příklad 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak vidíte, i drobnou změnu v syntaxi jazyka R má velký vliv na schéma výsledky. Všechny podrobnosti, jsou vysvětleny rozdíly v R datové typy najdete v podrobnostech v *datové struktury* tématu ["Advanced R" ve Hadley Wickham](http://adv-r.had.co.nz).

Teď stačí mějte na paměti, že je potřeba zkontrolovat očekávané výsledky, když podřízenému R objekty do datové rámce.

> [!TIP]
> Také můžete použít funkce identity R, jako například `is.matrix`, `is.vector`, který vrací informace o interní datové struktury.

## <a name="implicit-conversion-of-data-objects"></a>Implicitní převod datových objektů

Každý objekt dat R má svůj vlastní pravidla pro způsob zpracování hodnot při kombinaci s jiných datových objektů, pokud dva datové objekty, které mají stejný počet rozměrů, nebo pokud libovolný datový objekt obsahuje heterogenní datové typy.

Například předpokládejme, že chcete provést násobení matic pomocí jazyka R. Chcete vynásobit matice jedním sloupcem pomocí tří hodnot pole čtyři hodnoty a v důsledku očekávat matice 4 x 3.

Nejprve vytvořte malé tabulky testovací data.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Nyní spusťte následující skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Pod pokličkou sloupci ze tří hodnot převést na jednosloupcovou matice. Protože je pouze zvláštním případem pole v jazyce R, pole matice `y` implicitně převeden na jednosloupcovou matice provést dva argumenty, které jsou v souladu.

**Results**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Uvědomte si však, co se stane, když změníte velikost pole `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

R teď jako výsledek vrátí jednu hodnotu.

**Results**
    
|Col1|
|---|
|1542|

Proč? V takovém případě protože dva argumenty, může být zpracována jako vektory stejné délky, R Vrátí součin vnitřní jako matice.  Toto je očekávané chování podle pravidel objektů algebraický lineární. To však může způsobit problémy, pokud vaše aplikace pro příjem dat očekává, že se nikdy nezmění schéma výstupu!

## <a name="merge-or-multiply-columns-of-different-length"></a>Sloučení nebo vynásobit sloupce jinou délku

R poskytuje flexibilitu pro práci s vektory různých velikostí a kombinace těchto struktur jako sloupec do datové rámce. Seznamy vektorů může vypadat například tabulky, ale není řídí všechna pravidla, kterými se řídí databázových tabulek.

Například následující skript definuje číselného pole o délce 6 a uloží jej do proměnné R `df1`. Číselná pole se následně se spojí dohromady s celými čísly RTestData tabulky (vytvořené výše) obsahující tři (3) hodnoty, aby nový datový rámec `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

K vyplnění datového rámce, opakuje R prvky získaných RTestData tolikrát, kolikrát podle potřeby tak, aby odpovídaly počet prvků v poli `df1`.

**Results**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Mějte na paměti, že datový rámec pouze vypadá jako tabulky, ale je ve skutečnosti seznam vektorů.

## <a name="cast-or-convert-sql-data"></a>CAST nebo convert dat SQL

R a SQL nepoužívejte stejné typy dat, takže při spuštění dotazu v SQL k získání dat a předat ho do modulem runtime r. některé typ implicitní převod obvykle probíhá. Další sadu převody se provádí po vrácení dat z R na SQL.

- SQL odesílá data z dotazu do procesu R a převede ho na vnitřní reprezentaci pro vyšší efektivitu.
- Modulem runtime r. načte data do proměnné data.frame a provede vlastní operace s daty.
- Databázový stroj vrací data do SQL s použitím zabezpečené interní připojení a zobrazí nalezená data z hlediska datových typů SQL.
- Můžete načíst data pomocí připojení k SQL pomocí knihovny klienta nebo sítě, můžete zadávat dotazy na SQL a zpracování tabulkové datové sady. Tato klientská aplikace může potenciálně ovlivnit data jiným způsobem.

Pokud chcete zobrazit, jak to funguje, spustit dotaz jako je například tento na [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) datového skladu. Toto zobrazení vrátí data o prodeji použité při vytváření předpovědí.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Můžete použít jakoukoli verzi nástroje AdventureWorks, nebo vytvořte jiný dotaz pomocí vlastní databázi. Bod je pokusí zpracovat data, která obsahuje text, data a času a číselné hodnoty.

Nyní zkuste použít tento dotaz jako vstup pro uloženou proceduru.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Pokud dojde k chybě, budete pravděpodobně muset provádět některé úpravy text dotazu. Například řetězec predikátu v klauzuli WHERE musí být uzavřen ve dvou sad jednoduchých uvozovek.

Jakmile se zobrazí dotaz, práce, zkontrolujte výsledky `str` funkce naleznete v tématu Jak R zpracovává vstupní data.

**Results**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Sloupec Datum a čas se zpracovalo pomocí datový typ R, **POSIXct**.
- Textového sloupce "ProductSeries" bylo zjištěno, že **faktor**, to znamená proměnnou zařazené do kategorií. Řetězcové hodnoty jsou zpracovávány jako faktory ve výchozím nastavení. Pokud předáte řetězec jazyka R, je převést na celé číslo pro interní použití a potom mapována na řetězec na výstupu.

## <a name="summary"></a>Souhrn

I tyto příklady krátký uvidíte potřeba zkontrolovat efekty převodu dat při předání SQL dotazy jako vstup. Protože některé typy dat SQL nejsou podporovány R, vezměte v úvahu tyto způsoby, aby nedocházelo k chybám:

- Testování datových předem a ověřte sloupců nebo hodnoty ve schématu, která může být problém při předání kódu R.
- Zadat sloupce ve zdroji vstupních dat jednotlivě, místo použití `SELECT *`a vědět, jak se zpracovává každý sloupec.
- Při přípravě vaší vstupní data pro předcházení překvapením, proveďte explicitní přetypování podle potřeby.
- Vyhněte se předávání sloupce dat (například identifikátory GUID nebo rowguids), které způsobí chyby a nejsou vhodné pro modelování.

Další informace o podporované a nepodporované typy dat R najdete v tématu [R knihovny a datové typy](/sql/advanced-analytics/r/r-libraries-and-data-types).
