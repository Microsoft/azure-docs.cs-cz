---
title: Jak používat úložiště objektů (objekt Blob) z iOS – Azure | Microsoft Docs
description: Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů).
services: storage
documentationcenter: ios
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: a15ba7409b4c5f75729b1b40cd2f333c44ae0368
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-ios"></a>Používání úložiště Blob z iOS

Tento článek ukazuje, jak provádět běžné scénáře s využitím Microsoft Azure Blob storage. Ukázky jsou napsané v Objective-C a použít [Klientská knihovna pro úložiště Azure pro iOS](https://github.com/Azure/azure-storage-ios). Pokryté scénáře zahrnují odesílání, výpis, stahování a odstraňování objektů BLOB. Další informace o objekty BLOB, najdete v článku [další kroky](#next-steps) části. Můžete také stáhnout [ukázkovou aplikaci](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) rychle zobrazíte pomocí Azure Storage ve aplikace pro iOS.

## <a name="what-is-blob-storage"></a>Co je služba Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importovat knihovny iOS Azure Storage do vaší aplikace
Můžete importovat knihovně iOS Azure Storage do své aplikace buď pomocí [CocoaPod úložiště Azure](https://cocoapods.org/pods/AZSClient) nebo importem **Framework** souboru. CocoaPod je doporučeným způsobem, protože umožňuje integraci jednodušší, ale import ze souboru framework je šetrnější pro existující projekt knihovny.

Použití této knihovny, budete potřebovat následující:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Pokud jste to ještě neudělali, [nainstalovat CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) ve vašem počítači otevřete okno terminálu a spuštěním následujícího příkazu
    
    ```shell   
    sudo gem install cocoapods
    ```

2. V dalším kroku v adresáři projektu (adresář obsahující souboru .xcodeproj) vytvořte nový soubor s názvem _Podfile_(bez přípony souboru). Přidejte následující _Podfile_ a uložte.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. V okně terminálu přejděte do adresáře projektu a spusťte následující příkaz

    ```shell    
    pod install
    ```

4. Pokud vaše .xcodeproj otevřít v Xcode, zavřete ji. V adresáři projektu otevřete soubor nově vytvořený projekt, který bude mít příponu .xcworkspace. Toto je soubor, kterou budete pracujete z nyní na.

## <a name="framework"></a>Framework
Druhý způsob na používání knihovny, je vytvořit rozhraní ručně:

1. Nejprve stáhnout nebo klonovat [úložišti azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Přejděte do *azure-storage-ios* -> *Lib* -> *Klientská knihovna pro úložiště Azure*a otevřete `AZSClient.xcodeproj` v Xcode.
3. V levém horním pro Xcode změňte schéma active z "Klientská knihovna pro úložiště Azure" na "Framework".
4. Sestavení projektu (⌘ + B). Tím se vytvoří `AZSClient.framework` soubor na pracovní ploše.

Pak můžete importovat soubor framework do své aplikace následujícím způsobem:

1. Vytvoření nového projektu nebo otevře existující projekt v Xcode.
2. Přetáhnout myší `AZSClient.framework` do Xcode navigátoru projektů.
3. Vyberte *kopírovat položky v případě potřeby*a klikněte na *Dokončit*.
4. Klikněte na projekt v levém navigačním panelu a klikněte na *Obecné* v horní části editoru projektu.
5. V části *propojené architektury a knihovny* části, klikněte na tlačítko Přidat (+).
6. Vyhledejte v seznamu knihoven už poskytuje `libxml2.2.tbd` a přidejte ji do projektu.

## <a name="import-the-library"></a>Importovat knihovny 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Pokud používáte Swift, je potřeba vytvořit hlavičku přemostění a importovat < AZSClient/AZSClient.h > existuje:

1. Vytvořte soubor hlavičky `Bridging-Header.h`a přidejte výše uvedený příkaz importu.
2. Přejděte na *nastavení sestavení* kartě a vyhledejte *hlavičky přemostění jazyka Objective-C*.
3. Dvakrát klikněte na pole *hlavičky přemostění jazyka Objective-C* a přidejte cestu k souboru hlavičky: `ProjectName/Bridging-Header.h`
4. Sestavení projektu (⌘ + B) Chcete-li ověřit, že byl hlavičku přemostění zachyceny pomocí Xcode.
5. Začít používat knihovny přímo v žádném Swift souboru, není nutné pro příkazy pro import.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchronní operace
> [!NOTE]
> Všechny metody, které provádějí požadavek služby jsou asynchronních operací. Ukázky kódu obsahují splnit tyto metody dokončení obslužná rutina. Kód dokončení obslužná rutina se spustí **po** požadavek je dokončen. Kód po dokončení obslužná rutina se spustí **při** se provádí požadavek.
> 
> 

## <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob v Azure Storage se musí nacházet v kontejneru. Následující příklad ukazuje, jak vytvořit kontejner, nazývá *newcontainer*, ve vašem účtu úložiště, pokud ještě neexistuje. Když vyberete název vašeho kontejneru, mějte na paměti pojmenování pravidel uvedených výše.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Můžete potvrdit, že to funguje na základě [Microsoft Azure Storage Explorer](http://storageexplorer.com) a ověřování, který *newcontainer* je v seznamu kontejnery pro váš účet úložiště.

## <a name="set-container-permissions"></a>Nastavte oprávnění kontejneru
Kontejneru oprávnění jsou nakonfigurovaná pro **privátní** přístup ve výchozím nastavení. Kontejnery však obsahují několik různých volbách pro kontejner přístupu:

* **Privátní**: kontejnerů a objektů blob dat může číst pouze vlastníka účtu.
* **Objekt BLOB**: data objektu Blob v tomto kontejneru mohou číst přes anonymní žádost, ale kontejneru data nejsou k dispozici. Klienty nelze vytvořit výčet objektů BLOB v kontejneru přes anonymní žádost.
* **Kontejner**: přes anonymní dotazy můžete číst data kontejnerů a objektů blob. Klienti, můžete vytvořit výčet objektů BLOB v kontejneru přes anonymní žádost, ale nemůže vytvořit výčet kontejnery v rámci účtu úložiště.

Následující příklad ukazuje, jak vytvořit kontejner s **kontejneru** přístupová oprávnění, které vám umožní přístup k veřejné, jen pro čtení pro všechny uživatele v síti Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Jak je uvedeno v [koncepty služby objektů Blob](#blob-service-concepts) část, úložiště objektů Blob nabízí tři různé typy objektů blob: objekty BLOB bloků, doplňovací objekty BLOB a objekty BLOB stránky. Knihovna iOS Azure Storage podporuje všechny tři typy objektů BLOB. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Následující příklad ukazuje, jak nahrát objekt blob bloku z NSString. Pokud objekt blob se stejným názvem již existuje v tomto kontejneru, budou přepsány obsah tohoto objektu blob.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Můžete potvrdit, že to funguje na základě [Microsoft Azure Storage Explorer](http://storageexplorer.com) a ověřování, který kontejneru, *containerpublic*, obsahuje objekt blob, *sampleblob*. V této ukázce jsme použili kontejner veřejné, tak můžete také ověřit, zda tato aplikace funguje tak, že přejdete na objekty BLOB URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Kromě nahrát objekt blob bloku z NSString, existují podobné metody pro NSData, NSInputStream nebo místního souboru.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
Následující příklad ukazuje, jak získat seznam všech objektů BLOB v kontejneru. Při provádění této operace, mějte na paměti následující parametry:     

* **continuationToken** -tokenu představuje pokračování kde by se měl spustit operace výpisu. Pokud je k dispozici žádné token, zobrazí seznam objektů BLOB od začátku. Libovolný počet objektů BLOB může být uvedený od nuly až do maximální sady. I když tato metoda vrátí hodnotu nula výsledky, pokud `results.continuationToken` není nulovou hodnotu, může být další objekty BLOB ve službě nejsou uvedené.
* **Předpona** -můžete určit předpona, kterou chcete použít pro seznam objektů blob. Pouze objekty BLOB, které začínají s touto předponou budou zobrazeny.
* **useFlatBlobListing** – jak je uvedeno v [pojmenování a odkazování na kontejnery a objekty BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) části, i když služba objektů Blob je schéma ploché úložiště, můžete vytvořit virtuální hierarchie názvy objektů BLOB s cestou informace. Výpis bez dvojrozměrném však není aktuálně podporováno. Tato funkce bude brzy k dispozici. Prozatím se tato hodnota by měla být **Ano**.
* **blobListingDetails** -zadáte vhodných při výpisu objektů BLOB
  * _AZSBlobListingDetailsNone_: seznam pouze potvrdit objektů BLOB a nevrátí metadata objektu blob.
  * _AZSBlobListingDetailsSnapshots_: seznam potvrdit objektů BLOB a objektů blob snímky.
  * _AZSBlobListingDetailsMetadata_: načíst metadata objektu blob pro každý objekt blob vrátila ve výpisu.
  * _AZSBlobListingDetailsUncommittedBlobs_: seznam objektů BLOB nepotvrzené a potvrzené.
  * _AZSBlobListingDetailsCopy_: zahrnují kopírování vlastnosti ve výpisu.
  * _AZSBlobListingDetailsAll_: seznam dostupných potvrdit objekty BLOB, nepotvrzené objekty BLOB a snímky a vrátí všechny metadata a zkopírujte stav pro tyto objekty BLOB.
* **shluku** – maximální počet výsledků, které se chcete vrátit tuto operaci. Není nastavit omezení pomocí -1.
* **completionHandler** – blok kódu provést s výsledky operace výpisu.

V tomto příkladu se používá ke Pomocná metoda rekurzivní volání seznamu objektů BLOB metoda pokaždé, když se vrátí token pokračování.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Stažení objektu blob
Následující příklad ukazuje, jak stáhnout objektu blob na objekt NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Odstranění objektu blob
Následující příklad ukazuje, jak chcete odstranit objekt blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Odstranit kontejner objektů blob
Následující příklad ukazuje, jak odstranit kontejner.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili používání úložiště Blob z iOS, postupujte podle následujících odkazech na další informace o knihovně iOS a služby úložiště.

* [Azure Klientská knihovna pro úložiště pro iOS](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS referenční dokumentaci k nástroji](http://azure.github.io/azure-storage-ios/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage)

Pokud máte dotazy týkající se této knihovny, klidně k vystavování příspěvků na našem [fóru MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) nebo [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Pokud máte návrhy na funkce pro Azure Storage, prosím odeslání na [zpětnou vazbu úložiště Azure](https://feedback.azure.com/forums/217298-storage/).

