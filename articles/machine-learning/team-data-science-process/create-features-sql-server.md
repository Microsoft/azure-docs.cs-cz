---
title: Vytváření funkcí v SQL Serveru pomocí SQL a Pythonu – proces vědecké analýzy týmových dat
description: Generujte funkce pro data uložená v virtuálním počítači SQL Server v Azure pomocí SQL a Pythonu – součást procesu vědecké analýzy týmových dat.
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
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721740"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Vytvoření funkcí pro data v SQL Serveru pomocí jazyka SQL a Pythonu
Tento dokument ukazuje, jak generovat funkce pro data uložená ve virtuálním počítači SQL Server v Azure, které pomáhají algoritmům efektivněji zjistit z dat. K provedení tohoto úkolu můžete použít SQL nebo programovací jazyk, jako je Python. Oba přístupy jsou zde demonstrovány.

Tento úkol je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

> [!NOTE]
> Praktický příklad můžete konzultovat [nyc taxi datové sady](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data hašteření pomocí IPython notebook a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro end-to-end návod.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořil účet úložiště Azure. Pokud potřebujete pokyny, přečtěte [si tématy Vytvoření účtu Azure Storage.](../../storage/common/storage-account-create.md)
* Uložená data na serveru SQL Server. Pokud nemáte, najdete [v tématu Přesunutí dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md) pokyny, jak přesunout data tam.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generování funkcí s SQL
V této části popisujeme způsoby generování funkcí pomocí sql:  

* [Generování funkcí založených na počtu](#sql-countfeature)
* [Generování binningu](#sql-binningfeature)
* [Zavedení funkcí z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárním klíčem, které lze spojit s původní tabulkou.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generování funkcí založených na počtu
Tento dokument ukazuje dva způsoby generování funkcí počítání. První metoda používá podmíněný součet a druhá metoda používá klauzuli "where". Tyto nové funkce pak mohou být spojeny s původní tabulkou (pomocí sloupců primárního klíče) a mít vedle původních dat prvky počítání.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generování binningu
Následující příklad ukazuje, jak generovat přihrádkové prvky binning (pomocí pěti přihrádek) číselný sloupec, který lze použít jako prvek místo:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Zavedení funkcí z jednoho sloupce
V této části ukážeme, jak zavést jeden sloupec v tabulce pro generování dalších funkcí. Příklad předpokládá, že v tabulce, ze které se pokoušíte generovat funkce, je sloupec zeměpisné šířky nebo délky.

Zde je stručný základní nátěr na zeměpisné šířky a délky `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`umístění dat (zdrojů z stackoverflow). Zde je několik užitečných věcí, které je třeba pochopit o lokalizačních datech před vytvořením funkcí z pole:

* Značka označuje, zda jsme na severu nebo jihu, na východnebo západ na zeměkouli.
* Nenulová číslice sta označuje zeměpisnou délku, nikoli zeměpisnou šířku.
* Desítka dává pozici asi 1000 kilometrů. Poskytuje užitečné informace o tom, na jakém kontinentu nebo oceánu jsme.
* Číslice jednotek (jeden desetinný stupeň) udává polohu až 111 kilometrů (60 námořních mil, asi 69 mil). To naznačuje, zhruba, co velký stát nebo země / region jsme v.
* První desetinné místo stojí až 11,1 km: dokáže rozlišit polohu jednoho velkého města od sousedního velkého města.
* Druhé desetinné místo má hodnotu až 1,1 km: může oddělit jednu vesnici od druhé.
* Třetí desetinné místo má hodnotu až 110 m: dokáže identifikovat velké zemědělské pole nebo institucionální kampus.
* Čtvrté desetinné místo má hodnotu až 11 m: může identifikovat pozemek. Je srovnatelná s typickou přesností nekorigované jednotky GPS bez rušení.
* Páté desetinné místo má hodnotu až 1,1 m: odlišuje stromy od sebe navzájem. Přesnosti na této úrovni s komerčními GPS jednotkami lze dosáhnout pouze s diferenciální korekcí.
* Šesté desetinné místo má hodnotu až 0,11 m: tuto úroveň můžete použít pro podrobné rozkládání staveb, pro navrhování krajin, stavbu silnic. Mělo by to být víc než dost dobré pro sledování pohybu ledovců a řek. Tohoto cíle lze dosáhnout pečlivými opatřeními pomocí GPS, jako je například diferenciálně korigovaná GPS.

Informace o poloze lze provést oddělením informací o oblasti, umístění a městě. Jednou můžete také volat koncový bod REST, jako `https://msdn.microsoft.com/library/ff701710.aspx` je například Rozhraní API Mapy Bing (viz získání informací o oblasti nebo okresu).

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
> Záznamy můžete programově vložit podle vlastního jazyka. Možná budete muset vložit data do bloků dat ke zlepšení efektivity zápisu. [Zde je příklad, jak to udělat pomocí pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Další alternativou je vložení dat do databáze pomocí [nástroje BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaná funkce může být přidána jako sloupec do existující tabulky nebo uložena v nové tabulce a spojena s původní tabulkou pro strojové učení. Funkce lze generovat nebo přistupovat, pokud již byly vytvořeny, pomocí modulu [Import dat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) v Azure ML, jak je znázorněno níže:

![Čtečky Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Použití programovacího jazyka, jako je Python
Použití Pythonu ke generování funkcí, když jsou data v SQL Serveru, je podobné zpracování dat v objektu blob Azure pomocí Pythonu. Pro srovnání najdete [v tématu Zpracování dat objektů blob Azure v prostředí datové vědy](data-blob.md). Načtěte data z databáze do datového rámce pandas, abyste je dále zpracovali. Proces připojení k databázi a načítání dat do datového rámce je popsán v této části.

Následující formát připojovacího řetězce lze použít pro připojení k databázi SQL Serveru z Pythonu pomocí pyodbc (nahradit název serveru, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna Pandas](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte výsledky vrácené z databáze serveru SQL Server do datového rámce Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teď můžete pracovat s datovým rámcem Pandas, jak je popsáno v tématech [Vytvořit funkce pro data úložiště objektů blob Azure pomocí Panda](create-features-blob.md).

