---
title: Vytvoření funkce pro data úložiště objektů blob v Azure pomocí Panda | Microsoft Docs
description: Postup vytvoření funkce pro data, která je uložená v kontejneru objektů blob v Azure s balíčkem Panda Python.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 99e54400b070a03744c4d4504fa4c89ad6dc097d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837442"
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Vytvoření funkcí pro data služby Azure Blob Storage pomocí knihovny Pandas
Tento dokument ukazuje, jak vytvořit funkcí pro data, která je uložená v pomocí kontejneru objektů blob v Azure [Pandas](http://pandas.pydata.org/) balíček Python. Po osnovy jak načíst data do rámečku data Panda, ukazuje, jak vygenerovat kategorií funkce pomocí skriptů Python s hodnotami indikátoru a přihrádkování funkce.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **nabídky** odkazy na témata, které popisují, jak vytvořit funkce pro data v různých prostředích. Tato úloha je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste vytvořili účet úložiště objektů blob v Azure a jsou uloženy vaše data existuje. Pokud budete potřebovat pokyny k nastavení účtu, najdete v části [vytvoření účtu úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Načíst data do rámečku Pandas dat
Chcete-li prozkoumat a pracovat s datovou sadu, stáhnou se ze zdroje blob do místního souboru. Pak můžete načtěte do rámečku Pandas data. Tady jsou kroky provést tento postup:

1. Stahování dat z Azure blob s následujícím kódem ukázkové Python pomocí služby objektů blob. Nahraďte konkrétní hodnoty proměnné v následujícím kódem:
   
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
2. Načtení dat do data rámeček Pandas ze staženého souboru.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nyní jste připraveni k data prozkoumat a generování funkce pro tuto datovou sadu.

## <a name="blob-featuregen"></a>Funkce generování
V následujících dvou částech ukazují, jak vygenerovat kategorií funkce s hodnotami indikátoru a přihrádkování funkcí pomocí skriptů Python.

### <a name="blob-countfeature"></a>Generování funkce na základě hodnota indikátoru
Kategorií funkce lze vytvořit následujícím způsobem:

1. Zkontrolujte distribuci sloupci kategorií:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generování hodnot ukazatele pro jednotlivé hodnoty ve sloupcích
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Připojit ukazatel sloupec s původní data rámečku
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Odeberte původní proměnnou:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Přihrádkování funkce generování
Pro generování binned funkce, postupujte takto:

1. Přidat posloupnost sloupce, které chcete bin je číselný sloupec
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Převést přihrádkování pořadí boolean proměnných
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Nakonec připojení fiktivní proměnné zpět na původní data rámečku
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Zápis dat zpět do objektu blob Azure využívat v Azure Machine Learning
Tato data v Azure Machine Learning, že jste využili, vzorků nebo featurized, nahrát data do Azure blob. Další funkce lze vytvořit v Azure Machine Learning Studio také. Následující kroky ukazují, jak chcete nahrát data:

1. Zápis dat rámečku k místnímu souboru.
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Nahrajte data do objektů blob v Azure následujícím způsobem:
   
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
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Nyní lze číst data z objektu blob pomocí Azure Machine Learning [importovat Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modulu, jak je znázorněno na následujícím snímku obrazovky:

![Čtečka objektů blob](./media/data-blob/reader_blob.png)

