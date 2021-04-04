---
title: Zpracování dat Azure BLOB pomocí pokročilých analýz – vědecké zpracování týmových dat
description: Prozkoumejte data a generujte funkce z dat uložených ve službě Azure Blob Storage s využitím pokročilých analýz.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93307166"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Zpracování dat Azure Blob pomocí pokročilých analýz
Tento dokument popisuje prozkoumávání dat a generování funkcí z dat uložených v úložišti objektů BLOB v Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Načtení dat do datového rámce PANDAS
Aby bylo možné prozkoumat a manipulovat s datovou sadou, je nutné ji stáhnout ze zdroje objektu blob do místního souboru, který lze poté načíst do datového rámce PANDAS. Postup je následující:

1. Stáhněte si data z objektu blob Azure pomocí následujícího ukázkového kódu Pythonu pomocí Blob service. Nahraďte proměnnou v kódu níže konkrétními hodnotami: 
   
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
2. Přečtěte si data do PANDAS data-Frame ze staženého souboru.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Teď jste připraveni prozkoumat data a vygenerovat funkce v této datové sadě.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Zkoumání dat
Tady je několik příkladů způsobů, jak prozkoumat data pomocí PANDAS:

1. Kontrola počtu řádků a sloupců 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. V datové sadě zkontrolujte první nebo poslední řádky následujícím způsobem:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Ověřte, že se datový typ každého sloupce importoval pomocí následujícího ukázkového kódu.
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Ověřte základní statistiky pro sloupce v sadě dat následujícím způsobem.
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem.
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Počítat chybějící hodnoty oproti skutečnému počtu položek v každém sloupci pomocí následujícího ukázkového kódu
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Pokud v datech chybí hodnoty pro určitý sloupec, můžete je odstranit takto:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   Další způsob, jak nahradit chybějící hodnoty, je funkce Mode:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Vytvoření grafu histogramu pomocí proměnlivého počtu přihrádek k vykreslení distribuce proměnné    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Podívejte se na korelace mezi proměnnými pomocí scatterplot nebo pomocí integrované funkce Correlation.
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generace funkcí
Pomocí Pythonu můžeme vygenerovat funkce následujícím způsobem:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Generování funkcí na základě hodnoty ukazatele
Funkce kategorií se dají vytvořit takto:

1. Zkontrolujte distribuci sloupce kategorií:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Generovat hodnoty indikátoru pro každou hodnotu sloupce
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Spojit sloupec indikátoru s původním datovým rámcem 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Odebrat původní proměnnou jako celek:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generace funkcí binningu
Pro generování funkcí rozdělený budeme pokračovat následujícím způsobem:

1. Přidání posloupnosti sloupců do přihrádky a číselného sloupce
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Převést binningu na sekvenci logických proměnných
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Nakonec připojte fiktivní proměnné zpátky k původnímu datovému snímku.
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Zápis dat zpět do objektu blob Azure a využití v Azure Machine Learning
Po prozkoumání dat a vytvoření potřebných funkcí můžete data (sampleed nebo natrénuje) nahrát do objektu blob Azure a využít je v Azure Machine Learning pomocí následujících kroků: Další funkce se dají vytvořit také v Azure Machine Learning Studio (Classic). 

1. Zapsat datový rámec do místního souboru
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Nahrajte data do objektu blob Azure následujícím způsobem:
   
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
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. Nyní je možné číst data z objektu BLOB pomocí modulu Azure Machine Learning [Import dat][import-data] , jak je znázorněno na následující obrazovce:

![objekt BLOB čtečky][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data