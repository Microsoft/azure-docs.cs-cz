---
title: Zpracování dat objektů blob v Azure pomocí pokročilé analýzy – vědecké zpracování týmových dat
description: Zkoumání dat a generovat funkce z dat uložených v úložišti objektů Blob v Azure pomocí pokročilých analýz.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c7d8414fdc1070204ef02cf6d88b238575d0817a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472418"
---
# <a name="heading"></a>Zpracování dat objektů blob v Azure pomocí pokročilých analýz
Tento dokument popisuje analýzu dat a generování funkcí z dat uložených v úložišti objektů Blob v Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Načtení dat do datového rámce Pandas
K prozkoumání a manipulaci s datovou sadu, je stáhnout z zdroj objektu blob do místního souboru, který lze načíst do datového rámce Pandas. Tady jsou kroky pro tento postup:

1. Stahování dat z Azure blob s následující ukázkový kód Pythonu pomocí služby blob service. Proměnná ve níže uvedeného kódu nahraďte konkrétní hodnoty: 
   
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
2. Načtení dat do dat – rámec Pandas ze staženého souboru.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nyní jste připraveni na zkoumání dat a generovat funkce pro tuto datovou sadu.

## <a name="blob-dataexploration"></a>Zkoumání dat
Tady je pár příkladů, jak zkoumat data pomocí Pandas:

1. Kontrola počtu řádků a sloupců 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Kontrola první nebo poslední několik řádků v datové sadě, jak je uvedeno níže:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Zkontrolujte datový typ importované každý sloupec jako pomocí následujícího ukázkového kódu
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Zkontrolujte následující základní statistiky pro sloupce v datové sadě
   
        dataframe_blobdata.describe()
5. Podívejte se na počet položek pro každou hodnotu sloupce, které následujícím způsobem
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Počet chybějících hodnot a skutečný počet položek v jednotlivých sloupcích pomocí následujícího ukázkového kódu
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Pokud máte v datech chybějící hodnoty pro konkrétní sloupce, můžete je zrušit následujícím způsobem:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Dalším způsobem, jak nahradit chybějících hodnot je ve funkci režimu:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< Název_sloupce >: .mode()[0]}) dataframe_blobdata [. < Název_sloupce >']        
8. Vytvoření histogramu vykreslení pomocí proměnný počet intervalů k vykreslení distribuce proměnné    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Podívejte se na korelace mezi proměnné pomocí diagnostického nebo funkcí vestavěné korelace
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Funkce generování
Vygenerujeme funkce Python následujícím způsobem:

### <a name="blob-countfeature"></a>Hodnota ukazatele na základě funkcí generace
Funkce zařazené do kategorií můžete vytvořit následujícím způsobem:

1. Zkontrolujte distribuci sloupci zařazené do kategorií:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generování indikátor hodnot pro všechny hodnoty ve sloupcích
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Připojte se k sloupci indikátorů původního datového rámce 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Odeberte původní sám:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Binning funkci generování
Pro generování rozdělený na intervaly funkce jsme postupujte následovně:

1. Přidat posloupnost sloupců do adresáře bin číselný sloupec
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Převést binning na řadu proměnné typu boolean
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. A konečně připojte se k fiktivní proměnné zpět do původního datového rámce
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Zápis dat objektů blob v Azure a použití ve službě Azure Machine Learning
Když zkoumáte data a vytvoří nezbytné funkce, můžete nahrát data (Vzorkovaná nebo natrénuje) do služby Azure blob a používat ji ve službě Azure Machine Learning pomocí následujících kroků: Všimněte si, že nástroje Azure Machine Learning Studio a lze vytvořit další funkce. 

1. Zápis datového rámce do místního souboru
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Nahrání dat do objektů blob v Azure následujícím způsobem:
   
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
3. Nyní lze číst data z objektu blob pomocí Azure Machine Learning [Import dat] [ import-data] modulu, jak je znázorněno na následující obrazovce:

![Čtečka objektů blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

