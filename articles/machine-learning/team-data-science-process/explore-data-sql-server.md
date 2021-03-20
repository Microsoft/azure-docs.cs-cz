---
title: Prozkoumat data v SQL Serverm virtuálním počítači – vědeckým zpracováním týmových dat
description: Jak prozkoumat data uložená v SQL Serverm VIRTUÁLNÍm počítači v Azure pomocí SQL nebo programovacího jazyka, jako je Python.
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86085646"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Zkoumání dat na virtuálním počítači s SQL Serverem v Azure

Tento článek popisuje, jak prozkoumat data uložená v SQL Serverm VIRTUÁLNÍm počítači v Azure. K prohlédnutí dat použijte SQL nebo Python.

Tento úkol je krok v rámci [vědeckého procesu týmového zpracování dat](overview.md).

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že jsou data v SQL Server. Pokud tomu tak není, přečtěte si článek mapa vědeckých procesů cloudových dat, kde se dozvíte, jak přesunout data do SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Prozkoumat data SQL pomocí skriptů SQL
Tady je několik ukázkových skriptů SQL, které je možné použít k prozkoumávání úložišť dat v SQL Server.

1. Získá počet pozorování za den.
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získat úrovně ve sloupci kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získá počet úrovní v kombinaci dvou kategorií sloupců. 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získat distribuci pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Pro praktické příklady můžete použít [datovou sadu taxislužby NYC](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data tahání pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro ucelený návod.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Prozkoumat data SQL pomocí Pythonu
Použití Pythonu k prozkoumávání dat a generování funkcí, když jsou data v SQL Server podobná zpracování dat v Azure BLOB pomocí Pythonu, jak je popsáno v části [zpracování dat objektů BLOB v Azure v prostředí pro datové vědy](data-blob.md). Načtěte data z databáze do PANDAS dataframe a pak je můžete zpracovat dále. Dokument se připojuje k databázi a načítá data do datového rámce v této části.

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

Nyní můžete pracovat s PANDAS dataframe, jak je popsáno v tématu [zpracování dat objektů BLOB v Azure ve vašem prostředí pro datové vědy](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Příklad vědeckého zpracování týmových dat v akci
Podrobný příklad procesu Cortana Analytics, který používá veřejnou datovou sadu, najdete v tématu věnovaném [vědeckému zpracování týmových dat v akci: using SQL Server](sql-walkthrough.md).

