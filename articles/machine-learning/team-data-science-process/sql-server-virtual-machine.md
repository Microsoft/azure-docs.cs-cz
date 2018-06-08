---
title: Prozkoumejte data virtuálního počítače s SQL serverem v Azure | Microsoft Docs
description: Zkoumat data a funkce generování ve virtuálním počítači systému SQL Server v Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: ''
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: deguhath
ms.openlocfilehash: 39bdbce4ada225c0fa7df8559f68b591ccfc68b7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838652"
---
# <a name="heading"></a>Zpracování dat v virtuálního počítače systému SQL Server v Azure
Tento dokument popisuje, jak procházet data a vygenerovat funkcí pro data uložená ve virtuálním počítači serveru SQL v Azure. Tento krok můžete provést pomocí dat wrangling pomocí SQL nebo pomocí programovacího jazyka jako Python.

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na proces mapování cloudu dat vědecké účely se dozvíte, jak pro přesun dat do systému SQL Server.
> 
> 

## <a name="SQL"></a>Pomocí SQL
Jsme popisují následující úlohy wrangling dat v této části pomocí SQL:

1. [Zkoumání dat](#sql-dataexploration)
2. [Funkce generování](#sql-featuregen)

### <a name="sql-dataexploration"></a>Zkoumání dat
Tady jsou několik ukázkové skripty SQL, které lze použít k prozkoumání datová úložiště v systému SQL Server.

> [!NOTE]
> Praktické příklad, můžete použít [datovou sadu NYC taxíkem](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC Data wrangling pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
> 
> 

1. Získat počet připomínky za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získat úrovně ve sloupci kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získat počet úrovní v kombinaci dvou kategorií sloupců 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získat distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Funkce generování
V této části popisují jsme způsoby generování funkcí s použitím SQL:  

1. [Počet na základě funkce generování](#sql-countfeature)
2. [Přihrádkování funkce generování](#sql-binningfeature)
3. [Zavedením funkce z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Po vygenerování další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který lze spojit s původní tabulky. 
> 
> 

### <a name="sql-countfeature"></a>Počet na základě funkce generování
Následující příklady ukazují dva způsoby generování funkce count. První metoda používá podmíněného sum a druhé metody klauzuli 'where'. Tyto je pak možné připojit s původní tabulky (s použitím sloupců primárních klíčů) tak, aby měl funkce počet souběžně s původní data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Přihrádkování funkce generování
Následující příklad ukazuje, jak vygenerujte binned funkce přihrádkování (pomocí pět přihrádek) číselné sloupce, který lze použít jako funkce místo:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Zavedením funkce z jednoho sloupce
V této části ukážeme, jak k zavedení jeden sloupec v tabulce ke generování dalších funkcí. Příklad předpokládá, že je v tabulce, ze kterého chcete generovat funkce sloupec zeměpisné šířky nebo délky.

Zde je stručný úvod do na data o umístění zeměpisnou šířku a délku (ze zásobníku se zdroji [postupy: měření přesnost zeměpisnou šířku a délku?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). To je užitečné zjistit před featurizing pole umístění:

* Přihlašovací informuje nám jestli jsme jsou severní nebo – Jih, východ nebo – západ na celém světě.
* Nenulové hodnoty stovky číslice víme, že používáme zeměpisné délky, není zeměpisnou šířku!
* Desítkami číslice dává pozice do asi 1000 kilometrech. Nabízí nám užitečné informace o jaké kontinentě nebo oceánu jsme na.
* Jednotky číslice (jeden decimal stupeň) poskytuje na pozici až 111 kilometrech (60 mílové, o 69 miles). Je nám říct zhruba jaké velké státu nebo země, které jsme jsou v.
* Na jedno desetinné místo je vhodné až 11.1 km: je možné rozlišit pozici jedno velké město z sousedních velké města.
* Dvě desetinná místa je vhodné až 1.1 km: ho jeden vesnice nezávislá na další.
* Může zjistit velké zemědělských pole či institucionální univerzity vhodné až 110 m: je na tři desetinná místa.
* Může zjistit parcela čtvrtého desetinného místa je vhodné m: až 11. Je srovnatelná typické přesnost neopravené GPS jednotky s bez narušení.
* Páté desetinné místo je vhodné až 1.1 m: že stromy ho odlišuje od sebe navzájem. Přesnost do této úrovně s komerční GPS jednotky lze dosáhnout pouze s rozdílovou oprava.
* Šesté desetinné místo je vhodné až 0,11 m: že tu můžete použít pro vytvoření rozložení struktury podrobně pro návrh krajiny, vytváření cest. Mělo by být víc než dost vhodný pro sledování pohybu glaciers a řek. Toho lze dosáhnout pomocí painstaking míry s GPS, jako je například differentially opravené GPS.

Informace o umístění může být featurized následujícím způsobem oddělení oblast, umístění a informace o městě. Všimněte si, že byste také zavolat koncový bod REST například rozhraní API map Bing k dispozici na [vyhledat umístění bodem](https://msdn.microsoft.com/library/ff701710.aspx) získat informace o oblasti nebo oblasti.

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

Tyto funkce na základě polohy další slouží ke generování dalších počet funkcí, jak je popsáno výše. 

> [!TIP]
> Prostřednictvím kódu programu můžete vložit záznamů pomocí vámi zvolený jazyk. Budete muset vložit data v blocích pro zlepšení efektivity zápisu (příklad toho, jak to provést pomocí pyodbc, naleznete v části [ukázka A HelloWorld pro přístup k SQL Server s pythonem](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Další alternativou je k vkládání dat v databázi pomocí [nástroj BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaný funkce můžete přidat jako sloupec do existující tabulky nebo ukládat v nové tabulce a spojena s původní tabulky pro machine learning. Funkce můžete generovat ani přistupovat, pokud už vytvořili, pomocí [importovat Data] [ import-data] modulu v Azure Machine Learning, jak je uvedeno níže:

![azureml čtečky][1] 

## <a name="python"></a>Pomocí programovacího jazyka jako Python
Používá Python a zkoumat data funkce generovat, když jsou data v systému SQL Server je podobná zpracování dat v Azure blob, které se používá Python, jak je uvedeno v [procesu Azure Blob dat ve vašem prostředí vědecké účely data](data-blob.md). Data musí být načtená z databáze do rámečku pandas data a pak můžete další zpracování. Jsme dokumentů proces připojení k databázi a načítání dat do rámečku dat v této části.

Následující formátu řetězce připojení slouží k připojení k databázi systému SQL Server z Pythonu pomocí pyodbc (servername nahraďte, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas knihovny](http://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datové struktury a nástrojů pro analýzu dat pro manipulaci s daty pro programování Python. Následující kód čte vráceny výsledky z databáze SQL serveru do rámečku Pandas dat:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat s rámečkem data Pandas jako popsané v článku [procesu Azure Blob dat ve vašem prostředí vědecké účely data](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Vědecké zpracování dat Azure v příkladu akce
Příklad začátku do konce návod procesu vědecké účely dat Azure pomocí veřejné datové sady, naleznete v části [proces vědecké účely dat Azure v akci](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

