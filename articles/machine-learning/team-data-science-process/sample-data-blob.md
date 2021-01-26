---
title: Ukázková data v Azure Blob Storage – vědecký proces týmového zpracování dat
description: Vzorkování dat uložených v Azure Blob Storage jejich stažením prostřednictvím kódu programu a následným vzorkováním pomocí postupů napsaných v Pythonu.
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
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788837"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Ukázková data v Azure Blob Storage

Tento článek popisuje vzorkování dat uložených v Azure Blob Storage jejich stažením prostřednictvím kódu programu a následným vzorkováním pomocí postupů napsaných v Pythonu.

**Proč vzorkovat data?**
Pokud je datová sada, kterou plánujete analyzovat, rozsáhlá, je obvykle vhodné ji vyvzorkovat, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi. Vzorkování usnadňuje porozumění datům, průzkumům a inženýrům funkcí. Jeho rolí v procesu Cortana Analytics je povolit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

## <a name="download-and-down-sample-data"></a>Stažení a snížení ukázkových dat
1. Stáhněte si data z Azure Blob Storage pomocí Blob service z následujícího ukázkového kódu Pythonu: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Načte data do PANDAS data-Frame ze souboru staženého výše.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Vyznamenejte data pomocí `numpy` `random.choice` následujícího postupu:

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Nyní můžete pracovat s výše uvedeným datovým rámcem s využitím jedné procentuální ukázky pro další zkoumání a generaci funkcí.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Nahrajte data a přečtěte je Azure Machine Learning
Následující vzorový kód můžete použít k rozstupnému vzorkování dat a jeho použití přímo v Azure Machine Learning:

1. Zapsat datový rámec do místního souboru

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Místní soubor nahrajte do objektu blob Azure pomocí následujícího ukázkového kódu:

    ```python
    from azure.storage.blob import BlobService
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
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Přečtěte si data z objektu blob Azure pomocí Azure Machine Learning [importujte data](/azure/machine-learning/studio-module-reference/import-data) , jak je znázorněno na následujícím obrázku:

![objekt BLOB čtečky](./media/sample-data-blob/reader_blob.png)
