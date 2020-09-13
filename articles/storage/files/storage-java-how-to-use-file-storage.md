---
title: Vývoj pro soubory Azure pomocí Java | Microsoft Docs
description: Naučte se vyvíjet aplikace a služby Java, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2c80d741ca1cf0b4d66bc18488af8ef1da44fd14
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462594"
---
# <a name="develop-for-azure-files-with-java"></a>Vývoj pro Soubory Azure pomocí Javy
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>O tomto kurzu
V tomto kurzu se dozvíte základy použití jazyka Java k vývoji aplikací nebo služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu vytvoříme konzolovou aplikaci a ukážeme, jak provádět základní akce s použitím souborů Java a Azure:

* Vytváření a odstraňování sdílených složek Azure
* Vytváření a odstraňování adresářů
* Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Vzhledem k tomu, že soubory Azure mohou být přístupné přes protokol SMB, je možné psát aplikace, které přistupují ke sdílené složce Azure pomocí standardních tříd v/v jazyka Java. Tento článek popisuje, jak psát aplikace, které používají sadu SDK Azure Storage Java, která používá [REST API souborů Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) ke komunikaci se soubory Azure.

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
K sestavení ukázek budete potřebovat Java Development Kit (JDK) a [sadu SDK pro Azure Storage pro jazyk Java](https://github.com/Azure/azure-storage-java). Měli byste také vytvořit účet služby Azure Storage.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure
Pokud chcete použít rozhraní API služby Azure Storage, přidejte do horní části souboru Java, do kterého chcete získat přístup ke službě úložiště, následující příkaz.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage
Pokud chcete používat soubory Azure, musíte se připojit k účtu služby Azure Storage. Prvním krokem je konfigurace připojovacího řetězce, který použijeme pro připojení k vašemu účtu úložiště. Pojďme definovat statickou proměnnou k tomu.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Nahraďte your_storage_account_name a your_storage_account_key skutečnými hodnotami pro váš účet úložiště.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Připojení k účtu služby Azure Storage
Chcete-li se připojit k účtu úložiště, je nutné použít objekt **CloudStorageAccount** a předáním připojovacího řetězce do jeho metody **Parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. Parse** vyvolá InvalidKeyException, takže ho budete muset vložit do bloku try/catch.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Všechny soubory a adresáře v souborech Azure se nacházejí v kontejneru nazývaném **sdílená složka**. Váš účet úložiště může mít tolik sdílených složek, kolik umožňuje vaše kapacita účtu. Pokud chcete získat přístup ke sdílené složce a jejímu obsahu, musíte použít klienta souborů Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Pomocí klienta souborů Azure můžete získat odkaz na sdílenou složku.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Chcete-li skutečně vytvořit sdílenou složku, použijte metodu **createIfNotExists** objektu CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

V tomto okamžiku **sdílená složka** uchovává odkaz na sdílenou složku s názvem **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Odstranění sdílené složky Azure
Odstranění sdílené složky se provádí voláním metody **deleteIfExists** v objektu CloudFileShare. Zde je ukázkový kód, který to dělá.

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
Úložiště můžete také uspořádat tak, že umístíte soubory do podadresářů místo jejich použití v kořenovém adresáři. Soubory Azure vám umožní vytvořit tolik adresářů, kolik bude váš účet umožňovat. Následující kód vytvoří podadresář s názvem **sampledir** v kořenovém adresáři.

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
Odstranění adresáře je přímočarý úkol, i když je třeba poznamenat, že nemůžete odstranit adresář, který stále obsahuje soubory nebo jiné adresáře.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
Seznam souborů a adresářů ve sdílené složce je možné snadno provést voláním **listFilesAndDirectories** na odkaz CloudFileDirectory. Metoda vrátí seznam objektů ListFileItem, na kterých lze iterovat. Například následující kód bude v kořenovém adresáři zobrazovat soubory a adresáře.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Nahrání souboru
V této části se dozvíte, jak nahrát soubor z místního úložiště do kořenového adresáře sdílené složky.

Prvním krokem při nahrávání souboru je získání odkazu na adresář, ve kterém by měl být umístěný. Provedete to tak, že zavoláte metodu **getRootDirectoryReference** objektu Share.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teď, když máte odkaz na kořenový adresář sdílené složky, můžete do něj nahrát soubor pomocí následujícího kódu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Stažení souboru
Jednou z častých operací, které v Azure Files provedete, je stažení souborů. V následujícím příkladu kód stáhne SampleFile.txt a zobrazí jeho obsah.

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
Další běžnou operací se soubory Azure je odstraňování souborů. Následující kód odstraní soubor s názvem SampleFile.txt uložený v adresáři s názvem **sampledir**.

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

## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o dalších rozhraních API služby Azure Storage, postupujte podle těchto odkazů.

* [Azure pro vývojáře v jazyce Java](/java/azure)/)
* [Sada SDK služby Azure Storage pro Javu](https://github.com/azure/azure-storage-java)
* [Sada Azure Storage SDK pro Android](https://github.com/azure/azure-storage-android)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md)
* [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
