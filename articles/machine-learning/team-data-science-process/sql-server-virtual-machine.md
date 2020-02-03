---
title: Zkoumání dat ve virtuální počítač s SQL serverem - vědecké zpracování týmových dat
description: Prozkoumejte a zpracujte data a generujte funkce pomocí Pythonu nebo SQL ve virtuálním počítači s SQL Server v Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718476"
---
# <a name="heading"></a>Zpracování dat v SQL Server virtuálním počítači v Azure
Tento dokument popisuje, jak zkoumat data a vygenerovat funkcí pro data uložená v virtuálního počítače s SQL serverem v Azure. Tento cíl může dokončit data tahání pomocí SQL nebo pomocí programovacího jazyka, jako je Python.

> [!NOTE]
> Ukázky příkazů SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na mapování proces vědy dat cloudu se naučíte přesunout data do SQL serveru.
> 
> 

## <a name="SQL"></a>Používání SQL
Popisujeme následující úkoly wrangling dat v této části pomocí jazyka SQL:

1. [Zkoumání dat](#sql-dataexploration)
2. [Generace funkcí](#sql-featuregen)

### <a name="sql-dataexploration"></a>Zkoumání dat
Tady je několik ukázky skriptů SQL, které lze použít k prozkoumání úložiště dat v systému SQL Server.

> [!NOTE]
> Pro praktické příklady můžete použít [datovou sadu taxislužby NYC](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data tahání pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro ucelený návod.
> 
> 

1. Získat počet vyjádření za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získání úrovně ve sloupci zařazené do kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získat počet úrovní v kombinaci dva sloupce a bude zařazené do kategorií 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získání distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Generace funkcí
V této části popisujeme možnosti generování funkcí s použitím SQL:  

1. [Generace funkcí na základě počtu](#sql-countfeature)
2. [Generace funkcí binningu](#sql-binningfeature)
3. [Zavádění funkcí z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který jde připojit k původní tabulky. 
> 
> 

### <a name="sql-countfeature"></a>Generace funkcí na základě počtu
Následující příklady znázorňují dva způsoby generování počet funkcí. První metoda používá podmíněný součet a druhá metoda používá klauzuli 'where'. Tyto výsledky pak mohou být spojeny s původní tabulkou (pomocí sloupců primárního klíče), aby měly funkce Count společně s původními daty.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Generace funkcí binningu
Následující příklad ukazuje, jak generovat rozdělený na intervaly funkce podle binning (přihrádkami pět) číselný sloupec, který lze použít jako funkci:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Zavádění funkcí z jednoho sloupce
V této části jsme ukazují, jak zavést jeden sloupec v tabulce k vygenerování dalších funkcí. Příklad předpokládá, že je v tabulce, ze kterého jste se pokoušeli vygenerovat funkce sloupec zeměpisné šířky a délky.

Tady je stručný úvod k datům o poloze/Zeměpisná šířka (znovu se zdroji z StackOverflow, [jak změřit přesnost zeměpisné šířky a délky?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Tyto doprovodné materiály jsou užitečné k pochopení před zahrnutím umístění jako jedné nebo více funkcí:

* Znaménko manažerech, jestli se sever nebo – Jih, východní nebo – západ na celém světě.
* Nenulový stovky číslice nám říká, že používáme zeměpisná délka a šířka není!
* Desítkami číslic poskytuje schopen přibližně 1 000 kilometrů. Dává nám užitečné informace o jaké kontinentu nebo se na oceánských.
* Jednotky číslice (jednu míru decimal) poskytuje pozici 111 kilometrů (60 mílové, asi 69 mil). Dá vám vědět, co je to stát, země nebo oblast, ve které se nacházíte.
* Na jedno desetinné místo stojí až 11.1 km: ho odlišili pozice jedno město velké ze sousedních město velké.
* Druhé desetinné stojí až 1.1 km: ho jeden vesnice nezávislá na další.
* Můžete identifikovat velké zemědělská pole nebo institucionální campus které stojí za to, až 110 m: je třetí desetinné čárky.
* Může zjistit balení pozemního které stojí za to, až 11 m: je čtvrtý desetinné čárky. Je srovnatelná typické přesnost neopraveno GPS jednotky s bez rušení.
* Pátý desetinné čárky je vhodné až 1.1 m: že stromů ho odlišuje od sebe navzájem. Přesnost na tuto úroveň díky obchodní jednotky GPS lze nastavit pouze pomocí rozdílové opravy.
* Šestý desetinné místo stojí až 0,11 m:, že může být využit k rozložení struktury podrobně pro navrhování spolehnut, vytváření silnicích zakázána. Měla by být více než dostatečné pro sledování pohybu glaciers a řek. Toho lze dosáhnout pomocí painstaking míry pomocí GPS, jako je například differentially opravený GPS.

Informace o umístění může být natrénuje následujícím způsobem oddělení oblastí, umístění a Město informace. Můžete také zavolat koncový bod REST, jako je například rozhraní API služby Bing Maps, které je k dispozici v [umístění, kde](https://msdn.microsoft.com/library/ff701710.aspx) získáte informace o oblasti a oblasti.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Tyto funkce založená na poloze dále slouží ke generování dalších počet funkcí, jak je popsáno výše. 

> [!TIP]
> Programově můžete vložit záznamy pomocí vašich jazyk podle vlastní volby. Možná budete muset vložit data do bloků dat, abyste vylepšili efektivitu zápisu (příklad toho, jak to udělat pomocí pyodbc) najdete v [ukázce Hello pro přístup k SQLServer pomocí Pythonu](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python). Další možností je vkládat data do databáze pomocí [nástroje BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Připojování k Azure Machine Learning
Nově vygenerovaný funkce můžete přidat jako sloupec do existující tabulky nebo uložené do nové tabulky a spojen s původní tabulky pro službu machine learning. Funkce se dají vygenerovat nebo zobrazit, pokud už je vytvořený, pomocí modulu [Import dat][import-data] v Azure Machine Learning, jak je znázorněno níže:

![čtenáři Azure ml][1] 

## <a name="python"></a>Používání programovacího jazyka, jako je Python
Použití Pythonu k prozkoumávání dat a generování funkcí, když jsou data v SQL Server podobná zpracování dat v Azure BLOB pomocí Pythonu, jak je popsáno v části [zpracování dat objektů BLOB v Azure v prostředí pro datové vědy](data-blob.md). Načtěte data z databáze do datového rámce PANDAS pro další zpracování. Dokumentujeme proces připojení k databázi a načítání dat do datového rámce v této části.

Následující formát připojovacího řetězce je možné se připojit k databázi SQL serveru z Pythonu pomocí pyodbc (nahraďte název_serveru, dbname, uživatelské jméno a heslo s určitými hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna PANDAS](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte výsledky vrácené z databáze SQL serveru do Pandas datového rámce:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nyní můžete pracovat s datovým rámcem PANDAS, jak je popsáno v článku [zpracování dat objektů BLOB v Azure ve vašem prostředí pro datové vědy](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure pro datové vědy v příkladu akce
Podrobný příklad procesu Azure Data vědu pomocí veřejné datové sady najdete v tématu věnovaném [procesu Azure Data věda v akci](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

