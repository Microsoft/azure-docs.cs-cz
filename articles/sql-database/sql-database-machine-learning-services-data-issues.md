---
title: Práce s datovými typy a objekty R a SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Zjistěte, jak pracovat s datovými typy a datovými objekty v R s Azure SQL Database pomocí Machine Learning Services (preview), včetně běžných problémů, se kterými se můžete setkat.
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
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453195"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)

Tento článek popisuje některé běžné problémy, se kterými se můžete setkat při přesouvání dat mezi R a SQL Database ve [službě Machine Learning Services (s R) v Azure SQL Database](sql-database-machine-learning-services-overview.md). Zkušenosti, které získáte prostřednictvím tohoto cvičení poskytuje základní pozadí při práci s daty ve vlastním skriptu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Mezi běžné problémy, se kterými se můžete setkat, patří:

- Datové typy se někdy neshodují
- K implicitním konverzím může dojít.
- Někdy jsou vyžadovány operace přetypování a převodu.
- R a SQL používají různé datové objekty

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/) než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte mít nejprve [povolenou Azure SQL Database se službami machine learning (s R).](sql-database-machine-learning-services-overview.md)

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skripty R můžete spustit pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu budete používat SSMS.

## <a name="working-with-a-data-frame"></a>Práce s datovým rámcem

Když skript vrátí výsledky z R na SQL, musí vrátit data jako **data.frame**. Jakýkoli jiný typ objektu, který generujete ve skriptu – ať už se jedná o seznam, faktor, vektor nebo binární data – musí být převeden na datový rámec, pokud jej chcete vypronecit jako součást výsledků uložené procedury. Naštěstí existuje více funkcí R pro podporu změny jiných objektů do datového rámce. Můžete dokonce serializovat binární model a vrátit jej v datovém rámci, což uděláte později v tomto článku.

Nejprve pojďme experimentovat s některými základními objekty R - vektory, matice a seznamy - a uvidíte, jak převod na datový rámec změní výstup předaný SQL.

Porovnejte tyto dva skripty "Hello World" v R. Skripty vypadají téměř identické, ale první vrátí jeden sloupec tří hodnot, zatímco druhý vrátí tři sloupce s jednou hodnotou každý.

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

Proč jsou výsledky tak odlišné?

Odpověď lze obvykle nalézt pomocí `str()` příkazu R. Přidejte `str(object_name)` funkci kdekoli ve skriptu R, aby bylo datové schéma zadaného objektu R vráceno jako informační zpráva. Zprávy můžete zobrazit na kartě **Zprávy** v ssms.

Chcete-li zjistit, proč příklad 1 a příklad `str(OutputDataSet)` 2 mají tak `@script` odlišné výsledky, vložte řádek na konci definice proměnné v každém příkazu, například takto:

**Příklad 1 s přidanou funkcí str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Příklad 2 s přidanou funkcí str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Nyní zkontrolujte text ve **zprávách,** abyste zjistili, proč je výstup jiný.

**Výsledky - Příklad 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Výsledky - Příklad 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak můžete vidět, nepatrná změna syntaxe R měla velký vliv na schéma výsledků. Pro všechny podrobnosti jsou rozdíly v datových typech R vysvětleny v podrobnostech v části *Datové struktury* v [části "Advanced R" od Hadley Wickham](http://adv-r.had.co.nz).

Prozatím si uvědomte, že je třeba zkontrolovat očekávané výsledky při vynucení objektů R do datových rámců.

> [!TIP]
> Můžete také použít funkce identity `is.matrix`R, například , `is.vector`, vrátit informace o vnitřní datové struktuře.

## <a name="implicit-conversion-of-data-objects"></a>Implicitní převod datových objektů

Každý datový objekt R má vlastní pravidla pro způsob zpracování hodnot v kombinaci s jinými datovými objekty, pokud mají dva datové objekty stejný počet dimenzí nebo pokud libovolný datový objekt obsahuje heterogenní datové typy.

Předpokládejme například, že chcete provést násobení matice pomocí R. Chcete vynásobit matici s jedním sloupcem třemi hodnotami polem se čtyřmi hodnotami a v důsledku toho očekávat matici 4x3.

Nejprve vytvořte malou tabulku testovacích dat.

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

Pod obálkami je sloupec tří hodnot převeden na matici s jedním sloupcem. Vzhledem k tomu, že matice je pouze `y` zvláštní případ pole v R, pole je implicitně přinutí k matici s jedním sloupcem, aby se oba argumenty v souladu.

**Výsledky**

|Sloupec 1|Sloupec 2|Kol3|Kol4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1 500|

Všimněte si však, co se `y`stane, když změníte velikost pole .

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

Nyní R vrátí jednu hodnotu jako výsledek.

**Výsledky**
    
|Sloupec 1|
|---|
|1542|

Proč? V tomto případě, protože dva argumenty mohou být zpracovány jako vektory stejné délky, R vrátí vnitřní produkt jako matice.  Jedná se o očekávané chování podle pravidel lineární algebry. Může však způsobit problémy, pokud vaše aplikace pro příjem dat očekává, že se výstupní schéma nikdy nezmění!

## <a name="merge-or-multiply-columns-of-different-length"></a>Sloučení nebo násobení sloupců různé délky

R poskytuje velkou flexibilitu pro práci s vektory různých velikostí a pro kombinování těchto sloupcovitých struktur do datových rámců. Seznamy vektorů mohou vypadat jako tabulka, ale nedodržují všechna pravidla, kterými se řídí databázové tabulky.

Například následující skript definuje číselné pole délky 6 a `df1`ukládá jej do proměnné R . Číselné pole je pak zkombinováno s celámi tabulky RTestData (vytvořené výše), která obsahuje tři (3) hodnoty, aby se vytvořil nový datový rámec . `df2`

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

Chcete-li vyplnit datový rámec, R opakuje prvky načtené z RTestData tolikrát, kolikrát je potřeba, aby odpovídaly počtu prvků v poli `df1`.

**Výsledky**
    
|*Sloupec 2*|*Kol3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Nezapomeňte, že datový rámec vypadá pouze jako tabulka, ale ve skutečnosti je seznam vektorů.

## <a name="cast-or-convert-sql-data"></a>Přetypovat nebo převést data SQL

R a SQL nepoužívají stejné datové typy, takže při spuštění dotazu v SQL získat data a pak předat do modulu runtime R, některé typy implicitní převod obvykle probíhá. Další sada převodů probíhá při vrácení dat z R do SQL.

- SQL odešle data z dotazu do procesu R a převede je na vnitřní reprezentaci pro větší efektivitu.
- Runtime R načte data do proměnné data.frame a provede vlastní operace s daty.
- Databázový stroj vrátí data sql pomocí zabezpečené interní připojení a představuje data z hlediska datových typů SQL.
- Data získáte připojením k SQL pomocí klientské nebo síťové knihovny, která může vydávat dotazy SQL a zpracovávat tabulkové datové sady. Tato klientská aplikace může potenciálně ovlivnit data jinými způsoby.

Chcete-li zjistit, jak to funguje, spusťte dotaz, jako je tento v datovém skladu [AdventureWorksDW.](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) Toto zobrazení vrátí data o prodeji použitá při vytváření prognóz.

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
> Můžete použít libovolnou verzi AdventureWorks nebo vytvořit jiný dotaz pomocí vlastní databáze. Jde o pokus o zpracování některých dat, která obsahují text, datetime a číselné hodnoty.

Nyní zkuste použít tento dotaz jako vstup do uložené procedury.

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

Pokud se zobrazí chyba, budete pravděpodobně muset provést některé úpravy textu dotazu. Například predikát řetězce v klauzuli WHERE musí být uzavřen dvěma sadami jednoduchých uvozovek.

Po získání dotazu pracovní, zkontrolujte `str` výsledky funkce zobrazíte, jak R zachází se vstupními daty.

**Výsledky**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Sloupec datetime byl zpracován pomocí datového typu R **POSIXct**.
- Textový sloupec "ProductSeries" byl identifikován jako **faktor**, což znamená kategorickou proměnnou. Řetězcové hodnoty jsou ve výchozím nastavení zpracovány jako faktory. Pokud předáte řetězec R, je převeden na celé číslo pro interní použití a pak mapován zpět na řetězec na výstupu.

## <a name="summary"></a>Souhrn

Z těchto krátkých příkladů můžete vidět, že je třeba zkontrolovat účinky převodu dat při předávání dotazů SQL jako vstup. Vzhledem k tomu, že některé datové typy SQL nejsou podporovány R, zvažte tyto způsoby, jak se vyhnout chybám:

- Otestujte data předem a ověřte sloupce nebo hodnoty ve schématu, které by mohly být problémem při předání kódu R.
- Zadejte sloupce ve zdroji vstupních dat `SELECT *`jednotlivě, nikoli pomocí a zjistěte, jak bude každý sloupec zpracován.
- Proveďte explicitní přetypovádá podle potřeby při přípravě vstupních dat, aby se zabránilo překvapení.
- Vyhněte se předávání sloupců dat (například GUIDS nebo rowguids), které způsobují chyby a nejsou užitečné pro modelování.

Další informace o podporovaných a nepodporovaných datových typech R naleznete v [knihovnách R a datových typech](/sql/advanced-analytics/r/r-libraries-and-data-types).
