---
title: Prozkoumat data ve SQL Serverm virtuálním počítači – vědecké zpracování týmových dat
description: Prozkoumejte a zpracujte data a generujte funkce pomocí Pythonu nebo SQL ve virtuálním počítači s SQL Server v Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 877c639c35378b173b6ec9c8697725e3b3c09290
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053614"
---
# <a name="heading"></a>Zpracování dat v SQL Server virtuálním počítači v Azure
Tento dokument popisuje, jak prozkoumat data a generovat funkce pro data uložená v SQL Serverm VIRTUÁLNÍm počítači v Azure. To lze provést pomocí SQL data tahání nebo pomocí programovacího jazyka, jako je Python.

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že jsou data v SQL Server. Pokud tomu tak není, přečtěte si článek mapa vědeckých procesů cloudových dat, kde se dozvíte, jak přesunout data do SQL Server.
> 
> 

## <a name="SQL"></a>Používání SQL
V této části popisujeme následující úlohy tahání dat pomocí SQL:

1. [Zkoumání dat](#sql-dataexploration)
2. [Generace funkcí](#sql-featuregen)

### <a name="sql-dataexploration"></a>Zkoumání dat
Tady je několik ukázkových skriptů SQL, které je možné použít k prozkoumávání úložišť dat v SQL Server.

> [!NOTE]
> Pro praktické příklady můžete použít [datovou sadu taxislužby NYC](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data tahání pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro ucelený návod.
> 
> 

1. Získá počet pozorování za den.
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získat úrovně ve sloupci kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získá počet úrovní v kombinaci dvou kategorií sloupců. 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získat distribuci pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Generace funkcí
V této části popíšeme způsoby generování funkcí pomocí SQL:  

1. [Generace funkcí na základě počtu](#sql-countfeature)
2. [Generace funkcí binningu](#sql-binningfeature)
3. [Zavádění funkcí z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárními klíči, které lze propojit s původní tabulkou. 
> 
> 

### <a name="sql-countfeature"></a>Generace funkcí na základě počtu
Následující příklady znázorňují dva způsoby, jak vygenerovat funkce Count. První metoda používá Podmíněný součet a druhá metoda používá klauzuli WHERE. Tyto funkce se pak můžou spojit s původní tabulkou (pomocí sloupců primárního klíče) a mít tak k dispozici funkce Count spolu s původními daty.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Generace funkcí binningu
Následující příklad ukazuje, jak generovat funkce rozdělený pomocí binningu (pomocí pěti přihrádek) číselný sloupec, který lze použít jako funkci místo toho:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Zavádění funkcí z jednoho sloupce
V této části ukážeme, jak v tabulce zavést jeden sloupec pro generování dalších funkcí. V příkladu se předpokládá, že je v tabulce, ze které se pokoušíte generovat funkce, sloupec Zeměpisná šířka nebo zeměpisná délka.

Tady je stručný úvod k datům o poloze/Zeměpisná šířka (znovu se zdroji z StackOverflow, [jak změřit přesnost zeměpisné šířky a délky?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). To je užitečné pochopit před featurizing pole Location:

* Symbol informuje o tom, zda je na světě sever, USA – východ nebo západ.
* Nenulová číslice stovek oznamuje, že používáme zeměpisnou délku, ne zeměpisnou šířku.
* Desítková číslice poskytuje pozici přibližně 1 000 kilometrů. Poskytuje nám užitečné informace o tom, na jakém kontinentě nebo oceánu.
* Číslice jednotek (jedna desetinná hladina) dává pozici až 111 kilometrů (60 námořních mil, přibližně 69 mil.). Dá vám vědět, co je to stát, země nebo oblast, ve které se nacházíte.
* První desetinné místo je až 11,1 km: může rozlišovat polohu jednoho velkého města od sousedního města.
* Druhé desetinné místo je až 1,1 km: může oddělit jeden Village od dalšího.
* Třetí desetinné místo má hodnotu až 110 m: může identifikovat velké zemědělské pole nebo institucionální areály.
* Čtvrté desetinné místo je až 11 m: může identifikovat pozemek půdy. Je srovnatelná s typickou přesností nesprávné jednotky GPS bez jakýchkoli rušivých zásahů.
* Pátý počet desetinných míst je až 1,1 m: rozliší stromy od sebe navzájem. Přesnost na tuto úroveň u komerčních jednotek GPS se dá dosáhnout jenom s rozdílovou korekcí.
* Šest desetinných míst je rovno 0,11 m: můžete použít tuto možnost pro vytváření podrobných struktur pro navrhování krajiny a vytváření silnic. Mělo by být dostatečně dobré pro sledování pohybů Glaciers a řek. Toho je možné dosáhnout tím, že vezmete painstaking míry pomocí GPS, jako je rozdílově korigovaný GPS.

Informace o umístění může být natrénuje takto, oddělit informace o oblasti, umístění a městech. Všimněte si, že můžete také volat koncový bod REST, jako je například rozhraní API mapy Bingu, které je k dispozici v [umístění](https://msdn.microsoft.com/library/ff701710.aspx) pro získání informací o oblasti a oblasti.

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

Tyto funkce založené na umístění je možné dále využít ke generování dalších funkcí Count, jak je popsáno výše. 

> [!TIP]
> Záznamy můžete programově vkládat pomocí vámi zvoleného jazyka. Možná budete muset vložit data do bloků dat, abyste vylepšili efektivitu zápisu (příklad toho, jak to udělat pomocí pyodbc) najdete v [ukázce Hello pro přístup k SQLServer pomocí Pythonu](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python). Další možností je vkládat data do databáze pomocí [nástroje BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Připojování k Azure Machine Learning
Nově vygenerovanou funkci lze přidat jako sloupec do existující tabulky nebo Uložit do nové tabulky a spojit s původní tabulkou pro strojové učení. Funkce se dají vygenerovat nebo zobrazit, pokud už je vytvořený, pomocí modulu [Import dat][import-data] v Azure Machine Learning, jak je znázorněno níže:

![čtenáři AzureML][1] 

## <a name="python"></a>Používání programovacího jazyka, jako je Python
Použití Pythonu k prozkoumávání dat a generování funkcí, když jsou data v SQL Server podobná zpracování dat v Azure BLOB pomocí Pythonu, jak je popsáno v části [zpracování dat objektů BLOB v Azure v prostředí pro datové vědy](data-blob.md). Data musí být načtena z databáze do PANDAS datového rámce a poté mohou být zpracována dále. Dokument se připojuje k databázi a načítá data do datových snímků v této části.

Následující formát připojovacího řetězce se dá použít k připojení k SQL Server databázi z Pythonu pomocí pyodbc (nahraďte servername, dbname, username a Password konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna PANDAS](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte výsledky vrácené z databáze SQL Server do datového rámce PANDAS:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nyní můžete pracovat s datovým rámcem PANDAS, jak je popsáno v článku [zpracování dat objektů BLOB v Azure ve vašem prostředí pro datové vědy](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data vědu v akčním příkladu
Podrobný příklad procesu Azure Data vědu pomocí veřejné datové sady najdete v tématu věnovaném [procesu Azure Data věda v akci](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

