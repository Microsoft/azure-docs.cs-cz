---
title: Prozkoumejte data ve virtuálním počítači SQL Serveru – proces vědecké vědy o týmových datech
description: Jak prozkoumat data uložená v virtuálním počítači SQL Server v Azure pomocí SQL nebo programovacího jazyka, jako je Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720091"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Zkoumání dat na virtuálním počítači s SQL Serverem v Azure

Tento článek popisuje, jak prozkoumat data, která je uložená ve virtuálním počítači SQL Server v Azure. Pomocí SQL nebo Pythonu zkontrolujte data.

Tento úkol je krokem v [procesu vědecké vědy o týmových datech](overview.md).

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že data jsou na serveru SQL Server. Pokud tomu tak není, podívejte se na mapu procesu cloudové datové vědy a zjistěte, jak přesunout data na SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Prozkoumejte data SQL pomocí skriptů SQL
Zde je několik ukázkových skriptů SQL, které lze použít k prozkoumání úložišť dat na serveru SQL Server.

1. Získejte počet pozorování za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získání úrovní v kategorickém sloupci
   
    `select  distinct <column_name> from <databasename>`
3. Získání počtu úrovní v kombinaci dvou kategorických sloupců 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získání distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Pro praktický příklad můžete použít [NYC Taxi datové sady](https://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem NYC data [hašteření pomocí IPython notebook a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro end-to-end návod.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Prozkoumejte data SQL v Pythonu
Použití Pythonu k prozkoumání dat a generování funkcí, když jsou data v SQL Serveru, je podobné zpracování dat v objektu blob Azure pomocí Pythonu, jak je zdokumentováno v [process Azure Blob data ve vašem prostředí datové vědy](data-blob.md). Načtěte data z databáze do pandas DataFrame a pak mohou být dále zpracovány. Proces připojování k databázi a načítání dat do datového rámce dokumentujeme v této části.

Následující formát připojovacího řetězce lze použít pro připojení k databázi SQL Serveru z Pythonu pomocí pyodbc (nahradit název serveru, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Knihovna Pandas](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte výsledky vrácené z databáze serveru SQL Server do datového rámce Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teď můžete pracovat s Datovým rámem Pandy, jak je popsáno v tématu [Zpracování dat objektů blob Azure ve vašem prostředí datové vědy](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Příklad vědecké ho procesu týmových dat v akci
Příklad procesu analýzy Cortany pomocí veřejné datové sady najdete v tématu [Proces vědecké analýzy týmových dat v akci: pomocí serveru SQL Server](sql-walkthrough.md).

