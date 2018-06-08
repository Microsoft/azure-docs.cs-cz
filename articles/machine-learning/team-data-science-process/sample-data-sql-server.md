---
title: Ukázková Data v systému SQL Server na platformě Azure | Microsoft Docs
description: Ukázková Data v systému SQL Server v Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgeonlun
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 74dcef9e927fc537cba56b03fcbfb9528c952ad0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837867"
---
# <a name="heading"></a>Ukázková data v systému SQL Server v Azure
Tento článek ukazuje, jak ukázková data uložená v systému SQL Server na platformě Azure pomocí SQL nebo programovací jazyk Python. Také ukazuje, jak přesunout jen Vzorkovaná data do Azure Machine Learning ukládání do souboru, odesílání do objektu blob Azure a pak ho čtení do Azure Machine Learning Studio.

Používá Python vzorkování [pyodbc](https://code.google.com/p/pyodbc/) ODBC – knihovna pro připojení k systému SQL Server na platformě Azure a [Pandas](http://pandas.pydata.org/) knihovnu, která má odběr vzorků.

> [!NOTE]
> Ukázkový kód SQL v tomto dokumentu se předpokládá, že data jsou v systému SQL Server na platformě Azure. Pokud není, podívejte se na [přesun dat do systému SQL Server na platformě Azure](move-sql-server-virtual-machine.md) článku pokyny, jak pro přesun dat do systému SQL Server na platformě Azure.
> 
> 

Následující **nabídky** odkazy na články, které popisují, jak ukázková data z různých prostředích úložiště. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné nižší ukázková data, která mají snížit velikost menší, ale reprezentativní a lepší správu bitlockeru. To usnadňuje pochopení dat, zkoumání a funkce inženýrství. V jeho role [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) je umožnit rychlé vytváření prototypů zpracování dat funkcí a modelů strojového učení.

Tato úloha vzorkování je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>Pomocí SQL
Tato část popisuje několik způsobů použití SQL k provedení prostý náhodný výběr proti data v databázi. Vyberte metodu na základě vaší velikost dat a jeho distribuci.

Následující dvě položky ukazují, jak používat `newid` v systému SQL Server k provedení vzorkuje. Metoda zvolíte závisí na tom, jak náhodné chcete vzorku, který má být (pk_id v následujícím ukázkovém kódu se předpokládá, že se automaticky vygeneruje primární klíč).

1. Méně přísná náhodného vzorku
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Více náhodného vzorku 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Klauzule Tablesample lze použít pro vzorkování dat také. To může být lepším řešením, pokud (za předpokladu, že data na různých stránkách není korelační) má velká velikost dat a pro dotaz na dokončení v přiměřené době.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Vám umožní zkoumat a generovat funkce z této jen Vzorkovaná data ukládáním do nové tabulky
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Ukázkové dotazy výše můžete použít přímo v Azure Machine Learning [importovat Data] [ import-data] modulu nižší – ukázka data za chodu a tím, že ji do experimentu Azure Machine Learning. Snímek obrazovky pomocí modulu reader číst jen Vzorkovaná data je znázorněno zde:

![Čtečka sql][1]

## <a name="python"></a>Pomocí programovacího jazyka Python
V této části ukazuje, jak pomocí [pyodbc knihovny](https://code.google.com/p/pyodbc/) k navázání připojení rozhraní ODBC do databáze systému SQL server v Pythonu. Připojovací řetězec databáze je následující: (nahraďte servername, dbname, uživatelské jméno a heslo s konfigurací):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas](http://pandas.pydata.org/) knihovna v Pythonu obsahuje bohatou sadu datové struktury a nástrojů pro analýzu dat pro manipulaci s daty pro programování Python. Následující kód načte 0,1 % ukázkových dat z tabulky v databázi Azure SQL do Pandas dat:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Teď můžete pracovat s jen Vzorkovaná data v rámci Pandas data. 

### <a name="python-aml"></a>Připojení k Azure Machine Learning
Následující vzorový kód slouží k uložení dat vzorkovat dolů do souboru a nahrajte ho do Azure blob. Data v objektu blob může číst přímo do experimentu Azure Machine Learning pomocí [importovat Data] [ import-data] modulu. Kroky jsou následující: 

1. Zápis dat rámečku pandas do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Nahrát místní soubor do objektu blob Azure
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Čtení dat z Azure blob pomocí Azure Machine Learning [importovat Data] [ import-data] modulu, jak je znázorněno v následujícím okamžitými výsledky obrazovky:

![Čtečka objektů blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Proces Team dat. vědecké účely v příkladu akce
Návod Příklad procesu vědecké účely Team dat pomocí veřejné datové sady, najdete v článku [proces vědecké účely Team dat v akci: pomocí SQL serveru](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
