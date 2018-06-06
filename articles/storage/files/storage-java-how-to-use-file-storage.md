---
title: Vývoj pro Azure soubory s Javou | Microsoft Docs
description: Další informace jak vyvíjet aplikace Java a služby, které používají Azure souborů k ukládání dat souborů.
services: storage
documentationcenter: java
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: a9585bc77a73cbd84fb2efa201a5745c62f3360a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738196"
---
# <a name="develop-for-azure-files-with-java"></a>Vývoj pro Azure soubory s Javou
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>O tomto kurzu
Tento kurz popisuje základní informace o používání Javy k vývoji aplikací nebo služeb, které používají Azure soubory k ukládání dat souborů. V tomto kurzu jsme Vytvořte konzolovou aplikaci a ukazují, jak provést základní operace s Java a Azure souborů:

* Vytvářet a odstraňovat sdílené složky Azure
* Vytvářet a odstraňovat adresáře
* Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
* Odesílání, stahování a odstranění souboru

> [!Note]  
> Protože soubory Azure může mít přístup přes protokol SMB, je možné pro psaní aplikací, které přístup k Azure souborové složce přes standardní třídy Java vstupně-výstupních operací. Tento článek popisuje, jak k psaní aplikací, které používají Java SDK úložiště Azure, kterou používá [REST API služby Azure soubory](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) ke komunikaci s Azure Files.

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
Pokud chcete vytvořit ukázky, budete potřebovat Java Development Kit (JDK) a [sada SDK úložiště Azure pro jazyk Java](https://github.com/Azure/azure-storage-java). Musí také jste vytvořili účet úložiště Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pomocí Azure Files
Pokud chcete používat službu Azure storage rozhraní API, přidejte následující příkaz na začátek souboru Java, které máte v úmyslu přístup ke službě úložiště z.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavit připojovací řetězec úložiště Azure
Použití souborů Azure, budete muset připojit k účtu úložiště Azure. Prvním krokem by mohla být nakonfigurovat připojovací řetězec, který jsme budete používat pro připojení k vašemu účtu úložiště. Umožňuje definovat statické proměnné na to.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Your_storage_account_name a your_storage_account_key nahraďte skutečnými hodnotami pro váš účet úložiště.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Připojení k účtu úložiště Azure
Pokud chcete připojit k účtu úložiště, budete muset použít **CloudStorageAccount** objekt, předávání připojovací řetězec k jeho **analyzovat** metoda.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** vyvolá InvalidKeyException, budete muset uvést uvnitř bloku try/catch.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Všech souborů a adresářů v Azure soubory jsou umístěny v kontejneru názvem **sdílené složky**. Váš účet úložiště může mít libovolný počet sdílených složek jako umožňuje kapacitě vašeho účtu. Pokud chcete získat přístup ke sdílené složce a její obsah, budete muset použít klientem Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Pomocí Azure soubory klienta, můžete pak získat odkaz na sdílenou složku.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Chcete-li ve skutečnosti vytvořit sdílenou složku, použijte **createIfNotExists** metodu objektu CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

V tomto okamžiku **sdílet** obsahuje odkaz na sdílenou složku s názvem **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Odstranit sdílenou složku Azure
Odstranění sdílené složky se provádí volání **deleteIfExists** metoda CloudFileShare objektu. Tady je ukázkový kód, který nemá.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete navíc uspořádat umístěním soubory podadresáře místo nutnosti všechny z nich v kořenovém adresáři. Soubory Azure můžete vytvořit tolik adresáře, které umožní váš účet. Následující kód vytvoří podadresáři s názvem **sampledir** pod kořenovým adresářem.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Odstranění adresáře
Odstranění adresáře je přehledné úlohy, i když je potřeba poznamenat, že nelze odstranit adresář, který ještě obsahuje soubory nebo ostatních adresářů.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
Získání seznamu souborů a adresářů v rámci sdílené složky se snadno provádí voláním **listFilesAndDirectories** na CloudFileDirectory odkaz. Metoda vrátí seznam ListFileItem objektů, které můžete iterovat na. Například následující kód zobrazí seznam souborů a adresářů v kořenovém adresáři.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Nahrání souboru
V této části se dozvíte jak nahrát soubor z místního úložiště do kořenového adresáře sdílenou složku.

Prvním krokem při nahrání souboru se má získat odkaz na adresář, kde by měl být umístěn. To provedete pomocí volání **getRootDirectoryReference** metodu objektu sdílené složky.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teď, když máte odkaz na kořenovém adresáři sdílené složky, můžete nahrát soubor do jej pomocí následujícího kódu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Stažení souboru
Jedním z častější operace, které budete provádět na souborech Azure je ke stažení souborů. V následujícím příkladu kódu stáhne SampleFile.txt a zobrazí její obsah.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Odstranění souboru
Další běžné operace Azure Files je odstranění souborů. Následující kód odstraní soubor s názvem SampleFile.txt uložený v adresáři s názvem **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Další postup
Pokud vás zajímají další informace o dalších úložiště Azure API, použijte tyto odkazy.

* [Azure pro vývojáře v jazyce Java](/java/azure)nebo)
* [Úložiště Azure SDK pro jazyk Java](https://github.com/azure/azure-storage-java)
* [Úložiště Azure SDK pro Android](https://github.com/azure/azure-storage-android)
* [Odkaz na sadu SDK klienta úložiště Azure](http://dl.windowsazure.com/storage/javadoc/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md)
* [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)