---
title: Ukázková data v Azure blob storage – vědecké zpracování týmových dat
description: Vzorkování dat uložených ve službě Azure blob storage tak, že stahování prostřednictvím kódu programu a pak ho pomocí procedury napsané v Pythonu vzorkování.
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
ms.openlocfilehash: 0f9795e6a9a451ab1492e62fd54faea5894d99ae
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136490"
---
# <a name="heading"></a>Ukázková data v Azure blob storage

Tento článek se týká vzorkování data uložená ve službě Azure blob storage pomocí stahování prostřednictvím kódu programu a pak ho pomocí procedury napsané v Pythonu vzorkování.

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. To usnadňuje pochopení dat, prozkoumávání a vytváření funkcí. Jejich rolí v procesu sady Cortana Analytics je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Stažení a seznamu ukázkových dat
1. Stáhněte data z úložiště objektů blob v Azure pomocí služby blob service z následujícího vzorového kódu Pythonu: 
   
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

2. Čtení dat do dat – rámec Pandas ze souboru stáhnout výše.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Data s využitím dolů sample `numpy`společnosti `random.choice` následujícím způsobem:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Teď můžete pracovat s výše datový rámec s ukázkou procento 1 pro další zkoumání a funkce generování.

## <a name="heading"></a>Nahrání dat a načíst do Azure Machine Learning
Vám pomůže následující ukázka kódu dolů – ukázková data a použít ho přímo ve službě Azure Machine Learning:

1. Zápis datového rámce do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Nahrání místního souboru do objektu blob Azure pomocí následujícího ukázkového kódu:
   
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

3. Číst data z Azure blob pomocí Azure Machine Learning [Import dat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) jak je znázorněno na následujícím obrázku:

![Čtečka objektů blob](./media/sample-data-blob/reader_blob.png)

