---
title: Zpracování dat objektů blob v Azure s pokročilou analýzu | Microsoft Docs
description: Zpracování dat v úložišti objektů Blob Azure.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 7abb547692bf7cc8e78caabe7c4c0f47edc1a0d2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="heading"></a>Zpracování dat objektů blob v Azure s pokročilou analýzu
Tento dokument popisuje seznámení data a funkce generování z dat uložených v Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Načíst data do rámečku Pandas dat
Abyste mohli prozkoumat a upravit datovou sadu, se musí stáhnout ze zdroje blob do místního souboru, který lze načíst v rámci Pandas data. Tady jsou kroky provést tento postup:

1. Stahování dat z Azure blob s následujícím kódem ukázkové Python pomocí služby objektů blob. Nahraďte konkrétní hodnoty proměnné v kódu níže: 
   
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

## <a name="blob-dataexploration"></a>Zkoumání dat
Tady je několik příkladů jak prozkoumat dat pomocí Pandas:

1. Kontrola počtu řádků a sloupců 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Zkontrolujte první nebo poslední několik řádků v datové sadě, jak je uvedeno níže:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrola typu dat, které každý sloupec byl importován jako pomocí následující vzorový kód
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Zkontrolujte následující základní statistiky pro sloupce v datové sadě
   
        dataframe_blobdata.describe()
5. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Počet chybějících hodnot a skutečný počet položek v jednotlivých sloupcích pomocí následující vzorový kód
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Pokud máte chybějící hodnoty pro konkrétní sloupec v datech, vyřaďte je následujícím způsobem:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Jiný způsob, jak nahradit chybějící hodnoty je pomocí funkce režimu:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< column_name >: .mode()[0]}) dataframe_blobdata ['< column_name >"]        
8. Vytvoření histogram vykreslení pomocí proměnné počet přihrádek k vykreslení distribuce proměnné    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Podívejte se na korelací mezi proměnné pomocí scatterplot nebo pomocí funkce integrované korelace
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Funkce generování
Můžete se vygeneruje funkcí s použitím Python následujícím způsobem:

### <a name="blob-countfeature"></a>Hodnota ukazatele na základě funkce generování
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
Pro generování binned funkce, budeme postupovat takto:

1. Přidat posloupnost sloupce, které chcete bin je číselný sloupec
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Převést přihrádkování pořadí boolean proměnných
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Nakonec připojení fiktivní proměnné zpět na původní data rámečku
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Zápis dat zpět do objektu blob Azure a využívají v Azure Machine Learning
Poté, co jste prozkoumali data a vytvořili nezbytné funkce, můžete nahrát data (vzorkovat nebo featurized) do Azure blob a využívat v Azure Machine Learning pomocí následujících kroků: Další funkce můžete vytvářet v Azure Machine Learning Studio také. 

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
3. Nyní lze číst data z objektu blob pomocí Azure Machine Learning [importovat Data] [ import-data] modulu, jak je znázorněno na obrazovce níže:

![Čtečka objektů blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

