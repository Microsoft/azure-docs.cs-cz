---
title: Prozkoumejte data v úložišti objektů blob v Azure s Pandas | Microsoft Docs
description: Jak chcete dynamicky prozkoumávat data, která je uložená v kontejneru objektů blob v Azure pomocí Pandas.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: c727df985f3285f5def9bfdc249ee27b4d748a01
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837044"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Zkoumání dat ve službě Azure Blob Storage pomocí knihovny Pandas
Tento dokument popisuje, jak chcete dynamicky prozkoumávat data, která je uložená v Azure blob kontejneru pomocí [Pandas](http://pandas.pydata.org/) balíček Python.

Následující **nabídky** odkazy na témata, které popisují, jak používat nástroje a prozkoumejte data z různých prostředích úložiště. Tato úloha je krok v [proces vědecké účely dat]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořit účet úložiště Azure. Pokud budete potřebovat pokyny, najdete v části [vytvoření účtu úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Vaše data uložena v účtu úložiště objektů blob v Azure. Pokud budete potřebovat pokyny, najdete v části [přesun dat do a z Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Načíst data do Pandas DataFrame
Pokud chcete prozkoumat a manipulaci s datovou sadu, se musí nejprve stáhnout ze zdroje blob do místního souboru, který lze načíst v Pandas DataFrame. Tady jsou kroky provést tento postup:

1. Stahování dat z Azure blob s následující ukázka kódu Pythonu pomocí služby objektů blob. Nahraďte konkrétní hodnoty proměnné v následujícím kódem: 
   
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

## <a name="blob-dataexploration"></a>Příklady zkoumání dat pomocí Pandas
Tady je několik příkladů jak prozkoumat dat pomocí Pandas:

1. Zkontrolujte **počet řádků a sloupců** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Zkontrolujte** několik první nebo poslední **řádky** v datové sadě následující:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Zkontrolujte **datový typ** každý sloupec byl importován jako pomocí následující vzorový kód
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Zkontrolujte **základní statistiky** pro sloupce v datech nastavte takto
   
        dataframe_blobdata.describe()
5. Podívejte se na počet položek pro každou hodnotu sloupce následujícím způsobem
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Počet chybějících hodnot** a skutečný počet položek v jednotlivých sloupcích pomocí následující vzorový kód
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Pokud máte **chybějící hodnoty** pro konkrétní sloupec v datech, můžete jejich umístění následujícím způsobem:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Jiný způsob, jak nahradit chybějící hodnoty je pomocí funkce režimu:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< column_name >: .mode()[0]}) dataframe_blobdata ['< column_name >"]        
8. Vytvoření **histogram** vykreslení pomocí proměnné počet přihrádek k vykreslení distribuce proměnné    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Podívejte se na **korelací** mezi proměnné pomocí scatterplot nebo pomocí funkce integrované korelace
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

