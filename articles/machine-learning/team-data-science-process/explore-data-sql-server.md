---
title: Zkoumat data v virtuálního počítače systému SQL Server na platformě Azure | Microsoft Docs
description: Jak chcete dynamicky prozkoumávat data, která je uložená ve virtuálním počítači serveru SQL v Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 68c8386917599afcf2819ff97453de77dea90215
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Zkoumání dat na virtuálním počítači s SQL Serverem v Azure
Tento dokument popisuje, jak chcete dynamicky prozkoumávat data, která je uložená ve virtuálním počítači serveru SQL v Azure. Tento krok můžete provést pomocí dat wrangling pomocí SQL nebo pomocí programovacího jazyka jako Python.

Následující **nabídky** odkazy na témata, které popisují, jak používat nástroje a prozkoumejte data z různých prostředích úložiště. Tato úloha je krok v procesu Cortana Analytics (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> Ukázkové příkazy SQL v tomto dokumentu předpokládají, že data jsou v systému SQL Server. Pokud tomu tak není, podívejte se na proces mapování cloudu dat vědecké účely se dozvíte, jak pro přesun dat do systému SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Prozkoumejte data SQL pomocí skriptů SQL
Tady jsou několik ukázkové skripty SQL, které lze použít k prozkoumání datová úložiště v systému SQL Server.

1. Získat počet připomínky za den
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Získat úrovně ve sloupci kategorií
   
    `select  distinct <column_name> from <databasename>`
3. Získat počet úrovní v kombinaci dvou kategorií sloupců 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Získat distribuce pro číselné sloupce
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Praktické příklad, můžete použít [datovou sadu NYC taxíkem](http://www.andresmh.com/nyctaxitrips/) a odkazovat na IPNB s názvem [NYC Data wrangling pomocí poznámkového bloku IPython a SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pro návod začátku do konce.
> 
> 

## <a name="python"></a>Prozkoumejte data SQL s Pythonem
Používá Python a zkoumat data funkce generovat, když jsou data v systému SQL Server je podobná zpracování dat v Azure blob pomocí Python, jak je uvedeno v [procesu Azure Blob dat ve vašem prostředí vědecké účely data](data-blob.md). Data musí být vždycky načtená z databáze do pandas DataFrame a pak můžete další zpracování. Jsme dokumentů proces připojení k databázi a načítání dat do DataFrame v této části.

Následující formátu řetězce připojení slouží k připojení k databázi systému SQL Server z Pythonu pomocí pyodbc (servername nahraďte, dbname, uživatelské jméno a heslo s konkrétními hodnotami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas knihovny](http://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datové struktury a nástrojů pro analýzu dat pro manipulaci s daty pro programování Python. Následující kód čte vráceny výsledky z databáze SQL serveru do rámečku Pandas dat:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teď můžete pracovat s Pandas DataFrame jako popsané v tématu [procesu Azure Blob dat ve vašem prostředí vědecké účely data](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Proces Team dat. vědecké účely v příkladu akce
Příklad začátku do konce návod o procesu analýzy Cortana použití veřejné datové sady, naleznete v části [Team datové vědy procesu v akci: pomocí SQL serveru](sql-walkthrough.md).

