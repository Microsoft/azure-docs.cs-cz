---
title: Vývoj pro soubory Azure pomocí Javy | Dokumentace Microsoftu
description: Další informace jak vyvíjet aplikace v Javě a služeb, které používají soubory Azure k ukládání dat souborů.
services: storage
author: wmgries
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 62a771b831c88f73df91c50f34ba6de0d95b832e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453089"
---
# <a name="develop-for-azure-files-with-java"></a>Vývoj pro soubory Azure pomocí Javy
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>O tomto kurzu
Tento kurz vám ukáže základy používání Java k vývoji aplikací a služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu jsme Vytvořte konzolovou aplikaci a ukazují, jak provádět základní akce s Java a službou soubory Azure:

* Vytvářet a odstraňovat sdílené složky Azure
* Vytvoření a odstranění adresářů
* Zobrazení výčtu soubory a adresáře ve sdílené složky Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Protože soubory Azure přístupná přes protokol SMB, je možné psát aplikace, které přistupují k sdílenou složku Azure pomocí standardních tříd Java vstupně-výstupních operací. Tento článek popisuje, jak psát aplikace, které používají Azure Java SDK úložiště, který používá [REST API služby soubory Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) ke komunikaci s Azure Files.

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
Pokud chcete vytvořit ukázky, budete potřebovat Java Development Kit (JDK) a [Azure Storage SDK pro Javu](https://github.com/Azure/azure-storage-java). By měl také jste vytvořili účet úložiště Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace používat soubory Azure
Pokud chcete používat rozhraní API pro Azure storage, přidejte následující příkaz do horní části souboru Java, kde máte v úmyslu přístup ke službě storage z.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure storage
Používat soubory Azure, budete muset připojit ke svému účtu Azure storage. Prvním krokem je konfigurace připojovací řetězec, který jsme budete používat pro připojení k vašemu účtu úložiště. Umožňuje definovat statická proměnná to udělat.

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

## <a name="connecting-to-an-azure-storage-account"></a>Připojení k účtu služby Azure storage
Pro připojení k účtu úložiště, budete muset použít **CloudStorageAccount** objekt předávání připojovací řetězec pro jeho **analyzovat** metody.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** vyvolá InvalidKeyException tak bude potřeba umístit uvnitř bloku try/catch.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Všechny soubory a adresáře ve službě soubory Azure se nacházejí v kontejneru s názvem **sdílené složky**. Váš účet úložiště může mít libovolný počet sdílených složek jako umožňuje vaší kapacity účtu. Pokud chcete získat přístup ke sdílené složce a její obsah, budete muset použít klienta služby soubory Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Pomocí klienta Azure Files, můžete pak získat odkaz na sdílenou složku.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Chcete-li vytvořit sdílenou složku, použijte **createIfNotExists** metodu CloudFileShare objektu.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

V tomto okamžiku **sdílet** obsahuje odkaz na sdílenou složku s názvem **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Odstranění sdílené složky Azure
Odstranění sdílené složky se provádí pomocí volání **deleteIfExists** metodu na objekt CloudFileShare. Tady je ukázkový kód, který činí.

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
Úložiště můžete také uspořádat tak, že vložíte soubory v rámci podadresáře namísto toho, aby všechny z nich v kořenovém adresáři. Služba soubory Azure umožňuje vytvářet tolik adresáře, které umožní váš účet. Následující kód vytvoří podadresář s názvem **sampledir** pod kořenovým adresářem.

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
Odstraňuje se adresář je jednoduchý úkol, i když je třeba poznamenat, že nelze odstranit adresář, který se stále obsahuje soubory nebo jiné adresáře.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu soubory a adresáře ve sdílené složky Azure
Získání seznamu souborů a adresářů v rámci sdílené složky se snadno provádí pomocí volání **listFilesAndDirectories** na CloudFileDirectory odkazu. Metoda vrátí seznam hodnot ListFileItem objekty, které můžete iterovat. Například následující kód zobrazí seznam souborů a adresářů v kořenovém adresáři.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Nahrání souboru
V této části se dozvíte, jak nahrát soubor z místního úložiště na kořenovém adresáři sdílené složky.

Prvním krokem při nahrání souboru se k získání odkazu na adresář, ve kterém by měl být uložený. To provedete voláním **getRootDirectoryReference** metodu objektu sdílené složky.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teď, když máte odkaz na kořenovém adresáři sdílené složky, můžete nahrát soubor problém napravit pomocí následujícího kódu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Stažení souboru
Jednou z častější operace, které provedete před soubory Azure je ke stažení souborů. V následujícím příkladu kódu stáhne SampleFile.txt a zobrazí jeho obsah.

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
Další běžné operace soubory Azure je odstranění souborů. Následující kód odstraní soubor s názvem SampleFile.txt uložený v adresáři s názvem **sampledir**.

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
Pokud chcete získat další informace o dalších Azure rozhraní API služby storage, na následujících odkazech.

* [Azure pro vývojáře v Javě](/java/azure)/)
* [Azure Storage SDK pro Javu](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK pro Android](https://github.com/azure/azure-storage-android)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md)
* [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
