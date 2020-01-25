---
title: Ukázková Data na SQL serveru v Azure – vědecké zpracování týmových dat
description: Ukázková data uložená na SQL serveru v Azure s využitím SQL nebo programovací jazyk Python a potom ho přesunout do Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717641"
---
# <a name="heading"></a>Ukázková data v SQL serveru v Azure

Tento článek ukazuje, jak ukázková data uložená na SQL serveru v Azure pomocí jazyka SQL nebo programovací jazyk Python. Také ukazuje, jak přesunout jen Vzorkovaná data do Azure Machine Learning uložením do souboru, pak ho nahrát do objektu blob Azure a jeho načtením do Azure Machine Learning Studio.

Použití Pythonu vzorkování [pyodbc](https://code.google.com/p/pyodbc/) ODBC knihovny pro připojení k SQL serveru v Azure a [Pandas](https://pandas.pydata.org/) knihovny provedete vzorkování.

> [!NOTE]
> Ukázkový kód SQL v tomto dokumentu se předpokládá, že data jsou v systému SQL Server v Azure. Pokud není, podívejte se na [přesun dat do SQL serveru v Azure](move-sql-server-virtual-machine.md) najdete pokyny, jak přesunout data do SQL serveru v Azure.
> 
> 

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. Vzorkování usnadňuje porozumění datům, průzkumům a inženýrům funkcí. V jeho role [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Pomocí SQL
Tato část popisuje několik metod, pomocí jazyka SQL k provedení jednoduchého výběrová s daty v databázi. Vyberte metodu na základě velikost vašich dat a jeho distribuci.

Následující dvě položky ukazují, jak používat `newid` v systému SQL Server provádět vzorkování. Metoda, kterou zvolíte, závisí na tom, jak má být vzorek (pk_id v následujícím ukázkovém kódu považován za automaticky generovaný primární klíč).

1. Méně striktní náhodného vzorku
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Více náhodného vzorku 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Klauzule Tablesample lze použít pro vzorkování data při selhání. Tato možnost může být lepší, pokud je velikost dat velká (za předpokladu, že data na různých stránkách nejsou korelační) a že dotaz bude dokončen v přiměřené době.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Můžete prozkoumat a vytvořte funkce z těchto vybraných dat tím, že ukládání do nové tabulky
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Pomocí ukázkových dotazů uvedených výše v modulu Azure Machine Learning [Import dat][import-data] můžete data průběžně vzorkovat a přenést je do Azure Machine Learning experimentu. Snímek obrazovky s použitím modulu Reader ke čtení ukázkových dat najdete tady:

![Čtečka sql][1]

## <a name="python"></a>Pomocí programovacího jazyka Python
Tato část ukazuje, jak pomocí [pyodbc knihovny](https://code.google.com/p/pyodbc/) stanovit ODBC připojení k databázi SQL serveru v Pythonu. Připojovací řetězec databáze je následující: (nahraďte servername, dbname, username a Password pomocí vaší konfigurace):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas](https://pandas.pydata.org/) knihovny v jazyce Python nabízí bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte 0,1% vzorku dat z tabulky v Azure SQL Database do dat PANDAS:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Teď můžete pracovat s vzorkovaných dat. v Pandas datového rámce. 

### <a name="python-aml"></a>Připojení k Azure Machine Learning
Následující vzorový kód můžete použít k ukládání dat předvýpočtem zredukovaných do souboru a nahrajte ho do objektu blob Azure. Data v objektu BLOB je možné přímo přečíst do Azure Machine Learning experimentu pomocí modulu [Import dat][import-data] . Kroky jsou následující: 

1. Zápis pandas datového rámce do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Nahrání místního souboru do objektu blob Azure
   
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
3. Načtěte data z Azure BLOB pomocí Azure Machine Learning [Importujte modul dat][import-data] , jak je znázorněno na následující obrazovce:

![Čtečka objektů blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Vědecké zpracování týmových dat v příkladu akce
Chcete-li projít příkladem vědeckého zpracování týmových dat a pomocí veřejné datové sady, přečtěte si článek o [vědeckém zpracování týmových dat v akci: použití SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
