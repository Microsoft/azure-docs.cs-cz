---
title: Prozkoumejte data v úložišti objektů blob Azure pomocí pand – proces vědecké ho služky
description: Jak prozkoumat data uložená v kontejneru objektů blob Azure pomocí balíčku pandas Python.
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
ms.openlocfilehash: 0b602660142f70fb1442977b22fce2bc1bb275cd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481932"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Prozkoumejte data v úložišti objektů blob Azure pomocí pand

Tento článek popisuje, jak prozkoumat data, která je uložená v kontejneru objektů blob Azure pomocí [balíčku pandas](https://pandas.pydata.org/) Python.

Tento úkol je krokem v [procesu vědecké vědy o týmových datech](overview.md).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořil účet úložiště Azure. Pokud potřebujete pokyny, přečtěte [si tématy Vytvoření účtu Azure Storage.](../../storage/common/storage-account-create.md)
* Uložená data v účtu úložiště objektů blob Azure. Pokud potřebujete pokyny, přečtěte si informace [o přesunu dat do a z Azure Storage.](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Načtení dat do datového rámce pand
Chcete-li prozkoumat a manipulovat s datovou sadou, musí být nejprve stažena ze zdroje objektů blob do místního souboru, který pak lze načíst do datového rámce pandas. Zde jsou kroky, které je třeba pro tento postup postupovat:

1. Stáhněte si data z objektu blob Azure s následující ukázkou kódu Pythonu pomocí služby Blob. Nahraďte proměnnou v následujícím kódu konkrétními hodnotami:

    ```python
    from azure.storage.blob import BlockBlobService
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
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Přečtěte si data do pandas DataFrame ze staženého souboru.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Nyní jste připraveni prozkoumat data a generovat funkce v této datové sadě.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Příklady zkoumání dat pomocí pand
Zde je několik příkladů způsobů, jak prozkoumat data pomocí pand:

1. Kontrola **počtu řádků a sloupců**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Zkontrolujte** první nebo posledních několik **řádků** v následující datové sadě:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Zkontrolujte **datový typ,** který byl importován každý sloupec, jako použití následujícího ukázkového kódu.

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Podívejte se na **základní statistiky** sloupců v datové sadě takto

    ```python
    dataframe_blobdata.describe()
    ```

1. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Počet chybějících hodnot** ve skutečném počtu položek v každém sloupci pomocí následujícího ukázkového kódu

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Pokud v datech **chybí hodnoty** pro určitý sloupec, můžete je vynechat následujícím způsobem:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Dalším způsobem, jak nahradit chybějící hodnoty, je funkce režimu:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Vytvoření obrázku **histogramu** pomocí proměnného počtu přihrádek k vykreslení rozdělení proměnné

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Podívejte se na **korelace** mezi proměnnými pomocí scatterplot nebo pomocí vestavěné korelační funkce

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
