---
title: Práce s datovými typy a objekty R a SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Naučte se pracovat s datovými typy a datovými objekty v R s Azure SQL Database pomocí Machine Learning Services (Preview), včetně běžných problémů, se kterými se můžete setkat.
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
ms.openlocfilehash: f784d6ef56ad5cb800c0061fbb5d0d4ca3252fa0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048228"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje některé běžné problémy, se kterými se můžete setkat při přesouvání dat mezi R a SQL Database v [Machine Learning Services (s r) v Azure SQL Database](machine-learning-services-overview.md). Prostředí, které získáte prostřednictvím tohoto cvičení, poskytuje základní pozadí při práci s daty ve vlastním skriptu.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Mezi běžné problémy, se kterými se můžete setkat, patří:

- Datové typy se někdy neshodují.
- Mohou probíhat implicitní převody
- Operace přetypování a převodu se někdy vyžadují.
- R a SQL používají různé datové objekty

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Chcete-li spustit vzorový kód v těchto cvičeních, musíte nejprve mít [Azure SQL Database s povolenou Machine Learning Services (s R)](machine-learning-services-overview.md) .

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spouštět skripty R pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu použijete SSMS.

## <a name="working-with-a-data-frame"></a>Práce s datovým rámcem

Když skript vrátí výsledky z R do SQL, musí vrátit data jako **data. Frame**. Jakýkoli jiný typ objektu, který ve skriptu vygenerujete – ať už se jedná o seznam, faktor, vektor nebo binární data – musí být převedeno na datový rámec, pokud ho chcete vypsat jako součást výsledků uložených procedur. Naštěstí existuje více funkcí jazyka R, které podporují změnu jiných objektů na datový rámec. Můžete dokonce serializovat binární model a vrátit ho do datového rámce, který budete potřebovat později v tomto článku.

Nejprve Experimentujte s některými základními objekty jazyka R – vektory, maticemi a seznamy – a podívejte se, jak převod na datový rámec mění výstup předaný do SQL.

Porovnejte tyto dvě skripty "Hello World" v jazyce R. Skripty vypadají téměř stejně, ale první vrátí jeden sloupec tří hodnot, zatímco druhý vrátí tři sloupce s jednou hodnotou.

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

Proč se výsledky liší?

Odpověď se obvykle dá najít pomocí `str()` příkazu R. Přidejte funkci `str(object_name)` kamkoli do skriptu jazyka r, aby bylo schéma dat zadaného objektu R vráceno jako informační zpráva. Zprávy můžete zobrazit na kartě **zprávy** v SSMS.

Chcete-li zjistit, proč příklad 1 a příklad 2 obsahují takové různé výsledky, vložte řádek `str(OutputDataSet)` na konci `@script` Definice proměnné v každém příkazu, například takto:

**Příklad 1 s přidanými funkcemi str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Příklad 2 s přidanými funkcemi str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Teď zkontrolujte text ve **zprávách** a podívejte se, proč se výstup liší.

**Výsledky – příklad 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Výsledky – příklad 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak vidíte, lehká změna syntaxe jazyka R má velký vliv na schéma výsledků. Pro všechny podrobnosti jsou rozdíly v datových typech R vysvětleny v části "rozšířené informace" v části *datové struktury* v [tématu "Advanced R" by Hadley Wickham](http://adv-r.had.co.nz).

Prozatím stačí vědět, že je potřeba zkontrolovat očekávané výsledky při vynucený objektů R do datových rámců.

> [!TIP]
> Můžete také použít funkce R identity, například `is.matrix` ,, a `is.vector` získat informace o vnitřních datových strukturách.

## <a name="implicit-conversion-of-data-objects"></a>Implicitní převod datových objektů

Každý datový objekt R má vlastní pravidla pro způsob zpracování hodnot v kombinaci s jinými datovými objekty, pokud mají dva datové objekty stejný počet rozměrů, nebo pokud libovolný datový objekt obsahuje heterogenní datové typy.

Předpokládejme například, že chcete provést násobení matice pomocí jazyka R. Chcete vynásobit matici s jedním sloupcem a třemi hodnotami polem se čtyřmi hodnotami a očekávat 4x3 matici.

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

V rámci pokrývání se sloupec tří hodnot převede na matici s jedním sloupcem. Vzhledem k tomu, že matice je pouze zvláštní případ pole v jazyce R, pole `y` je implicitně převedeno na matici s jedním sloupcem, aby byly tyto dva argumenty shodné.

**Důsledk**

|Sloupec 1|Sloupec 2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1 500|

Všimněte si však, co se stane, když změníte velikost pole `y` .

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

**Důsledk**
    
|Sloupec 1|
|---|
|1542|

Proč? V takovém případě, protože dva argumenty lze zpracovat jako vektory stejné délky, R vrátí vnitřní produkt jako matici.  Toto je očekávané chování podle pravidel lineární algebraický. Může ale způsobit problémy, pokud vaše aplikace pro příjem dat očekává, že se výstupní schéma nikdy nemění.

## <a name="merge-or-multiply-columns-of-different-length"></a>Sloučení nebo vynásobení sloupců s různou délkou

Jazyk R poskytuje skvělou flexibilitu pro práci s vektory různých velikostí a pro kombinování těchto struktur podobných sloupců do datových rámců. Seznamy vektorů můžou vypadat jako tabulka, ale nedodržují všechna pravidla, která řídí databázové tabulky.

Například následující skript definuje číselné pole o délce 6 a uloží jej do proměnné R `df1` . Číselné pole je pak kombinováno s celými čísly tabulky RTestData (vytvořené výše), která obsahuje tři (3) hodnoty, k vytvoření nového datového rámce `df2` .

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

Aby bylo možné vyplnit datový rámec, R opakuje prvky načtené z RTestData tolikrát, kolikrát to bude nutné, aby odpovídaly počtu prvků v poli `df1` .

**Důsledk**
    
|*Sloupec 2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Mějte na paměti, že datový rámec vypadá jenom jako tabulka, ale ve skutečnosti se jedná o seznam vektorů.

## <a name="cast-or-convert-sql-data"></a>Přetypování nebo převod dat SQL

R a SQL nepoužívají stejné datové typy, takže když spustíte dotaz v SQL, který získá data a pak je předáte do modulu runtime jazyka R, obvykle se provede nějaký typ implicitního převodu. Další sada převodů probíhá při vracení dat z R do SQL.

- SQL přenáší data z dotazu do procesu R a převede je na interní reprezentace pro vyšší efektivitu.
- Běhový modul R načte data do proměnné data. Frame a provede jejich vlastní operace s daty.
- Databázový stroj vrátí data do SQL pomocí zabezpečeného interního připojení a prezentuje data z datových typů SQL.
- Data získáte připojením k SQL pomocí klienta nebo síťové knihovny, která může vydávat dotazy SQL a zpracovávat tabulkové datové sady. Tato klientská aplikace může potenciálně ovlivnit data jiným způsobem.

Pokud se chcete podívat, jak to funguje, spusťte v [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) datovém skladu dotaz, jako je tento. Toto zobrazení vrátí prodejní data použitá při vytváření prognóz.

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
> Můžete použít libovolnou verzi AdventureWorks nebo vytvořit jiný dotaz s vlastní databází. V tomto bodě se snažíte zpracovat některá data, která obsahují text, data a data a číselné hodnoty.

Teď tento dotaz zkuste použít jako vstup do uložené procedury.

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

Pokud se zobrazí chyba, budete pravděpodobně muset udělat nějaké úpravy textu dotazu. Například predikát řetězce v klauzuli WHERE musí být uzavřen dvěma sadami jednoduchých uvozovek.

Až se dotaz dostanou, podívejte se na výsledky `str` funkce a podívejte se, jak R zpracovává vstupní data.

**Důsledk**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Sloupec DateTime byl zpracován pomocí datového typu R, **POSIXct**.
- Textový sloupec "ProductSeries" byl identifikován jako **faktor**, což znamená kategorií proměnnou. Hodnoty řetězce se ve výchozím nastavení zpracovávají jako faktory. Pokud předáte řetězec do jazyka R, je převeden na celé číslo pro interní použití a následně mapován zpět na řetězec ve výstupu.

## <a name="summary"></a>Souhrn

V těchto krátkých příkladech vidíte nutnost kontrolovat účinky převodu dat při předávání dotazů SQL jako vstupu. Vzhledem k tomu, že jazyk R nepodporuje některé datové typy SQL, zvažte tyto způsoby, jak se vyhnout chybám:

- Otestujte data předem a ověřte sloupce nebo hodnoty ve schématu, které by mohly být problémem při předání do kódu jazyka R.
- Určete sloupce ve vstupním zdroji dat jednotlivě, nikoli pomocí `SELECT *` , a zjistěte, jak budou jednotlivé sloupce zpracovány.
- Při přípravě vstupních dat proveďte explicitní přetypování, abyste se vyhnuli překvapením.
- Vyhněte se předávání sloupců dat (například identifikátorů GUID nebo ROWGUIDS), které způsobují chyby a nejsou užitečné pro modelování.

Další informace o podporovaných a nepodporovaných datových typech R najdete v tématu [knihovny a datové typy jazyka r](/sql/advanced-analytics/r/r-libraries-and-data-types).
