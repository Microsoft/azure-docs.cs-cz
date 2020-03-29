---
title: Prozkoumejte data ve virtuálním počítači SQL Serveru – proces vědecké analýzy týmových dat
description: Prozkoumejte + zpracování dat a generovat funkce pomocí Pythonu nebo SQL ve virtuálním počítači SQL Serveru v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718476"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Zpracování dat na virtuálním počítači s SQL Serverem v Azure
Tento dokument popisuje, jak prozkoumat data a generovat funkce pro data uložená ve virtuálním počítači SQL Server v Azure. Tento cíl může být dokončen a data hádání pomocí SQL nebo pomocí programovacího jazyka, jako je Python.

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že data jsou na serveru SQL Server. Pokud tomu tak není, podívejte se na mapu procesu cloudové datové vědy a zjistěte, jak přesunout data na SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Použití SQL
Následující úlohy dotazování dat v této části popisujeme pomocí sql:

1. [Průzkum dat](#sql-dataexploration)
2. [Generování funkcí](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Průzkum dat
Zde je několik ukázkových skriptů SQL, které lze použít k prozkoumání úložišť dat na serveru SQL Server.

> [!NOTE]
> Pro praktický příklad můžete použít [NYC Taxi datové sady](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem NYC data [hašteření pomocí IPython notebook a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro end-to-end návod.
> 
> 

1. Získejte počet pozorování za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získání úrovní v kategorickém sloupci
   
    `select  distinct <column_name> from <databasename>`
3. Získání počtu úrovní v kombinaci dvou kategorických sloupců 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získání distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generování funkcí
V této části popisujeme způsoby generování funkcí pomocí sql:  

1. [Generování funkcí založených na počtu](#sql-countfeature)
2. [Generování binningu](#sql-binningfeature)
3. [Zavedení funkcí z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárním klíčem, které lze spojit s původní tabulkou. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generování funkcí založených na počtu
Následující příklady ukazují dva způsoby generování funkcí počítání. První metoda používá podmíněný součet a druhá metoda používá klauzuli "where". Tyto výsledky pak mohou být spojeny s původní tabulkou (pomocí sloupců primárního klíče) a mít vedle původních dat prvky počítání.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generování binningu
Následující příklad ukazuje, jak generovat přihrádkové prvky binning (pomocí pěti přihrádek) číselný sloupec, který lze použít jako prvek místo:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Zavedení funkcí z jednoho sloupce
V této části ukážeme, jak zavést jeden sloupec v tabulce pro generování dalších funkcí. Příklad předpokládá, že v tabulce, ze které se pokoušíte generovat funkce, je sloupec zeměpisné šířky nebo délky.

Zde je stručný základní informace o zeměpisné šířky a délky umístění dat (zdrojů z stackoverflow [Jak měřit přesnost zeměpisné šířky a délky?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Tyto pokyny jsou užitečné k pochopení před zahrnutím umístění jako jedné nebo více funkcí:

* Značka nám říká, zda jsme na severu nebo na jihu, na východ nebo na západ na světě.
* Nenulová stovka číslice nám říká, že používáme zeměpisnou délku, ne zeměpisnou šířku!
* Desítka dává pozici asi 1000 kilometrů. Poskytuje nám užitečné informace o tom, na jakém kontinentu nebo oceánu jsme.
* Číslice jednotek (jeden desetinný stupeň) udává polohu až 111 kilometrů (60 námořních mil, asi 69 mil). Může vám zhruba říci, v jakém státě, zemi nebo oblasti se nacházíte.
* První desetinné místo stojí až 11,1 km: dokáže rozlišit polohu jednoho velkého města od sousedního velkého města.
* Druhé desetinné místo má hodnotu až 1,1 km: může oddělit jednu vesnici od druhé.
* Třetí desetinné místo má hodnotu až 110 m: dokáže identifikovat velké zemědělské pole nebo institucionální kampus.
* Čtvrté desetinné místo má hodnotu až 11 m: může identifikovat pozemek. Je srovnatelná s typickou přesností nekorigované jednotky GPS bez rušení.
* Páté desetinné místo má hodnotu až 1,1 m: odlišuje stromy od sebe navzájem. Přesnosti na této úrovni s komerčními GPS jednotkami lze dosáhnout pouze s diferenciální korekcí.
* Šesté desetinné místo má hodnotu až 0,11 m: můžete jej použít pro podrobné rozložení staveb, pro navrhování krajin, stavbu silnic. Mělo by to být víc než dost dobré pro sledování pohybu ledovců a řek. Toho lze dosáhnout pečlivými opatřeními pomocí GPS, jako je diferenciálně korigovaná GPS.

Informace o poloze lze featurized takto, oddělující oblast, umístění a informace o městě. Můžete také volat koncový bod REST, jako je například rozhraní API Mapy Bing, které je k dispozici na adrese [Najít umístění podle bodu,](https://msdn.microsoft.com/library/ff701710.aspx) abyste získali informace o oblasti nebo okrese.

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

Tyto funkce založené na umístění lze dále použít ke generování dalších funkcí počítání, jak je popsáno výše. 

> [!TIP]
> Záznamy můžete programově vložit podle vlastního jazyka. Možná budete muset vložit data v blocích ke zlepšení efektivity zápisu (příklad, jak to udělat pomocí pyodbc, naleznete v [tématu HelloWorld ukázka pro přístup sqlserver s python).](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python) Další alternativou je vložení dat do databáze pomocí [nástroje BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaná funkce může být přidána jako sloupec do existující tabulky nebo uložena v nové tabulce a spojena s původní tabulkou pro strojové učení. Funkce lze generovat nebo přistupovat, pokud už byly vytvořeny, pomocí modulu [Import dat][import-data] v Azure Machine Learning, jak je znázorněno níže:

![čtečky azureml][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Použití programovacího jazyka, jako je Python
Použití Pythonu k prozkoumání dat a generování funkcí, když jsou data v SQL Serveru, je podobné zpracování dat v objektu blob Azure pomocí Pythonu, jak je zdokumentováno v [process Azure Blob data ve vašem prostředí datové vědy](data-blob.md). Načtěte data z databáze do datového rámce pandas pro další zpracování. Proces připojování k databázi a načítání dat do datového rámce dokumentujeme v této sekci.

Následující formát připojovacího řetězce lze použít pro připojení k databázi SQL Serveru z Pythonu pomocí pyodbc (nahradit název serveru, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna Pandas](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Níže uvedený kód přečte výsledky vrácené z databáze serveru SQL Server do datového rámce Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teď můžete pracovat s datovým rámcem Pandas, jak je uvedeno v článku [Zpracování dat objektů blob Azure ve vašem prostředí datové vědy](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Příklad azure datové vědy v akci
Příklad procesu azure datové vědy pomocí veřejné datové sady najdete v tématu [Proces vědeckého zpracování dat Azure v akci](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

