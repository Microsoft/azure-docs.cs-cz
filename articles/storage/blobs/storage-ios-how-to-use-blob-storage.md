---
title: Jak používat úložiště objektů (Blob) z iOS – Azure | Dokumenty společnosti Microsoft
description: Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726410"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak používat úložiště objektů Blob z iOS

Tento článek ukazuje, jak provádět běžné scénáře pomocí úložiště objektů blob Microsoft Azure. Ukázky jsou zapsány v Objective-C a použít [knihovnu klienta úložiště Azure pro iOS](https://github.com/Azure/azure-storage-ios). Zahrnuté scénáře zahrnují nahrávání, výpis, stahování a odstranění objektů BLOB. Další informace o objektech BLOB najdete v části [Další kroky.](#next-steps) Ukázkovou [aplikaci](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) si taky můžete stáhnout a rychle zobrazit využití Azure Storage v aplikaci pro iOS.

Další informace o úložišti objektů Blob najdete [v tématu Úvod do úložiště objektů blob Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Import knihovny Azure Storage pro iOS do aplikace

Knihovnu Azure Storage iOS můžete importovat do vaší aplikace buď pomocí [Azure Storage CocoaPod,](https://cocoapods.org/pods/AZSClient) nebo importováním souboru **Framework.** CocoaPod je doporučený způsob, jak to usnadňuje integraci knihovny, ale import z rámcového souboru je méně rušivé pro váš stávající projekt.

Chcete-li používat tuto knihovnu, potřebujete následující:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod

1. Pokud jste tak ještě neučinili, [nainstalujte kakaové pody](https://guides.cocoapods.org/using/getting-started.html#toc_3) do počítače otevřením okna terminálu a spuštěním následujícího příkazu

    ```shell
    sudo gem install cocoapods
    ```

2. Dále v adresáři projektu (adresář obsahující váš soubor .xcodeproj) vytvořte nový soubor s názvem _Podfile_(bez přípony). Přidejte následující do _Podfile_ a uložte.

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

4. Pokud je váš .xcodeproj otevřen v Xcode, zavřete jej. V adresáři projektu otevřete nově vytvořený soubor projektu, který bude mít příponu Xcworkspace. Tohle je soubor, na který budeš pracovat.

## <a name="framework"></a>Rozhraní .NET Framework

Jiný způsob, jak použít knihovnu je vytvořit rozhraní ručně:

1. Nejprve stáhněte nebo naklonujte [úložiště azure-ios repo](https://github.com/azure/azure-storage-ios).
2. Přejděte do*Lib* ->  *klientské knihovny* -> *Azure Storage Library* `AZSClient.xcodeproj` azure a otevřete v Xcode.
3. V levém horním rohu Xcode změňte aktivní schéma z "Azure Storage Client Library" na "Framework".
4. Sestavte projekt (中+B). Tím se `AZSClient.framework` vytvoří soubor na ploše.

Soubor frameworku pak můžete importovat do aplikace následujícím způsobem:

1. Vytvořte nový projekt nebo otevřete stávající projekt v Xcode.
2. Přetáhněte `AZSClient.framework` jej do navigátoru projektu Xcode.
3. V případě potřeby vyberte *Kopírovat položky*a klepněte na *tlačítko Dokončit*.
4. Klikněte na svůj projekt v levém navigačním panelu a klikněte na kartu *Obecné* v horní části editoru projektu.
5. V části *Propojené architektury a knihovny* klikněte na tlačítko Přidat (+).
6. V seznamu již poskytnutých knihoven `libxml2.2.tbd` vyhledejte a přidejte jej do projektu.

## <a name="import-the-library"></a>Import knihovny

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Pokud používáte Swift, budete muset vytvořit překlenovací hlavičku a importovat \<AZSClient/AZSClient.h> tam:

1. Vytvořte soubor `Bridging-Header.h`záhlaví a přidejte výše uvedený příkaz importu.
2. Přejděte na kartu *Nastavení sestavení* a vyhledejte *položku Přemostění cíle C*.
3. Poklepejte na pole *Překlenovací hlavičky Objective-C* a přidejte cestu do souboru záhlaví:`ProjectName/Bridging-Header.h`
4. Sestavte projekt (中+B) a ověřte, zda byla hlavička přemostění vyzvednuta xcodem.
5. Začněte používat knihovnu přímo v libovolném souboru Swift, není třeba importovat příkazy.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchronní operace

> [!NOTE]
> Všechny metody, které provádějí požadavek proti službě jsou asynchronní operace. Ve vzorcích kódu zjistíte, že tyto metody mají obslužnou rutinu dokončení. Kód uvnitř obslužné rutiny dokončení bude spuštěn **po** dokončení požadavku. Kód po dokončení obslužná rutina bude spuštěna **při** požadavku.

## <a name="create-a-container"></a>Vytvoření kontejneru

Každý objekt blob ve službě Azure Storage musí být umístěn v kontejneru. Následující příklad ukazuje, jak vytvořit kontejner, nazvaný *newcontainer*, ve vašem účtu úložiště, pokud ještě neexistuje. Při výběru názvu kontejneru mějte na paměti výše uvedená pravidla pro pojmenování.

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

Můžete potvrdit, že to funguje na hlédnutí do [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com) a ověření, že *newcontainer* je v seznamu kontejnerů pro váš účet úložiště.

## <a name="set-container-permissions"></a>Nastavení oprávnění kontejneru

Oprávnění kontejneru jsou ve výchozím nastavení **nakonfigurována** pro soukromý přístup. Kontejnery však poskytují několik různých možností pro přístup ke kontejneru:

- **Soukromé**: Data kontejneru a objektu blob může číst pouze vlastník účtu.
- **Objekt blob**: Data objektů blob v rámci tohoto kontejneru lze číst prostřednictvím anonymní požadavek, ale data kontejneru není k dispozici. Klienti nelze vytvořit výčet objektů BLOB v rámci kontejneru prostřednictvím anonymní požadavek.
- **Kontejner**: Data kontejneru a objektu blob lze číst prostřednictvím anonymního požadavku. Klienti můžete vytvořit výčet objektů BLOB v rámci kontejneru prostřednictvím anonymní požadavek, ale nelze vytvořit výčet kontejnerů v rámci účtu úložiště.

Následující příklad ukazuje, jak vytvořit kontejner s oprávněními pro přístup **k kontejneru,** který umožní veřejný přístup jen pro čtení pro všechny uživatele na Internetu:

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

Jak je uvedeno v části koncepty služby objektů Blob, úložiště objektů blob nabízí tři různé typy objektů BLOB: objekty BLOB bloku, objekty BLOB připojení a objekty BLOB stránky. Knihovna Azure Storage iOS podporuje všechny tři typy objektů BLOB. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Následující příklad ukazuje, jak nahrát objekt blob bloku z NSString. Pokud objekt blob se stejným názvem již existuje v tomto kontejneru, obsah tohoto objektu blob bude přepsán.

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

Můžete potvrdit, že to funguje na hlédnutí do [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com) a ověření, že kontejner, *containerpublic*, obsahuje objekt blob, *sampleblob*. V této ukázce jsme použili veřejný kontejner, takže můžete také ověřit, že tato aplikace fungovala tak, že přejdete na identifikátor URI objektů BLOB:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Kromě nahrávání objektu blob bloku z NSString existují podobné metody pro NSData, NSInputStream nebo místní soubor.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Následující příklad ukazuje, jak vypsat všechny objekty BLOB v kontejneru. Při provádění této operace mějte na paměti následující parametry:

- **continuationToken** - Token pokračování představuje, kde by měla být zahájena operace výpisu. Pokud není k dispozici žádný token, bude seznam objektů BLOB od začátku. Může být uveden libovolný počet objektů BLOB, od nuly až po nastavené maximum. I v případě, že `results.continuationToken` tato metoda vrátí nulové výsledky, pokud není nula, může být více objektů BLOB ve službě, které nebyly uvedeny.
- **předpona** - Můžete zadat předponu, která se má použít pro výpis objektů blob. Budou uvedeny pouze objekty BLOB, které začínají touto předponou.
- **useFlatBlobListing** - Jak je uvedeno v [pojmenování a odkazování kontejnerů a objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) části, i když služba blob je schéma ploché úložiště, můžete vytvořit virtuální hierarchii pojmenováníobjektů objektů BLOB s informacemi o cestě. Nicméně, non-byt výpis není v současné době podporována. Tato funkce je již brzy. Prozatím by tato hodnota měla být **ANO**.
- **blobListingDetails** – můžete určit, které položky mají být zahrnuty při výpisu objektů BLOB
  - _AZSBlobListingDetailsNone_: Seznam pouze potvrzené objekty BLOB a nevracejí metadata objektu blob.
  - _AZSBlobListingDetailsSnapshots_: Seznam potvrzených objektů blob a snímky objektů blob.
  - _AZSBlobListingDetailsMetadata_: Načtení metadat objektu blob pro každý objekt blob vrácený v seznamu.
  - _AZSBlobListingDetailsUncommittedBlobs_: Seznam potvrzených a nepotvrzených objektů BLOB.
  - _AZSBlobListingDetailsCopy_: Zahrnout vlastnosti kopírování do výpisu.
  - _AZSBlobListingDetailsAll_: Seznam všech dostupných potvrzených objektů BLOB, nepotvrzených objektů BLOB a snímků a vrátí všechna metadata a stav kopírování pro tyto objekty BLOB.
- **maxResults** - Maximální počet výsledků vrátit pro tuto operaci. Použijte -1 nenastavovat limit.
- **completionHandler** - Blok kódu, který se má provést s výsledky výpisu operace.

V tomto příkladu pomocná metoda se používá k rekurzivně volat seznam objektů blob metodu pokaždé, když je vrácen token pokračování.

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

## <a name="delete-a-blob-container"></a>Odstranění kontejneru objektů blob

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

Teď, když jste se naučili používat úložiště objektů Blob z iOS, na těchto odkazech se dozvíte víc o knihovně iOS a službě Storage.

- [Klientská knihovna úložiště Azure pro iOS](https://github.com/azure/azure-storage-ios)
- [Referenční dokumentace k úložišti Azure pro iOS](https://azure.github.io/azure-storage-ios/)
- [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage)

Máte-li dotazy týkající se této knihovny, neváhejte a pošlete příspěvek na našem [fóru MSDN Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) nebo [přetečení zásobníku](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Pokud máte návrhy funkcí pro Azure Storage, zaúčtujte do [Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage/).
