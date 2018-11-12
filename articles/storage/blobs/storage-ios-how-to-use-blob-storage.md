---
title: Jak používat úložiště objektů (Blob) z iOS – Azure | Dokumentace Microsoftu
description: Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů).
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.component: blobs
ms.openlocfilehash: 43e9acb79b363e8f2cb00f6a4676d450c097bf3e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261991"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Používání úložiště Blob z iOS

Tento článek ukazuje, jak provádět běžné scénáře pomocí Microsoft Azure Blob storage. Ukázky jsou napsané v jazyce Objective-C a použití [Klientská knihovna pro úložiště Azure pro iOS](https://github.com/Azure/azure-storage-ios). Popsané scénáře patří odesílání, výpis, stahování a odstraňování objektů BLOB. Další informace o objektech BLOB najdete v článku [další kroky](#next-steps) oddílu. Můžete také stáhnout [ukázkovou aplikaci](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) rychle zobrazíte pomocí služby Azure Storage v aplikace pro iOS.

## <a name="what-is-blob-storage"></a>Co je služba Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importovat knihovny Azure Storage s Iosem do vaší aplikace
Můžete importovat iOS knihovnu pro úložiště Azure do své aplikace s použitím [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) nebo importováním **Framework** souboru. CocoaPod je doporučeným způsobem, jako je integrace jednodušší, ale import ze souboru framework je teď míň obtěžující pro existující projekt knihovny.

Použití této knihovny, budete potřebovat následující:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Pokud jste tak dosud neučinili, [nainstalujte CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) ve vašem počítači otevřete okno terminálu a spuštěním následujícího příkazu
    
    ```shell   
    sudo gem install cocoapods
    ```

2. V dalším kroku v adresáři projektu (adresář obsahující soubor .xcodeproj) vytvořte nový soubor s názvem _Podfile_(bez přípony souboru). Přidejte následující text do _Podfile_ a uložit.

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

4. Pokud vaše .xcodeproj je otevřít v Xcodu, zavřete ho. V adresáři projektu otevřete soubor nově vytvořený projekt, který bude mít příponu .xcworkspace. Toto je soubor, který budete pracovat z pro nyní na.

## <a name="framework"></a>Rozhraní Framework
Druhý způsob použití knihovny je sestavení rozhraní ručně:

1. Nejprve stáhněte nebo naklonujte [úložiště azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Přejděte do *azure-storage-ios* -> *Lib* -> *Klientská knihovna Azure Storage*a otevřete `AZSClient.xcodeproj` v Xcode.
3. V levém horním xcode změňte aktivní schéma z "Klientská knihovna Azure Storage" na "Rozhraní".
4. Sestavte projekt (⌘ + B). Tím se vytvoří `AZSClient.framework` souboru na ploše.

Pak můžete importovat soubor framework do vaší aplikace následujícím způsobem:

1. Vytvoření nového projektu nebo otevřete existující projekt v Xcode.
2. Přetáhnout myší `AZSClient.framework` do navigátoru projektů Xcode.
3. Vyberte *kopírovat položky v případě potřeby*a klikněte na *Dokončit*.
4. Klikněte na projekt v levém navigačním panelu a klikněte na tlačítko *Obecné* kartě v horní části editoru projektu.
5. V části *propojené architektury a knihovny* klikněte na tlačítko Přidat (+).
6. V seznamu knihoven, které jsou už k dispozici, vyhledejte `libxml2.2.tbd` a přidejte ho do projektu.

## <a name="import-the-library"></a>Importovat knihovny 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Pokud používáte Swift, je potřeba vytvořit hlavičku přemostění Datacenter a importovat < AZSClient/AZSClient.h > existuje:

1. Vytvořte soubor hlaviček `Bridging-Header.h`a přidejte výše uvedený příkaz import.
2. Přejděte *nastavení sestavení* kartu a vyhledejte *hlavičky přemostění jazyka Objective-C*.
3. Dvakrát klikněte na pole *hlavičky přemostění jazyka Objective-C* a přidejte cestu k souboru hlaviček: `ProjectName/Bridging-Header.h`
4. Sestavte projekt (⌘ + B) Chcete-li ověřit, že hlavičku přemostění Datacenter se prodlouží Xcode.
5. Začněte používat knihovny přímo v žádném souboru Swift, není nutné pro příkazy pro import.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchronní operace
> [!NOTE]
> Všechny metody, které provádějí požadavek službu jsou asynchronní operace. V ukázkách kódu zjistíte, že tyto metody mají obslužné rutiny dokončení. Spustí kód uvnitř obslužné rutiny dokončení **po** požadavek dokončit. Kód po dokončení obslužná rutina se spustí **při** provádí požadavek.
> 
> 

## <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob ve službě Azure Storage se musí nacházet v kontejneru. Následující příklad ukazuje, jak vytvořit kontejner volá *newcontainer*, ve vašem účtu úložiště, pokud ještě neexistuje. Když vyberete název kontejneru, dávejte pojmenování pravidel uvedených výše.

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

Můžete potvrdit, že to funguje, zobrazením [Microsoft Azure Storage Explorer](http://storageexplorer.com) a ověříte, že *newcontainer* je v seznamu kontejnerů pro váš účet úložiště.

## <a name="set-container-permissions"></a>Nastavit oprávnění pro kontejner
Kontejneru oprávnění jsou nakonfigurované pro **privátní** přístup ve výchozím nastavení. Ale kontejnerů poskytuje několik různých možností přístupu ke kontejneru:

* **Privátní**: data kontejneru a objektu blob je možné načíst pouze vlastník účtu.
* **Objekt BLOB**: data objektů Blob v tomto kontejneru lze číst prostřednictvím anonymní žádosti, ale kontejnerů dat není k dispozici. Klienty nelze vytvořit výčet objektů BLOB v kontejneru prostřednictvím anonymní žádosti.
* **Kontejner**: kontejneru a objektu blob data lze číst prostřednictvím anonymní žádosti. Klienty můžete zobrazit výčet objektů BLOB v kontejneru prostřednictvím anonymní žádosti, ale nelze vytvořit výčet kontejnery v rámci účtu úložiště.

Následující příklad ukazuje, jak vytvořit kontejner s **kontejneru** přístupová oprávnění, které vám umožní přístup public, jen pro čtení pro všechny uživatele na Internetu:

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
Jak je uvedeno v [koncepty služby Blob](#blob-service-concepts) oddílu, úložiště objektů Blob nabízí tři různé typy objektů blob: objekty BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky. Knihovna pro iOS služby Azure Storage podporuje všechny tři typy objektů BLOB. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Následující příklad ukazuje, jak nahrát objekt blob bloku ze NSString. Pokud se objekt blob se stejným názvem už v tomto kontejneru existuje, obsah tohoto objektu blob bude přepsán.

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

Můžete potvrdit, že to funguje, zobrazením [Microsoft Azure Storage Explorer](http://storageexplorer.com) a ověříte, že kontejner, *containerpublic*, obsahuje objekt blob, *sampleblob*. V této ukázce jsme použili veřejném kontejneru, tak můžete také ověřit, zda tato aplikace funguje tak, že přejdete na identifikátor URI pro objekty BLOB:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Kromě nahrát objekt blob bloku ze NSString, podobné metody existovat NSData, NSInputStream nebo místní soubor.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
Následující příklad ukazuje, jak zobrazit seznam všech objektů BLOB v kontejneru. Při provádění této operace, mějte na paměti následující parametry:     

* **continuationToken** -token představuje pokračování kde operace výpisu by měla začít. Pokud není k dispozici žádný token, zobrazí se seznam objektů BLOB od začátku. Libovolný počet objektů blob je možný, od nuly až do maximální sady. I v případě, že tato metoda vrátí nula výsledků, pokud `results.continuationToken` nemá hodnotu nil, můžou existovat další BLOB ve službě nejsou uvedené.
* **Předpona** -můžete určit předpona, kterou chcete použít pro výpis objektů blob. Zobrazí se pouze na objekty BLOB, které začínají s touto předponou.
* **useFlatBlobListing** – jak je uvedeno v [pojmenování a odkazování na ně kontejnerům a objektům blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) oddíl, i když službu Blob service je schéma plochého úložiště, můžete vytvořit hierarchii virtuální pojmenování objektů BLOB pomocí cesty informace. Bez plochý výpis však není aktuálně podporováno. Tato funkce je již brzy. Prozatím se tato hodnota by měla být **Ano**.
* **blobListingDetails** -můžete určit položky, které chcete zahrnout při výpisu objektů BLOB
  * _AZSBlobListingDetailsNone_: výpis pouze potvrzené objektů BLOB a nevrací metadata objektu blob.
  * _AZSBlobListingDetailsSnapshots_: výpis potvrzené objektů BLOB a snímků objektů blob.
  * _AZSBlobListingDetailsMetadata_: vrátil načíst metadata objektu blob pro každý objekt blob ve výpisu.
  * _AZSBlobListingDetailsUncommittedBlobs_: výpis objektů BLOB nepotvrzené a potvrzené.
  * _AZSBlobListingDetailsCopy_: kopie vlastnosti v seznamu.
  * _AZSBlobListingDetailsAll_: seznam dostupných potvrzené objekty BLOB, nepotvrzené objekty BLOB a snímků a vrátí všechna metadata a kopírování stav pro tyto objekty BLOB.
* **maxResults** – maximální počet výsledků k vrácení této operace. Nenastavovat limit použijte hodnotu -1.
* **completionHandler** – blok kódu, které jsou spouštěny s výsledky operace výpisu.

V tomto příkladu se používá k Pomocná metoda rekurzivní volání seznamu objektů BLOB metoda pokaždé, když se vrátí token pro pokračování.

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
Následující příklad ukazuje, jak stáhnout objekt blob na objekt NSString.

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
Následující příklad ukazuje, jak odstranit objekt blob.

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
Teď, když jste zjistili, jak používat úložiště objektů Blob z iOS, použijte tyto odkazy na další informace o službě úložiště a knihovny iOS.

* [Klientská knihovna Azure Storage pro iOS](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS referenční dokumentaci](http://azure.github.io/azure-storage-ios/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage)

Pokud máte dotazy týkající se této knihovny, neváhejte přidat do našich [fóra MSDN Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) nebo [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Pokud máte návrhy funkcí pro službu Azure Storage, přidejte příspěvek do [zpětné vazby úložiště Azure](https://feedback.azure.com/forums/217298-storage/).

