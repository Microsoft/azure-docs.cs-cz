---
title: Jak používat úložiště objektů (BLOB) z iOS – Azure | Microsoft Docs
description: Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 378c21a6904acad16847bb32955e4bc091e587df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465486"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak používat úložiště objektů BLOB ze systému iOS

Tento článek popisuje, jak provádět běžné scénáře pomocí Microsoft Azure Blob Storage. Ukázky jsou napsány v cíli-C a používají [Azure Storage klientské knihovny pro iOS](https://github.com/Azure/azure-storage-ios). Mezi zahrnuté scénáře patří nahrávání, výpis, stahování a odstraňování objektů BLOB. Další informace o objektech blob najdete v části [Další kroky](#next-steps) . [Ukázkovou aplikaci](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) si také můžete stáhnout a rychle tak zobrazit použití Azure Storage v aplikaci pro iOS.

Další informace o službě BLOB Storage najdete v tématu [Úvod do úložiště objektů BLOB v Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Import knihovny iOS Azure Storage do vaší aplikace

Do své aplikace můžete importovat knihovnu Azure Storage iOS buď pomocí [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) , nebo importováním souboru **architektury** . CocoaPod je doporučený způsob, jak usnadňuje integraci knihovny, ale import ze souboru rozhraní je pro váš stávající projekt méně rušivý.

K použití této knihovny potřebujete následující:

- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Pokud jste to ještě neudělali, nainstalujte do svého počítače [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) tak, že otevřete okno terminálu a spustíte následující příkaz.

    ```shell
    sudo gem install cocoapods
    ```

2. Dále v adresáři projektu (adresář obsahující soubor. xcodeproj) vytvořte nový soubor s názvem _souboru podfile_(bez přípony souboru). Přidejte následující _souboru podfile_ a uložte je.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. V okně terminálu přejděte do adresáře projektu a spusťte následující příkaz.

    ```shell
    pod install
    ```

4. Pokud je váš. xcodeproj otevřený v Xcode, zavřete ho. V adresáři projektu otevřete nově vytvořený soubor projektu, který bude mít příponu. xcworkspace. Jedná se o soubor, ze kterého teď budete pracovat.

## <a name="framework"></a>Rozhraní .NET Framework

Dalším způsobem, jak použít knihovnu, je sestavit rozhraní ručně:

1. Nejdřív si stáhněte nebo naklonujte [úložiště Azure-Storage-iOS](https://github.com/azure/azure-storage-ios).
2. Přejít do knihovny *Azure-Storage-iOS*  ->  *lib*  ->  *Azure Storage klientské knihovny*a otevřít `AZSClient.xcodeproj` v Xcode.
3. V levém horním rohu Xcode Změňte aktivní schéma z "Azure Storage Klientská knihovna" na "Framework".
4. Sestavte projekt (⌘ + B). Tím se vytvoří `AZSClient.framework` soubor na ploše.

Pak můžete soubor architektury importovat do aplikace pomocí následujícího postupu:

1. Vytvořte nový projekt nebo otevřete existující projekt v Xcode.
2. Přetáhněte `AZSClient.framework` do navigátoru projektu Xcode.
3. V *případě potřeby vyberte Kopírovat položky*a klikněte na *Dokončit*.
4. V levém navigačním panelu klikněte na projekt a klikněte na kartu *Obecné* v horní části editoru projektu.
5. V části *propojené architektury a knihovny* klikněte na tlačítko Přidat (+).
6. V seznamu již poskytnutých knihoven vyhledejte `libxml2.2.tbd` a přidejte ho do projektu.

## <a name="import-the-library"></a>Import knihovny

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Pokud používáte SWIFT, budete muset vytvořit hlavičku přemostění a naimportovat ji \<AZSClient/AZSClient.h> :

1. Vytvořte hlavičkový soubor `Bridging-Header.h` a přidejte výše uvedený příkaz import.
2. Přejdete na kartu *nastavení sestavení* a vyhledejte *záhlaví cíl-C přemostění*.
3. Poklikejte na pole *hlavičky přemostění v cíli C* a přidejte cestu k souboru hlaviček: `ProjectName/Bridging-Header.h`
4. Sestavte projekt (⌘ + B), abyste ověřili, že se hlavička přemostění vybrala prostřednictvím Xcode.
5. Začněte používat knihovnu přímo v jakémkoli souboru SWIFT, není nutné importovat příkazy.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchronní operace

> [!NOTE]
> Všechny metody, které provádějí požadavky na službu, jsou asynchronní operace. V ukázkách kódu zjistíte, že tyto metody mají obslužnou rutinu dokončení. Kód uvnitř obslužné rutiny dokončení se spustí **po** dokončení žádosti. Po **spuštění žádosti** se kód po obslužné rutině dokončení spustí.

## <a name="create-a-container"></a>Vytvoření kontejneru

Každý objekt BLOB v Azure Storage musí být umístěn v kontejneru. Následující příklad ukazuje, jak vytvořit kontejner s názvem *newcontainer*v účtu úložiště, pokud ještě neexistuje. Při volbě názvu pro svůj kontejner nezapomeňte na pravidla pojmenování uvedená výše.

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

To můžete ověřit tak, že si vyhledáte [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com) a ověříte, že *newcontainer* je v seznamu kontejnerů pro váš účet úložiště.

## <a name="set-container-permissions"></a>Nastavení oprávnění kontejneru

Ve výchozím nastavení jsou oprávnění kontejneru nakonfigurovaná pro **privátní** přístup. Kontejnery ale poskytují několik různých možností pro přístup k kontejneru:

- **Private**: data kontejneru a objektu BLOB může číst pouze vlastník účtu.
- **Objekt BLOB**: data objektu BLOB v tomto kontejneru lze číst prostřednictvím anonymního požadavku, ale data kontejneru nejsou k dispozici. Klienti nemohou vypsat objekty BLOB v kontejneru prostřednictvím anonymního požadavku.
- **Kontejner**: data kontejneru a objektu BLOB lze číst prostřednictvím anonymního požadavku. Klienti mohou vytvořit výčet objektů BLOB v kontejneru prostřednictvím anonymního požadavku, ale nemohou vytvořit výčet kontejnerů v rámci účtu úložiště.

Následující příklad ukazuje, jak vytvořit kontejner s přístupovými oprávněními **kontejneru** , který umožní veřejný přístup jen pro čtení pro všechny uživatele na internetu:

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

Jak je uvedeno v části koncepty Blob service, Blob Storage nabízí tři různé typy objektů BLOB: objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky. Knihovna Azure Storage iOS podporuje všechny tři typy objektů BLOB. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Následující příklad ukazuje, jak nahrát objekt blob bloku z NSString. Pokud v tomto kontejneru již existuje objekt BLOB se stejným názvem, obsah tohoto objektu BLOB bude přepsán.

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

To můžete ověřit tak, že se podíváte na [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com) a ověříte, že kontejner *containerpublic*obsahuje objekt BLOB *sampleblob*. V této ukázce jsme použili veřejný kontejner, takže můžete také ověřit, že se tato aplikace pracovala, a to tak, že se vrátí k identifikátoru URI objektů BLOB:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Kromě nahrávání objektu blob bloku z NSString existují podobné metody pro NSData, NSInputStream nebo místní soubor.

## <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Následující příklad ukazuje, jak zobrazit seznam všech objektů BLOB v kontejneru. Při provádění této operace je potřeba mít na vědomí následující parametry:

- **token continuationtoken** – token pokračování představuje, kde by se měla začínat operace výpisu. Pokud není zadaný žádný token, vypíše objekty blob od začátku. Je možné uvést libovolný počet objektů blob, od nuly až po nastavené maximum. I v případě, že tato metoda vrátí nulové výsledky, pokud `results.continuationToken` není Nil, může existovat více objektů BLOB ve službě, které nebyly uvedeny.
- **prefix** – můžete zadat předponu, která se má použít pro výpis objektu BLOB. Zobrazí se pouze objekty blob začínající touto předponou.
- **useFlatBlobListing** – jak je uvedeno v části [pojmenování a odkazování na kontejnery a objekty blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , i když BLOB Service je schéma plochého úložiště, můžete vytvořit virtuální hierarchii pojmenování objektů BLOB s informacemi o cestě. Neplochý seznam se ale v tuto chvíli nepodporuje. Tato funkce se už brzo blíží. Teď by tato hodnota měla být **Ano**.
- **blobListingDetails** – můžete určit, které položky se mají zahrnout při výpisu objektů BLOB.
  - _AZSBlobListingDetailsNone_: listuje pouze potvrzené objekty BLOB a nevrací metadata objektu BLOB.
  - _AZSBlobListingDetailsSnapshots_: vypíše potvrzené objekty BLOB a snímky objektů BLOB.
  - _AZSBlobListingDetailsMetadata_: načte metadata objektu BLOB pro každý objekt BLOB vrácený v seznamu.
  - _AZSBlobListingDetailsUncommittedBlobs_: vypíše potvrzené a nepotvrzené objekty blob.
  - _AZSBlobListingDetailsCopy_: zahrnout vlastnosti kopírování do výpisu.
  - _AZSBlobListingDetailsAll_: vypíše všechny dostupné potvrzené objekty blob, nepotvrzené objekty BLOB a snímky a vrátí všechny metadata a stav kopírování těchto objektů BLOB.
- **maxResults** – maximální počet výsledků, které se mají vrátit pro tuto operaci. Pro omezení nepoužívejte hodnotu-1.
- **completionHandler** – blok kódu, který se má provést s výsledky operace výpisu.

V tomto příkladu je použita pomocná metoda pro rekurzivní volání metody list BLOBs pokaždé, když je vrácen token pro pokračování.

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

Následující příklad ukazuje, jak stáhnout objekt blob do objektu NSString.

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

Následující příklad ukazuje, jak odstranit objekt BLOB.

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

## <a name="delete-a-blob-container"></a>Odstranění kontejneru objektů BLOB

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

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat Blob Storage v iOS, použijte následující odkazy, kde se dozvíte víc o knihovně iOS a službě úložiště.

- [Klientská knihovna Azure Storage pro iOS](https://github.com/azure/azure-storage-ios)
- [Referenční dokumentace k Azure Storage iOS](https://azure.github.io/azure-storage-ios/)
- [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog týmu Azure Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)

Pokud máte dotazy týkající se této knihovny, můžete se vystavit na našem [webu Microsoft Q&stránku s dotazy](https://docs.microsoft.com/answers/topics/azure-blob-storage.html) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Pokud máte návrhy na funkce pro Azure Storage, odešlete příspěvek [Azure Storage zpětnou vazbu](https://feedback.azure.com/forums/217298-storage/).
