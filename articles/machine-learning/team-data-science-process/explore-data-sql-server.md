---
title: Zkoumání dat v systému SQL Server virtuální počítač – vědecké zpracování týmových dat
description: Popisuje, jak zkoumat data, která je uložena v virtuálního počítače s SQL serverem v Azure s využitím SQL nebo programovací jazyk, jako je Python.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720091"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Zkoumání dat na virtuálním počítači s SQL Serverem v Azure

Tento článek popisuje, jak zkoumat data, která je uložena v virtuálního počítače s SQL serverem v Azure. K prohlédnutí dat použijte SQL nebo Python.

Tento úkol je krok v rámci [vědeckého procesu týmového zpracování dat](overview.md).

> [!NOTE]
> Ukázky příkazů SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na mapování proces vědy dat cloudu se naučíte přesunout data do SQL serveru.
> 
> 

## <a name="sql-dataexploration"></a>Prozkoumat data SQL pomocí skriptů SQL
Tady je několik ukázky skriptů SQL, které lze použít k prozkoumání úložiště dat v systému SQL Server.

1. Získat počet vyjádření za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získání úrovně ve sloupci zařazené do kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získat počet úrovní v kombinaci dva sloupce a bude zařazené do kategorií 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získání distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Pro praktické příklady můžete použít [datovou sadu taxislužby NYC](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC data tahání pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro ucelený návod.
> 
> 

## <a name="python"></a>Prozkoumat data SQL pomocí Pythonu
Použití Pythonu k prozkoumávání dat a generování funkcí, když jsou data v SQL Server podobná zpracování dat v Azure BLOB pomocí Pythonu, jak je popsáno v části [zpracování dat objektů BLOB v Azure v prostředí pro datové vědy](data-blob.md). Načtěte data z databáze do PANDAS dataframe a pak je můžete zpracovat dále. Dokumentujeme proces připojení k databázi a načítání dat do datového rámce v této části.

Následující formát připojovacího řetězce je možné se připojit k databázi SQL serveru z Pythonu pomocí pyodbc (nahraďte název_serveru, dbname, uživatelské jméno a heslo s určitými hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna PANDAS](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte výsledky vrácené z databáze SQL serveru do Pandas datového rámce:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nyní můžete pracovat s PANDAS dataframe, jak je popsáno v tématu [zpracování dat objektů BLOB v Azure ve vašem prostředí pro datové vědy](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Vědecké zpracování týmových dat v příkladu akce
Podrobný příklad procesu Cortana Analytics, který používá veřejnou datovou sadu, najdete v tématu věnovaném [vědeckému zpracování týmových dat v akci: using SQL Server](sql-walkthrough.md).

