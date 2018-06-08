---
title: Vytvoření funkce pro data v systému SQL Server pomocí SQL a Python | Microsoft Docs
description: Zpracování dat z SQL Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: ''
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 432f7b9bb27c1dee396677c54edf48d9fdb027a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836330"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Vytvoření funkcí pro data v SQL Serveru pomocí jazyka SQL a Pythonu
Tento dokument ukazuje, jak vygenerovat funkcí pro data uložená v virtuální počítač SQL Server na platformě Azure, který pomůže algoritmy efektivněji dozvědět se od data. K provedení této úlohy můžete použít SQL nebo programovací jazyk jako Python. Zde je ukázán obou přístupů.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **nabídky** odkazy na témata, které popisují, jak vytvořit funkce pro data v různých prostředích. Tato úloha je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Praktické příkladu lze najít [datovou sadu NYC taxíkem](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC Data wrangling pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořit účet úložiště Azure. Pokud budete potřebovat pokyny, najdete v části [vytvoření účtu úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Vaše data uložena v systému SQL Server. Pokud máte není, najdete v části [přesun dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md) pokyny o tom, jak přesunout data existuje.

## <a name="sql-featuregen"></a>Funkce generování pomocí SQL
V této části popisují jsme způsoby generování funkcí s použitím SQL:  

1. [Počet na základě funkce generování](#sql-countfeature)
2. [Přihrádkování funkce generování](#sql-binningfeature)
3. [Zavedením funkce z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Po vygenerování další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který lze spojit s původní tabulky.
> 
> 

### <a name="sql-countfeature"></a>Na základě počtu funkce generování
Tento dokument ukazuje dva způsoby generování funkce count. První metoda používá podmíněného sum a druhé metody klauzuli 'where'. Tyto je pak možné připojit s původní tabulky (s použitím sloupců primárních klíčů) tak, aby měl funkce počet souběžně s původní data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Přihrádkování funkce generování
Následující příklad ukazuje, jak vygenerujte binned funkce přihrádkování (s použitím 5 přihrádek) číselné sloupce, který lze použít jako funkce místo:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Zavedením funkce z jednoho sloupce
V této části ukážeme, jak k zavedení jeden sloupec v tabulce ke generování dalších funkcí. Příklad předpokládá, že je v tabulce, ze kterého chcete generovat funkce sloupec zeměpisné šířky nebo délky.

Zde je stručný úvod do na data o umístění zeměpisnou šířku a délku (ze zásobníku se zdroji `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Zde jsou některé užitečné věci zjistit o data o umístění, před vytvořením funkce z pole:

* Přihlašovací označuje, jestli Snažíme se severně nebo – Jih, – východ nebo – západ na celém světě.
* Nenulové hodnoty stovky číslice určuje zeměpisnou délku, používá není zeměpisné šířky.
* Desítkami číslice dává pozice do asi 1000 kilometrech. Nabízí užitečné informace o jaké kontinentě nebo oceánu jsme na.
* Jednotky číslice (jeden decimal stupeň) poskytuje na pozici až 111 kilometrech (60 mílové, o 69 miles). Označuje, zhruba, jaké velké státu nebo země Snažíme se v.
* Na jedno desetinné místo je vhodné až 11.1 km: je možné rozlišit pozici jedno velké město z sousedních velké města.
* Dvě desetinná místa je vhodné až 1.1 km: ho jeden vesnice nezávislá na další.
* Může zjistit velké zemědělských pole či institucionální univerzity vhodné až 110 m: je na tři desetinná místa.
* Může zjistit parcela čtvrtého desetinného místa je vhodné m: až 11. Je srovnatelná typické přesnost neopravené GPS jednotky s bez narušení.
* Páté desetinné místo je vhodné až 1.1 m: že stromy ho odlišuje od sebe navzájem. Přesnost do této úrovně s komerční GPS jednotky lze dosáhnout pouze s rozdílovou oprava.
* Šesté desetinné místo je vhodné až 0,11 m: že tu můžete použít pro vytvoření rozložení struktury podrobně pro návrh krajiny, vytváření cest. Mělo by být víc než dost vhodný pro sledování pohybu glaciers a řek. Toho lze dosáhnout pomocí painstaking míry s GPS, jako je například differentially opravené GPS.

Informace o umístění může být featurized oddělením oblast, umístění a informace o městě. Všimněte si, že jednou můžete také zavolat koncový bod REST například rozhraní API map Bing k dispozici na `https://msdn.microsoft.com/library/ff701710.aspx` získat informace o oblasti nebo oblasti.

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

Tyto funkce na základě umístění další lze vygenerujte počet další funkce, jak je popsáno výše.

> [!TIP]
> Prostřednictvím kódu programu můžete vložit záznamů pomocí vámi zvolený jazyk. Musíte vložit data v blocích pro zlepšení efektivity zápisu. [Tady je příklad toho, jak to provést pomocí pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Další alternativou je k vkládání dat v databázi pomocí [nástroj BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Nově vygenerovaný funkce můžete přidat jako sloupec do existující tabulky nebo ukládat v nové tabulce a spojena s původní tabulky pro machine learning. Funkce můžete generovat ani přistupovat, pokud už vytvořili, pomocí [importovat Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modulu v Azure ML, jak je uvedeno níže:

![Čtečky Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Pomocí programovacího jazyka jako Python
Generování funkce, když jsou data v systému SQL Server pomocí Python je podobná zpracování dat v Azure blob pomocí Pythonu. Porovnání najdete v tématu [procesu Azure Blob dat ve vašem prostředí vědecké účely data](data-blob.md). Načíst data z databáze do rámečku data pandas zpracovat další. Proces připojení k databázi a načítání dat do data rámečku je popsané v této části.

Následující formátu řetězce připojení slouží k připojení k databázi systému SQL Server z Pythonu pomocí pyodbc (servername nahraďte, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas knihovny](http://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datové struktury a nástrojů pro analýzu dat pro manipulaci s daty pro programování Python. Následující kód čte vráceny výsledky z databáze SQL serveru do rámečku Pandas dat:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat s rámečkem data Pandas jako zahrnutých v tématech [vytvořit funkcí pro data úložiště objektů blob v Azure pomocí Panda](create-features-blob.md).

