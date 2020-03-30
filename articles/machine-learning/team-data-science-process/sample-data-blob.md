---
title: Ukázková data v úložišti objektů blob Azure – proces vědecké ho zpracování týmových dat
description: Vzorkování dat uložených v úložišti objektů blob Azure tak, že je programově stáhnete a pak je navzorkujete pomocí procedur napsaných v Pythonu.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720271"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Ukázková data ve službě Azure Blob Storage

Tento článek popisuje vzorkování dat uložených v úložišti objektů blob Azure tím, že je programově stáhnete a pak je navzorkujete pomocí postupů napsaných v Pythonu.

**Proč vzorkovat data?**
Pokud je datová sada, kterou chcete analyzovat, velká, je obvykle vhodné data snížit na menší, ale reprezentativní a lépe zvládnutelnou velikost. Vzorkování usnadňuje pochopení dat, zkoumání a technické zpracování prvků. Jeho úlohou v procesu Cortana Analytics je umožnit rychlé vytváření prototypů funkcí zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="download-and-down-sample-data"></a>Stahování a stahování a stahování dat
1. Stáhněte si data z úložiště objektů blob Azure pomocí služby Blob z následujícího ukázkového kódu Pythonu: 
   
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

2. Přečtěte si data do datového rámce Pandas z výše uvedeného souboru.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Údaje se vzorkujte `numpy`dolů `random.choice` pomocí "s takto:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nyní můžete pracovat s výše uvedeným datovým rámcem s ukázkou one percent pro další zkoumání a generování funkcí.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Nahrání dat a jejich přečtení do Azure Machine Learning
Následující ukázkový kód můžete použít k down-sample data a použít přímo v Azure Machine Learning:

1. Zápis datového rámce do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Nahrajte místní soubor do objektu blob Azure pomocí následujícího ukázkového kódu:
   
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

3. Přečtěte si data z objektu blob Azure pomocí [dat importu](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) Azure Machine Learning, jak je znázorněno na obrázku níže:

![objekt blob čtečky](./media/sample-data-blob/reader_blob.png)

