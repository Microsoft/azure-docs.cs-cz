---
title: Vytváření funkcí v SQL Server pomocí SQL a Pythonu – týmových datových vědeckých procesů
description: Vygenerujte funkce pro data uložená v SQL Serverm VIRTUÁLNÍm počítači v Azure pomocí SQL a Pythonu v rámci vědeckého procesu týmového zpracování dat.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322043"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Vytvoření funkcí pro data v SQL Serveru pomocí jazyka SQL a Pythonu
V tomto dokumentu se dozvíte, jak vygenerovat funkce pro data uložená v SQL Serverm VIRTUÁLNÍm počítači v Azure, které pomůžou z dat efektivněji učit algoritmy. K provedení této úlohy můžete použít SQL nebo programovací jazyk, jako je Python. Oba přístupy jsou znázorněné tady.

Tento úkol je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

> [!NOTE]
> Pro praktický příklad si můžete projít [datovou sadu taxislužby NYC](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data tahání pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro ucelený návod.
> 
> 

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte následující:

* Vytvořili jste účet úložiště Azure. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Data uložená v SQL Server. Pokud ne, přečtěte si téma [přesunutí dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md) , kde najdete pokyny k přesunu dat.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generování funkcí pomocí SQL
V této části popíšeme způsoby generování funkcí pomocí SQL:  

* [Generace funkcí na základě počtu](#sql-countfeature)
* [Generace funkcí binningu](#sql-binningfeature)
* [Zavádění funkcí z jednoho sloupce](#sql-featurerollout)

> [!NOTE]
> Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárními klíči, které lze propojit s původní tabulkou.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generace funkcí na základě počtu
Tento dokument ukazuje dva způsoby, jak vygenerovat funkce Count. První metoda používá Podmíněný součet a druhá metoda používá klauzuli WHERE. Tyto nové funkce se pak dají spojit s původní tabulkou (pomocí primárního klíčového sloupce), aby byly k dispozici funkce Count spolu s původními daty.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generace funkcí binningu
Následující příklad ukazuje, jak generovat funkce rozdělený pomocí binningu (pomocí pěti přihrádek) číselný sloupec, který lze použít jako funkci místo toho:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Zavádění funkcí z jednoho sloupce
V této části ukážeme, jak v tabulce zavést jeden sloupec pro generování dalších funkcí. V příkladu se předpokládá, že je v tabulce, ze které se pokoušíte generovat funkce, sloupec Zeměpisná šířka nebo zeměpisná délka.

Tady je stručný úvod k datům o poloze/Zeměpisná šířka (znovu se zdroji z StackOverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` ). Tady jsou některé užitečné věci, které vám pomohou pochopit data o poloze před vytvořením funkcí z tohoto pole:

* Symbol označuje, zda se jedná o Severní nebo jižní, východní nebo západní na světě.
* Nenulová stovka číslice značí zeměpisnou délku, nepoužívá zeměpisnou šířku.
* Desítková číslice poskytuje pozici přibližně 1 000 kilometrů. Poskytuje užitečné informace o tom, na jakém kontinentu nebo oceánu máme.
* Číslice jednotek (jedna desetinná hladina) dává pozici až 111 kilometrů (60 námořních mil, přibližně 69 mil.). Indikuje zhruba, jaký velký stav nebo země nebo oblast tam máme.
* První desetinné místo je až 11,1 km: může rozlišovat polohu jednoho velkého města od sousedního města.
* Druhé desetinné místo je až 1,1 km: může oddělit jeden Village od dalšího.
* Třetí desetinné místo má hodnotu až 110 m: může identifikovat velké zemědělské pole nebo institucionální areály.
* Čtvrté desetinné místo je až 11 m: může identifikovat pozemek půdy. Je srovnatelná s typickou přesností nesprávné jednotky GPS bez jakýchkoli rušivých zásahů.
* Pátý počet desetinných míst je až 1,1 m: rozliší stromy od sebe navzájem. Přesnost na tuto úroveň u komerčních jednotek GPS se dá dosáhnout jenom s rozdílovou korekcí.
* Šest desetinných míst je rovno 0,11 m: můžete použít tuto úroveň pro navrhování struktur podrobněji pro návrh krajiny a vytváření silnic. Mělo by být dostatečně dobré pro sledování pohybů Glaciers a řek. Tento cíl je možné dosáhnout tím, že přijímáte painstaking míry pomocí GPS, jako je rozdílově korigovaný GPS.

Informace o umístění může být natrénuje oddělením informací o oblasti, umístění a městech. Jednou taky může zavolat koncový bod REST, jako je například rozhraní API služby Bing Maps (viz `https://msdn.microsoft.com/library/ff701710.aspx` získání informací o oblasti nebo oblasti).

```sql
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
```

Tyto funkce založené na umístění je možné dále využít ke generování dalších funkcí Count, jak je popsáno výše.

> [!TIP]
> Záznamy můžete programově vkládat pomocí vámi zvoleného jazyka. Možná budete muset vložit data do bloků dat, aby se zlepšila efektivita zápisu. [Tady je příklad, jak to provést pomocí pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Další možností je vkládat data do databáze pomocí [nástroje BCP](/sql/tools/bcp-utility) .
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Připojování k Azure Machine Learning
Nově vygenerovanou funkci lze přidat jako sloupec do existující tabulky nebo Uložit do nové tabulky a spojit s původní tabulkou pro strojové učení. Funkce se dají vygenerovat nebo zobrazit, pokud už se vytvořily, pomocí modulu [Import dat](/azure/machine-learning/studio-module-reference/import-data) v Azure ml, jak je znázorněno níže:

![Čtenáři Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Používání programovacího jazyka, jako je Python
Použití Pythonu ke generování funkcí v případě, že jsou data v SQL Server podobná zpracování dat v Azure BLOB pomocí Pythonu. Porovnání najdete v tématu [zpracování dat objektů BLOB v Azure ve vašem prostředí pro datové vědy](data-blob.md). Načtěte data z databáze do datového rámce PANDAS, abyste ji mohli dále zpracovat. V této části je popsán proces připojení k databázi a načtení dat do datového rámce.

Následující formát připojovacího řetězce se dá použít k připojení k SQL Server databázi z Pythonu pomocí pyodbc (nahraďte servername, dbname, username a Password konkrétními hodnotami):

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

[Knihovna PANDAS](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte výsledky vrácené z databáze SQL Server do datového rámce PANDAS:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Nyní můžete pracovat s datovým rámcem PANDAS, jak je popsáno v tématech [vytváření funkcí pro data služby Azure Blob Storage pomocí programu Panda](./explore-data-blob.md).