---
title: Ukázková data v SQL Server v procesu Azure – tým pro datové vědy
description: Ukázková data uložená v SQL Server v Azure pomocí programovacího jazyka SQL nebo Pythonu a pak je přesuňte do Azure Machine Learning.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321871"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Ukázková data na SQL Serveru v Azure

V tomto článku se dozvíte, jak vzorkovat data uložená v SQL Server v Azure pomocí programovacího jazyka SQL nebo Pythonu. Také ukazuje, jak přesunout ukázková data do Azure Machine Learning tím, že je uložíte do souboru, nahrajete je do objektu blob Azure a pak je přečtete do Azure Machine Learning Studio.

Vzorkování Pythonu používá knihovnu rozhraní ODBC [pyodbc](https://code.google.com/p/pyodbc/) k připojení k SQL Server v Azure a ke knihovně [PANDAS](https://pandas.pydata.org/) pro vzorkování.

> [!NOTE]
> Ukázkový kód SQL v tomto dokumentu předpokládá, že data jsou v SQL Server v Azure. Pokud tomu tak není, přečtěte si článek o [přesunu dat do SQL Server v Azure](move-sql-server-virtual-machine.md) , kde najdete pokyny, jak přesunout data do SQL Server v Azure.
> 
> 

**Proč vzorkovat data?**
Pokud je datová sada, kterou plánujete analyzovat, rozsáhlá, je obvykle vhodné ji vyvzorkovat, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi. Vzorkování usnadňuje porozumění datům, průzkumům a inženýrům funkcí. Jejím úkolem v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml) je povolit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Používání SQL
Tato část popisuje několik metod, pomocí kterých SQL provádí jednoduché náhodné vzorkování s daty v databázi. Vyberte metodu na základě velikosti dat a její distribuce.

Následující dvě položky ukazují, jak použít `newid` v SQL Server k provedení vzorkování. Metoda, kterou zvolíte, závisí na tom, jak má být vzorek (pk_id v následujícím ukázkovém kódu považován za automaticky generovaný primární klíč).

1. Méně striktní náhodný vzorek

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Náhodná ukázka 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Možnost TABLESAMPLE lze použít také pro vzorkování dat. Tato možnost může být lepší, pokud je velikost dat velká (za předpokladu, že data na různých stránkách nejsou korelační) a že dotaz bude dokončen v přiměřené době.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Můžete zkoumat a generovat funkce z těchto ukázkových dat tím, že je uložíte do nové tabulky.
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Připojování k Azure Machine Learning
Pomocí ukázkových dotazů uvedených výše v modulu Azure Machine Learning [Import dat][import-data] můžete data průběžně vzorkovat a přenést je do Azure Machine Learning experimentu. Snímek obrazovky s použitím modulu Reader ke čtení ukázkových dat najdete tady:

![čtecí modul SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Používání programovacího jazyka Python
V této části se dozvíte, jak pomocí [knihovny pyodbc](https://code.google.com/p/pyodbc/) vytvořit rozhraní ODBC, které se připojí k databázi SQL serveru v Pythonu. Připojovací řetězec databáze je následující: (nahraďte servername, dbname, username a Password pomocí vaší konfigurace):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Knihovna [PANDAS](https://pandas.pydata.org/) v Pythonu poskytuje bohatou sadu datových struktur a nástrojů pro analýzu dat pro manipulaci s daty pro programování v Pythonu. Následující kód přečte 0,1% vzorku dat z tabulky v Azure SQL Database do dat PANDAS:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Nyní můžete pracovat se vzorovými daty v datovém rámečku PANDAS. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Připojování k Azure Machine Learning
Pomocí následujícího ukázkového kódu můžete uložit data z rozevíracího vzorku do souboru a odeslat je do objektu blob Azure. Data v objektu BLOB je možné přímo přečíst do Azure Machine Learning experimentu pomocí modulu [Import dat][import-data] . Kroky jsou následující: 

1. Zápis datového rámce PANDAS do místního souboru

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Nahrání místního souboru do Azure Blob

    ```python
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
    ```

3. Načtěte data z Azure BLOB pomocí Azure Machine Learning [Importujte modul dat][import-data] , jak je znázorněno na následující obrazovce:

![objekt BLOB čtečky][2]

## <a name="the-team-data-science-process-in-action-example"></a>Příklad vědeckého zpracování týmových dat v akci
Chcete-li projít příkladem vědeckého zpracování týmových dat a pomocí veřejné datové sady, přečtěte si článek o [vědeckém zpracování týmových dat v akci: použití SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data