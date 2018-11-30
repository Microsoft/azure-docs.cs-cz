---
title: Zkoumání dat ve virtuální počítač s SQL serverem v Azure | Dokumentace Microsoftu
description: Zkoumání dat a vytvoření funkce v virtuálního počítače s SQL serverem v Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 73e56497834344e0e41d89313086c051683b4fbb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442553"
---
# <a name="heading"></a>Zpracování dat v SQL serveru na virtuálním počítači Azure
Tento dokument popisuje, jak zkoumat data a vygenerovat funkcí pro data uložená v virtuálního počítače s SQL serverem v Azure. To můžete udělat tahání dat pomocí SQL nebo pomocí programovacího jazyka, jako je Python.

> [!NOTE]
> Ukázky příkazů SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na mapování proces vědy dat cloudu se naučíte přesunout data do SQL serveru.
> 
> 

## <a name="SQL"></a>Pomocí SQL
Popisujeme následující úkoly wrangling dat v této části pomocí jazyka SQL:

1. [Zkoumání dat](#sql-dataexploration)
2. [Funkce generování](#sql-featuregen)

### <a name="sql-dataexploration"></a>Zkoumání dat
Tady je několik ukázky skriptů SQL, které lze použít k prozkoumání úložiště dat v systému SQL Server.

> [!NOTE]
> Například praktické, můžete použít [NYC taxislužby datovou sadu](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [tahání dat NYC pomocí SQL Server a IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
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

### <a name="sql-featuregen"></a>Funkce generování
V této části popisujeme možnosti generování funkcí s použitím SQL:  

1. [Počet na základě funkcí generace](#sql-countfeature)
2. [Binning funkci generování](#sql-binningfeature)
3. [Použití funkce z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který jde připojit k původní tabulky. 
> 
> 

### <a name="sql-countfeature"></a>Počet na základě funkcí generace
Následující příklady znázorňují dva způsoby generování počet funkcí. První metoda používá podmíněný součet a druhá metoda používá klauzuli 'where'. Ty potom jde připojit k původní tabulky (s použitím sloupce s primárním klíčem) mít počet funkcí společně s původními daty.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Binning funkci generování
Následující příklad ukazuje, jak generovat rozdělený na intervaly funkce podle binning (přihrádkami pět) číselný sloupec, který lze použít jako funkci:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Použití funkce z jednoho sloupce
V této části jsme ukazují, jak zavést jeden sloupec v tabulce k vygenerování dalších funkcí. Příklad předpokládá, že je v tabulce, ze kterého jste se pokoušeli vygenerovat funkce sloupec zeměpisné šířky a délky.

Tady je stručný úvod do data o poloze zeměpisnou šířkou/délkou (zdroje z stackoverflow [způsob měření přesnost zeměpisné šířky a délky?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). To je užitečné porozumět, než featurizing pole umístění:

* Znaménko manažerech, jestli se sever nebo – Jih, východní nebo – západ na celém světě.
* Nenulový stovky číslice nám říká, že používáme zeměpisná délka a šířka není!
* Desítkami číslic poskytuje schopen přibližně 1 000 kilometrů. Dává nám užitečné informace o jaké kontinentu nebo se na oceánských.
* Jednotky číslice (jednu míru decimal) poskytuje pozici 111 kilometrů (60 mílové, asi 69 mil). To můžete nám říct, zhruba jaké velké státě nebo zemi, kterými se v.
* Na jedno desetinné místo stojí až 11.1 km: ho odlišili pozice jedno město velké ze sousedních město velké.
* Druhé desetinné stojí až 1.1 km: ho jeden vesnice nezávislá na další.
* Můžete identifikovat velké zemědělská pole nebo institucionální campus které stojí za to, až 110 m: je třetí desetinné čárky.
* Může zjistit balení pozemního které stojí za to, až 11 m: je čtvrtý desetinné čárky. Je srovnatelná typické přesnost neopraveno GPS jednotky s bez rušení.
* Pátý desetinné čárky je vhodné až 1.1 m: že stromů ho odlišuje od sebe navzájem. Přesnost na tuto úroveň díky obchodní jednotky GPS lze nastavit pouze pomocí rozdílové opravy.
* Šestý desetinné místo stojí až 0,11 m:, že může být využit k rozložení struktury podrobně pro navrhování spolehnut, vytváření silnicích zakázána. Měla by být více než dostatečné pro sledování pohybu glaciers a řek. Toho lze dosáhnout pomocí painstaking míry pomocí GPS, jako je například differentially opravený GPS.

Informace o umístění může být natrénuje následujícím způsobem oddělení oblastí, umístění a Město informace. Všimněte si, že můžete také volat koncový bod REST, jako je například rozhraní API map Bing k dispozici na [najít umístění bodem](https://msdn.microsoft.com/library/ff701710.aspx) zobrazíte informace o oblasti a oblasti.

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
> Programově můžete vložit záznamy pomocí vašich jazyk podle vlastní volby. Možná budete muset vložit data za účelem zlepšení efektivity zápisu (příklad toho, jak to udělat pomocí pyodbc, naleznete v tématu [ukázky Hello World A pro přístup k systému SQL Server s pythonem](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Další možností je k vložení dat do databáze pomocí [nástroj BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaný funkce můžete přidat jako sloupec do existující tabulky nebo uložené do nové tabulky a spojen s původní tabulky pro službu machine learning. Můžete generovat ani přistupovat, pokud už vytvořili, pomocí funkce [Import dat] [ import-data] modulu ve službě Azure Machine Learning, jak je znázorněno níže:

![čtenáři Azure ml][1] 

## <a name="python"></a>Pomocí programovacího jazyka, jako je Python
Použití Pythonu k zkoumat data a vygenerovat funkce, když jsou data v systému SQL Server je podobný zpracování dat v Azure blob pomocí Pythonu, jak je uvedeno v [data objektů Blob v Azure procesu v prostředí pro datové vědy](data-blob.md). Data musí být načtena z databáze do pandas datového rámce a pak mohou být další zpracování. Dokumentujeme proces připojení k databázi a načítání dat do datového rámce v této části.

Následující formát připojovacího řetězce je možné se připojit k databázi SQL serveru z Pythonu pomocí pyodbc (nahraďte název_serveru, dbname, uživatelské jméno a heslo s určitými hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovny Pandas](http://pandas.pydata.org/) v Pythonu nabízí bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte výsledky vrácené z databáze SQL serveru do Pandas datového rámce:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat se datový rámec Pandas, jak je popsáno v článku [data objektů Blob v Azure procesu v prostředí pro datové vědy](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure pro datové vědy v příkladu akce
Příklad začátku do konce Průvodce vědecké zpracování dat Azure pomocí veřejné datové sady, naleznete v tématu [vědecké zpracování dat Azure v akci](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

