---
title: Zkoumání dat v Azure Blob Storage s využitím procesu PANDAS – tým pro datové vědy
description: Jak prozkoumat data uložená v kontejneru objektů BLOB v Azure pomocí balíčku Pythonu PANDAS
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
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98805678"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Zkoumání dat v Azure Blob Storage pomocí PANDAS

Tento článek popisuje, jak prozkoumat data uložená v kontejneru objektů BLOB v Azure pomocí balíčku Pythonu [PANDAS](https://pandas.pydata.org/) .

Tento úkol je krok v rámci [vědeckého procesu týmového zpracování dat](overview.md).

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte následující:

* Vytvořili jste účet úložiště Azure. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Data uložená v účtu Azure Blob Storage. Pokud potřebujete pokyny, přečtěte si téma [přesun dat do a z Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Načtení dat do PANDAS dataframe
Chcete-li prozkoumat a manipulovat s datovou sadou, je nutné ji nejprve stáhnout ze zdroje objektu blob do místního souboru, který lze poté načíst do PANDAS dataframe. Postup je následující:

1. Stáhněte si data z objektu blob Azure s následující ukázkou kódu Pythonu pomocí Blob service. Nahraďte proměnnou v následujícím kódu konkrétními hodnotami:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Přečtěte si data do PANDAS dataframe ze staženého souboru.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Teď jste připraveni prozkoumat data a vygenerovat funkce v této datové sadě.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Příklady průzkumu dat pomocí PANDAS
Tady je několik příkladů způsobů, jak prozkoumat data pomocí PANDAS:

1. Kontrola **počtu řádků a sloupců**

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Zkontrolujte** první nebo poslední **řádky** v následující datové sadě:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Ověřte, že se **datový typ** každého sloupce importoval pomocí následujícího ukázkového kódu.

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Ověřte **základní statistiky** pro sloupce v sadě dat následujícím způsobem.

    ```python
    dataframe_blobdata.describe()
    ```

1. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem.

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Počítat chybějící hodnoty** oproti skutečnému počtu položek v každém sloupci pomocí následujícího ukázkového kódu

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Pokud v datech **chybí hodnoty** pro určitý sloupec, můžete je odstranit takto:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Další způsob, jak nahradit chybějící hodnoty, je funkce Mode:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Vytvoření grafu **histogramu** pomocí proměnlivého počtu přihrádek k vykreslení distribuce proměnné

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Podívejte se na **korelace** mezi proměnnými pomocí scatterplot nebo pomocí integrované funkce Correlation.

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
