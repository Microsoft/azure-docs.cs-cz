---
title: Zpracování dat objektů blob Azure pomocí pokročilých analýz – proces vědecké analýzy týmových dat
description: Prozkoumejte data a generujte funkce z dat uložených v úložišti objektů Blob Azure pomocí pokročilých analýz.
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
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721094"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Zpracování dat Azure Blob pomocí pokročilých analýz
Tento dokument popisuje zkoumání dat a generování funkcí z dat uložených v úložišti objektů blob Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Načtení dat do datového rámce Pandas
Aby bylo možné prozkoumat a manipulovat s datovou sadou, musí být stažena ze zdroje objektů blob do místního souboru, který pak lze načíst v datovém rámci Pandas. Zde jsou kroky, které je třeba pro tento postup postupovat:

1. Stáhněte si data z objektu blob Azure s následujícím ukázkovým kódem Pythonu pomocí služby Blob. Nahraďte proměnnou v níže uvedeném kódu konkrétními hodnotami: 
   
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
2. Přečtěte si data do datového rámce Pandas ze staženého souboru.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nyní jste připraveni prozkoumat data a generovat funkce v této datové sadě.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Průzkum dat
Zde je několik příkladů způsobů, jak prozkoumat data pomocí pand:

1. Kontrola počtu řádků a sloupců 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Zkontrolujte první nebo posledních několik řádků v datové sadě, jak je uvedeno níže:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Zkontrolujte datový typ, který byl importován každý sloupec, jako použití následujícího ukázkového kódu.
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Podívejte se na základní statistiky sloupců v datové sadě takto
   
        dataframe_blobdata.describe()
5. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Počet chybějících hodnot ve skutečném počtu položek v každém sloupci pomocí následujícího ukázkového kódu
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Pokud v datech chybí hodnoty pro určitý sloupec, můžete je vynechat následujícím způsobem:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Dalším způsobem, jak nahradit chybějící hodnoty, je funkce režimu:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Vytvoření obrázku histogramu pomocí proměnného počtu přihrádek k vykreslení rozdělení proměnné    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Podívejte se na korelace mezi proměnnými pomocí scatterplot nebo pomocí vestavěné korelační funkce
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generování funkcí
Můžeme generovat funkce pomocí Pythonu takto:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Generování prvků založených na hodnotách indikátoru
Kategorické prvky lze vytvořit následujícím způsobem:

1. Zkontrolujte rozložení kategorického sloupce:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generovat hodnoty indikátorů pro každou hodnotu sloupce
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Spojení sloupce indikátoru s původním datovým rámcem 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Odeberte samotnou původní proměnnou:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generování binningu
Pro generování funkcí binned postupujeme takto:

1. Přidání posloupnosti sloupců do přihrádky číselného sloupce
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Převést binning na posloupnost logických proměnných
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Nakonec připojte fiktivní proměnné zpět k původnímu datovému rámci
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Zápis dat zpět do objektu blob Azure a náročné v Azure Machine Learning
Po prozkoumání dat a vytvoření nezbytných funkcí můžete data (ukázková nebo featurized) nahrát do objektu blob Azure a využívat je v Azure Machine Learning pomocí následujících kroků: Další funkce lze vytvořit v Azure Machine Learning Studio (klasické). 

1. Zápis datového rámce do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Nahrajte data do objektu blob Azure takto:
   
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
3. Teď se data můžou číst z objektu blob pomocí modulu Azure Machine Learning [Import Data,][import-data] jak je znázorněno na obrazovce níže:

![objekt blob čtečky][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

