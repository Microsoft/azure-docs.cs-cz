---
title: Vývoj pro soubory Azure s Javou | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyvíjet java aplikace a služby, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837458"
---
# <a name="develop-for-azure-files-with-java"></a>Vývoj pro Soubory Azure pomocí Javy
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>O tomto kurzu
Tento kurz předvede základy používání Javy k vývoji aplikací nebo služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu vytvoříme konzolovou aplikaci a ukážeme, jak provádět základní akce s Java a Soubory Azure:

* Vytvoření a odstranění sdílených složek Azure
* Vytvoření a odstranění adresářů
* Vytvoření výčtu souborů a adresářů ve sdílené složce Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Vzhledem k tomu, že k souborům Azure lze přistupovat přes SMB, je možné zapisovat aplikace, které přistupují ke sdílené složce Azure pomocí standardních tříd Java I/O. Tento článek popisuje, jak psát aplikace, které používají Azure Storage Java SDK, který používá [rozhraní API Azure Files REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) pro mluvení do souborů Azure.

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
K sestavení ukázek budete potřebovat Java Development Kit (JDK) a [Azure Storage SDK pro Javu](https://github.com/Azure/azure-storage-java). Měli jste také vytvořit účet úložiště Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure
Chcete-li použít azure storage API, přidejte následující příkaz do horní části souboru Java, kde máte v úmyslu získat přístup ke službě úložiště z.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce úložiště Azure
Pokud chcete používat Soubory Azure, musíte se připojit ke svému účtu úložiště Azure. Prvním krokem by bylo nakonfigurovat připojovací řetězec, který použijeme pro připojení k účtu úložiště. Pojďme definovat statickou proměnnou k tomu, že.

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

## <a name="connecting-to-an-azure-storage-account"></a>Připojení k účtu úložiště Azure
Chcete-li se připojit k účtu úložiště, musíte použít objekt **CloudStorageAccount** a předat připojovací řetězec metodě **analýzy.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** vyvolá InvalidKeyException, takže budete muset umístit uvnitř try/catch bloku.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Všechny soubory a adresáře v Azure Files jsou umístěny v kontejneru s názvem **Sdílení**. Váš účet úložiště může mít tolik sdílených složek, kolik kapacita vašeho účtu umožňuje. Chcete-li získat přístup ke sdílené položce a jejímu obsahu, musíte použít klienta Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Pomocí klienta Soubory Azure pak můžete získat odkaz na sdílenou složku.

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

V tomto okamžiku **share** obsahuje odkaz na podíl s názvem **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Odstranění sdílené složky Azure
Odstranění sdílené složky se provádí voláním metody **deleteIfExists** na objektu CloudFileShare. Zde je ukázkový kód, který to dělá.

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
Úložiště můžete také uspořádat umístěním souborů do podadresářů namísto toho, abyste je měli všechny v kořenovém adresáři. Azure Files umožňuje vytvořit tolik adresářů, kolik váš účet povolí. Níže uvedený kód vytvoří podadresář s názvem **sampledir** pod kořenovým adresářem.

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
Odstranění adresáře je přímočará úloha, i když je třeba poznamenat, že nelze odstranit adresář, který stále obsahuje soubory nebo jiné adresáře.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Vytvoření výčtu souborů a adresářů ve sdílené složce Azure
Získání seznamu souborů a adresářů v rámci sdílené složky lze snadno provést voláním **listFilesAndDirectories** na odkaz CloudFileDirectory. Metoda vrátí seznam objektů ListFileItem, na kterých můžete iterate. Například následující kód zobrazí seznam souborů a adresářů v kořenovém adresáři.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Nahrání souboru
V této části se dozvíte, jak nahrát soubor z místního úložiště do kořenového adresáře sdílené složky.

Prvním krokem při nahrávání souboru je získat odkaz na adresář, kde by měl být umístěn. To provést voláním metody **getRootDirectoryReference** objektu sdílení.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nyní, když máte odkaz na kořenový adresář sdílené položky, můžete do něj nahrát soubor pomocí následujícího kódu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Stažení souboru
Jednou z častějších operací, které budete provádět proti souborům Azure, je stahování souborů. V následujícím příkladu kód stáhne SampleFile.txt a zobrazí jeho obsah.

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
Další běžnou operací Azure Files je odstranění souborů. Následující kód odstraní soubor s názvem SampleFile.txt uložený v adresáři s názvem **sampledir**.

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
Pokud se chcete dozvědět víc o dalších azure storage API, postupujte podle těchto odkazů.

* [Vývojáři Azure pro Javu](/java/azure)/)
* [Sada SDK služby Azure Storage pro Javu](https://github.com/azure/azure-storage-java)
* [Sada Azure Storage SDK pro Android](https://github.com/azure/azure-storage-android)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md)
* [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
