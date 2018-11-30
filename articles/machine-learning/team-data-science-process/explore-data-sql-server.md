---
title: Zkoumání dat na virtuálním počítači SQL serveru v Azure | Dokumentace Microsoftu
description: Popisuje, jak zkoumat data, která je uložena v virtuálního počítače s SQL serverem v Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: da01a1a386e1ffc590f4cbbb85d9d50a180d4973
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442926"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Zkoumání dat na virtuálním počítači s SQL Serverem v Azure

Tento článek popisuje, jak zkoumat data, která je uložena v virtuálního počítače s SQL serverem v Azure. To můžete udělat tahání dat pomocí SQL nebo pomocí programovacího jazyka, jako je Python.

Tato úloha je nějaký krok [vědecké zpracování týmových dat](overview.md).

> [!NOTE]
> Ukázky příkazů SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na mapování proces vědy dat cloudu se naučíte přesunout data do SQL serveru.
> 
> 

## <a name="sql-dataexploration"></a>Zkoumání dat SQL pomocí skriptů SQL
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
> Například praktické, můžete použít [NYC taxislužby datovou sadu](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [tahání dat NYC pomocí SQL Server a IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
> 
> 

## <a name="python"></a>Zkoumání dat SQL s využitím Pythonu
Použití Pythonu k zkoumat data a vygenerovat funkce, když jsou data v systému SQL Server je podobný zpracování dat v Azure blob pomocí Pythonu, jak je uvedeno v [data objektů Blob v Azure procesu v prostředí pro datové vědy](data-blob.md). Data musí být načtena z databáze do pandas DataFrame a pak mohou být další zpracování. Dokumentujeme proces připojení k databázi a načítání dat do datového rámce v této části.

Následující formát připojovacího řetězce je možné se připojit k databázi SQL serveru z Pythonu pomocí pyodbc (nahraďte název_serveru, dbname, uživatelské jméno a heslo s určitými hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovny Pandas](http://pandas.pydata.org/) v Pythonu nabízí bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte výsledky vrácené z databáze SQL serveru do Pandas datového rámce:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat s balíčky Pandas DataFrame, jak je popsáno v tématu [data objektů Blob v Azure procesu v prostředí pro datové vědy](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Vědecké zpracování týmových dat v příkladu akce
Příklad začátku do konce Průvodce procesu sady Cortana Analytics pomocí veřejné datové sady, naleznete v tématu [vědecké zpracování týmových dat v akci: použití serveru SQL Server](sql-walkthrough.md).

