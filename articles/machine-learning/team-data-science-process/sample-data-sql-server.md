---
title: Ukázková data v SQL Serveru v Azure – proces vědecké analýzy týmových dat
description: Ukázková data uložená v SQL Serveru v Azure pomocí programovacího jazyka SQL nebo Pythonu a pak je přesuňte do Azure Machine Learning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717641"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Ukázková data na SQL Serveru v Azure

Tento článek ukazuje, jak ukázkovat data uložená v SQL Serveru v Azure pomocí programovacího jazyka SQL nebo Pythonu. Také ukazuje, jak přesunout ukázková data do Azure Machine Learning uložením do souboru, jejich nahráním do objektu blob Azure a čtením do Azure Machine Learning Studio.

Vzorkování Pythonu používá knihovnu [pyodbc](https://code.google.com/p/pyodbc/) ODBC pro připojení k SQL Serveru v Azure a knihovně [Pandas](https://pandas.pydata.org/) k provedení vzorkování.

> [!NOTE]
> Ukázkový kód SQL v tomto dokumentu předpokládá, že data jsou v SQL Serveru v Azure. Pokud tomu tak není, naleznete v článku [Přesunout data na SQL Server v Azure](move-sql-server-virtual-machine.md) článek pokyny, jak přesunout data na SQL Server v Azure.
> 
> 

**Proč vzorkovat data?**
Pokud je datová sada, kterou chcete analyzovat, velká, je obvykle vhodné data snížit na menší, ale reprezentativní a lépe zvládnutelnou velikost. Vzorkování usnadňuje pochopení dat, zkoumání a technické zpracování prvků. Jeho úlohou v [procesu teamových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) je umožnit rychlé prototypování funkcí zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="using-sql"></a><a name="SQL"></a>Použití SQL
Tato část popisuje několik metod, které používají SQL k provedení jednoduchého náhodného vzorkování dat v databázi. Zvolte metodu na základě velikosti dat a jejich distribuce.

Následující dvě položky ukazují, jak použít `newid` v SQL Server k provedení vzorkování. Metoda, kterou zvolíte, závisí na tom, jak náhodné má být vzorek (pk_id v následujícím ukázkovém kódu se předpokládá, že je automaticky generovaný primární klíč).

1. Méně přísný náhodný vzorek
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Další náhodný vzorek 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample lze použít také pro odběr vzorků dat. Tato možnost může být lepší přístup, pokud velikost dat je velký (za předpokladu, že data na různých stránkách není korelována) a dotaz dokončit v přiměřené době.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Můžete prozkoumat a generovat funkce z těchto vzorkovaných dat jejich uložením do nové tabulky
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Připojení k Azure Machine Learning
Můžete přímo použít ukázkové dotazy výše v modulu Azure Machine Learning [Import Data][import-data] k down-sample data za běhu a přenést je do experimentu Azure Machine Learning. Zde je zobrazen snímek obrazovky s použitím modulu čtečky ke čtení vzorkovaných dat:

![čtečka SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Používání programovacího jazyka Pythonu
Tato část ukazuje použití [knihovny pyodbc](https://code.google.com/p/pyodbc/) k vytvoření připojení ODBC k databázi serveru SQL v Pythonu. Připojovací řetězec databáze je následující: (nahraďte název serveru, název db, uživatelské jméno a heslo konfigurací):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Knihovna [Pandas](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte ukázku 0,1 % dat z tabulky v Azure SQL Database do dat Pandy:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Nyní můžete pracovat s ukázkovými daty v datovém rámci Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Připojení k Azure Machine Learning
Následující ukázkový kód můžete použít k uložení dat dolů vzorku do souboru a nahrát je do objektu blob Azure. Data v objektu blob lze přímo číst do experimentu Azure Machine Learning pomocí modulu [Import dat.][import-data] Kroky jsou následující: 

1. Zápis datového rámce pand do místního souboru
   
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
3. Čtení dat z objektu blob Azure pomocí modulu Azure Machine Learning [Import Data,][import-data] jak je znázorněno na následujícím uchopení obrazovky:

![objekt blob čtečky][2]

## <a name="the-team-data-science-process-in-action-example"></a>Příklad procesu vědecké vědy o týmových datech v akci
Chcete-li projít příklad procesu vědeckého zpracování týmových dat pomocí veřejné datové sady, přečtěte si informace [o procesu vědecké ho procesu týmových dat v akci: pomocí serveru SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
