---
title: Ukázková data v Azure blob storage | Microsoft Docs
description: Ukázková data do Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: fecff09b67f019a0d2b95d8ca33d16011b17b9ce
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="heading"></a>Ukázková data v Azure blob storage
Tento dokument popisuje vzorkování dat uložených v Azure blob storage stáhnout prostřednictvím kódu programu a potom ji pomocí postupů, které jsou napsané v Pythonu vzorkování.

Následující **nabídky** odkazy na témata, které popisují, jak ukázková data z různých prostředích úložiště. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné nižší ukázková data, která mají snížit velikost menší, ale reprezentativní a lepší správu bitlockeru. To usnadňuje pochopení dat, zkoumání a funkce inženýrství. Jeho role v procesu Cortana Analytics je umožnit rychlé vytváření prototypů zpracování dat funkcí a modelů strojového učení.

Tato úloha vzorkování je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Stažení a nižší ukázková data
1. Stáhněte data z Azure blob storage pomocí služby objektů blob z následující vzorový kód Python: 
   
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

2. Čtení dat do data rámečku Pandas z soubor stažený výše.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Nižší sample dat pomocí `numpy`na `random.choice` následujícím způsobem:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nyní můžete pracovat s výše rámečku dat s ukázkou procent 1 pro další zkoumání a funkce generování.

## <a name="heading"></a>Nahrání dat a načtení do Azure Machine Learning
Následující vzorový kód vám pomůže nižší sample data a použít ho přímo v Azure Machine Learning:

1. Zápis dat rámečku do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Nahrajte místního souboru do služby Azure blob pomocí následující vzorový kód:
   
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

3. Čtení dat z Azure blob pomocí Azure Machine Learning [importovat Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) jak je znázorněno na obrázku níže:

![Čtečka objektů blob](./media/sample-data-blob/reader_blob.png)

