---
title: Ukázková Data na SQL serveru v Azure – vědecké zpracování týmových dat
description: Ukázková data uložená na SQL serveru v Azure s využitím SQL nebo programovací jazyk Python a potom ho přesunout do Azure Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 076e2aec249407406d42357df0dc8e74e9362992
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446659"
---
# <a name="heading"></a>Ukázková data v SQL serveru v Azure

Tento článek ukazuje, jak ukázková data uložená na SQL serveru v Azure pomocí jazyka SQL nebo programovací jazyk Python. Také ukazuje, jak přesunout jen Vzorkovaná data do Azure Machine Learning uložením do souboru, pak ho nahrát do objektu blob Azure a jeho načtením do Azure Machine Learning Studio.

Použití Pythonu vzorkování [pyodbc](https://code.google.com/p/pyodbc/) ODBC knihovny pro připojení k SQL serveru v Azure a [Pandas](http://pandas.pydata.org/) knihovny provedete vzorkování.

> [!NOTE]
> Ukázkový kód SQL v tomto dokumentu se předpokládá, že data jsou v systému SQL Server v Azure. Pokud není, podívejte se na [přesun dat do SQL serveru v Azure](move-sql-server-virtual-machine.md) najdete pokyny, jak přesunout data do SQL serveru v Azure.
> 
> 

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. To usnadňuje pochopení dat, prozkoumávání a vytváření funkcí. V jeho role [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Pomocí SQL
Tato část popisuje několik metod, pomocí jazyka SQL k provedení jednoduchého výběrová s daty v databázi. Vyberte metodu na základě velikost vašich dat a jeho distribuci.

Následující dvě položky ukazují, jak používat `newid` v systému SQL Server provádět vzorkování. Zvolená metoda závisí na tom, jak náhodných chcete vzorku, který má být (pk_id v následujícím ukázkovém kódu se předpokládá, že automaticky vygeneruje primární klíč).

1. Méně striktní náhodného vzorku
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Více náhodného vzorku 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Klauzule Tablesample lze použít pro vzorkování data při selhání. Pokud velikost vašich dat je velká (za předpokladu, že se data na různých stránkách korelována) a pro dotaz můžete dokončit v rozumné době to může být lepším řešením.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Můžete prozkoumat a vytvořte funkce z těchto vybraných dat tím, že ukládání do nové tabulky
> 
> 

### <a name="sql-aml"></a>Připojení k Azure Machine Learning
Ukázkové dotazy výše můžete použít přímo ve službě Azure Machine Learning [Import dat] [ import-data] modulu dolů – ukázková data v reálném čase a připojili k experimentu Azure Machine Learning. Snímek obrazovky pomocí modulu reader na čtení jen Vzorkovaná data je znázorněna zde:

![Čtečka sql][1]

## <a name="python"></a>Pomocí programovacího jazyka Python
Tato část ukazuje, jak pomocí [pyodbc knihovny](https://code.google.com/p/pyodbc/) stanovit ODBC připojení k databázi SQL serveru v Pythonu. Připojovací řetězec databáze je následujícím způsobem: (nahraďte název_serveru, dbname, uživatelské jméno a heslo vaší konfigurace):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas](http://pandas.pydata.org/) knihovny v jazyce Python nabízí bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód načte do datové Pandas 0,1 % ukázková data z tabulky ve službě Azure SQL database:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Teď můžete pracovat s vzorkovaných dat. v Pandas datového rámce. 

### <a name="python-aml"></a>Připojení k Azure Machine Learning
Následující vzorový kód můžete použít k ukládání dat předvýpočtem zredukovaných do souboru a nahrajte ho do objektu blob Azure. Data v objektu blob je možné přímo načíst do Azure Machine Learning experimentu pomocí [Import dat] [ import-data] modulu. Kroky jsou následující: 

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
3. Čtení dat z objektů blob v Azure pomocí Azure Machine Learning [Import dat] [ import-data] modulu, jak je znázorněno v následujícím úchytů obrazovky:

![Čtečka objektů blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Vědecké zpracování týmových dat v příkladu akce
Průvodce příkladem vědecké zpracování týmových dat pomocí veřejné datové sady, najdete v článku [vědecké zpracování týmových dat v akci: použití serveru SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
