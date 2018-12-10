---
title: Vytvoření funkcí v SQL serveru pomocí SQL a Python - vědecké zpracování týmových dat
description: Generovat funkcí pro data uložená v virtuálního počítače s SQL serverem v Azure pomocí jazyka SQL a Pythonu – část vědecké zpracování týmových dat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bf6f27b7720cf4a30aa08ca02f9150aebe564244
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135929"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Vytvoření funkcí pro data v SQL Serveru pomocí jazyka SQL a Pythonu
Tento dokument ukazuje, jak generovat funkcí pro data uložená v virtuálního počítače s SQL serverem v Azure, který pomůže algoritmy, Učte se od data efektivněji. K provedení této úlohy můžete použít SQL nebo programovací jazyk, jako je Python. Oba přístupy jsou zde popsané.

Tato úloha je nějaký krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Praktické příklad lze najít [NYC taxislužby datovou sadu](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [tahání dat NYC pomocí SQL Server a IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete pokyny, přečtěte si [vytvoření účtu služby Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Vaše data uložená na SQL serveru. Pokud ne, najdete v článku [přesun dat do služby Azure SQL Database pro Azure Machine Learning](move-sql-azure.md) pokyny o tom, jak přesunout data.

## <a name="sql-featuregen"></a>Funkce generování pomocí jazyka SQL
V této části popisujeme možnosti generování funkcí s použitím SQL:  

1. [Počet na základě funkcí generace](#sql-countfeature)
2. [Binning funkci generování](#sql-binningfeature)
3. [Použití funkce z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který jde připojit k původní tabulky.
> 
> 

### <a name="sql-countfeature"></a>Funkce generování podle počtu
Tento dokument ukazuje dva způsoby generování počet funkcí. První metoda používá podmíněný součet a druhá metoda používá klauzuli 'where'. Ty potom jde připojit k původní tabulky (s použitím sloupce s primárním klíčem) mít počet funkcí společně s původními daty.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Binning funkci generování
Následující příklad ukazuje, jak generovat rozdělený na intervaly funkce podle binning (přihrádkami 5) číselný sloupec, který lze použít jako funkci:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Použití funkce z jednoho sloupce
V této části jsme ukazují, jak zavést jeden sloupec v tabulce k vygenerování dalších funkcí. Příklad předpokládá, že je v tabulce, ze kterého jste se pokoušeli vygenerovat funkce sloupec zeměpisné šířky a délky.

Tady je stručný úvod do data o poloze zeměpisnou šířkou/délkou (zdroje z stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Tady jsou některé užitečné možnosti pochopit o umístění dat před vytvořením funkce z pole:

* Znaménko označuje Určuje, zda se sever nebo – Jih, východní nebo – západ na celém světě.
* Nenulový stovky číslice znamená zeměpisnou délku, ne zeměpisná šířka se používá.
* Desítkami číslic poskytuje schopen přibližně 1 000 kilometrů. Poskytuje užitečné informace o jaké kontinentu nebo se na oceánských.
* Jednotky číslice (jednu míru decimal) poskytuje pozici 111 kilometrů (60 mílové, asi 69 mil). Znamená to, zhruba, jaké velké státě nebo zemi máme.
* Na jedno desetinné místo stojí až 11.1 km: ho odlišili pozice jedno město velké ze sousedních město velké.
* Druhé desetinné stojí až 1.1 km: ho jeden vesnice nezávislá na další.
* Můžete identifikovat velké zemědělská pole nebo institucionální campus které stojí za to, až 110 m: je třetí desetinné čárky.
* Může zjistit balení pozemního které stojí za to, až 11 m: je čtvrtý desetinné čárky. Je srovnatelná typické přesnost neopraveno GPS jednotky s bez rušení.
* Pátý desetinné čárky je vhodné až 1.1 m: že stromů ho odlišuje od sebe navzájem. Přesnost na tuto úroveň díky obchodní jednotky GPS lze nastavit pouze pomocí rozdílové opravy.
* Šestý desetinné místo stojí až 0,11 m:, že může být využit k rozložení struktury podrobně pro navrhování spolehnut, vytváření silnicích zakázána. Měla by být více než dostatečné pro sledování pohybu glaciers a řek. Toho lze dosáhnout pomocí painstaking míry pomocí GPS, jako je například differentially opravený GPS.

Informace o umístění může být natrénuje oddělením mimo oblast, umístění a Město informace. Všimněte si, že se jednou můžete také volat koncový bod REST, jako je například rozhraní API map Bing k dispozici na `https://msdn.microsoft.com/library/ff701710.aspx` zobrazíte informace o oblasti a oblasti.

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

Tyto funkce na základě umístění je možné dále generovat další počet funkcí, jak je popsáno výše.

> [!TIP]
> Programově můžete vložit záznamy pomocí vašich jazyk podle vlastní volby. Budete muset vložit data za účelem zlepšení efektivity zápisu. [Tady je příklad toho, jak to udělat pomocí pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Další možností je k vložení dat do databáze pomocí [nástroj BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaný funkce můžete přidat jako sloupec do existující tabulky nebo uložené do nové tabulky a spojen s původní tabulky pro službu machine learning. Můžete generovat ani přistupovat, pokud už vytvořili, pomocí funkce [Import dat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) moduly v Azure ML, jak je znázorněno níže:

![Čtenáři Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Pomocí programovacího jazyka, jako je Python
Použití Pythonu k vygenerování funkce data umístěná v systému SQL Server je podobný zpracování dat v Azure blob pomocí Pythonu. Porovnání najdete v tématu [data objektů Blob v Azure procesu v prostředí pro datové vědy](data-blob.md). Načtěte data z databáze do datový rámec pandas ke zpracování další. Proces připojení k databázi a načítání dat do datového rámce je popsané v této části.

Následující formát připojovacího řetězce je možné se připojit k databázi SQL serveru z Pythonu pomocí pyodbc (nahraďte název_serveru, dbname, uživatelské jméno a heslo s určitými hodnotami):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovny Pandas](http://pandas.pydata.org/) v Pythonu nabízí bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte výsledky vrácené z databáze SQL serveru do Pandas datového rámce:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat se datový rámec Pandas, jak je popsáno v tématech [vytvoření funkcí pro data objektů blob v Azure storage pomocí knihovny Pandas](create-features-blob.md).

