---
title: Vývoj pro soubory Azure pomocí Java | Microsoft Docs
description: Naučte se vyvíjet aplikace a služby Java, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024105"
---
# <a name="develop-for-azure-files-with-java"></a>Vývoj pro Soubory Azure pomocí Javy

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Naučte se základy vývoje aplikací Java, které používají soubory Azure k ukládání dat. Vytvořte konzolovou aplikaci a Naučte se základní akce pomocí rozhraní API souborů Azure:

- Vytváření a odstraňování sdílených složek Azure
- Vytváření a odstraňování adresářů
- Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
- Nahrání, stažení a odstranění souboru

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java

K sestavení ukázek budete potřebovat Java Development Kit (JDK) a [sadu SDK pro Azure Storage pro jazyk Java](https://github.com/azure/azure-sdk-for-java). Měli byste také vytvořit účet služby Azure Storage.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure

Pokud chcete používat rozhraní API souborů Azure, přidejte do horní části souboru Java následující kód, ze kterého máte v úmyslu přistupovat k souborům Azure.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Pokud chcete používat soubory Azure, musíte se připojit k účtu služby Azure Storage. Nakonfigurujte připojovací řetězec a použijte ho pro připojení k vašemu účtu úložiště. Definujte statickou proměnnou pro uložení připojovacího řetězce.

# <a name="java-v12"></a>[Java V12](#tab/java)

Nahraďte *\<storage_account_name\>* a *\<storage_account_key\>* skutečnými hodnotami pro váš účet úložiště.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Nahraďte *your_storage_account_name* a *your_storage_account_key* skutečnými hodnotami pro váš účet úložiště.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Přístup k úložišti Azure Files

# <a name="java-v12"></a>[Java V12](#tab/java)

Pro přístup k souborům Azure vytvořte objekt [ShareClient](/java/api/com.azure.storage.file.share.shareclient) . Použijte třídu [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) k sestavení nového objektu **ShareClient** .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Chcete-li získat přístup k účtu úložiště, použijte objekt **CloudStorageAccount** a předáním připojovacího řetězce do jeho metody **Parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. Parse** vyvolá InvalidKeyException, takže ho budete muset vložit do bloku try/catch.

---

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Všechny soubory a adresáře v souborech Azure se ukládají do kontejneru nazývaného sdílená složka.

# <a name="java-v12"></a>[Java V12](#tab/java)

Metoda [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) vyvolá výjimku, Pokud sdílená složka již existuje. Vložte volání pro **Vytvoření** v `try/catch` bloku a zpracujte výjimku.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Pokud chcete získat přístup ke sdílené složce a jejímu obsahu, vytvořte klienta souborů Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Pomocí klienta souborů Azure můžete získat odkaz na sdílenou složku.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Chcete-li skutečně vytvořit sdílenou složku, použijte metodu **createIfNotExists** objektu **CloudFileShare** .

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

V tomto okamžiku **sdílená** složka uchovává odkaz na sdílenou složku s názvem **Vzorová sdílená složka**.

---

## <a name="delete-a-file-share"></a>Odstranění sdílené složky

Následující vzorový kód odstraní sdílenou složku.

# <a name="java-v12"></a>[Java V12](#tab/java)

Odstraňte sdílenou složku voláním metody [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Odstraňte sdílenou složku voláním metody **deleteIfExists** v objektu **CloudFileShare** .

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

---

## <a name="create-a-directory"></a>Vytvoření adresáře

Uspořádejte úložiště tak, že umístíte soubory do podadresářů místo jejich použití v kořenovém adresáři.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující kód vytvoří adresář voláním [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Ukázková metoda vrátí `Boolean` hodnotu, která označuje, zda úspěšně vytvořil adresář.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Následující kód vytvoří podadresář s názvem **sampledir** v kořenovém adresáři.

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

---

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstranění adresáře je přímočarý úkol. Nelze odstranit adresář, který stále obsahuje soubory nebo podadresáře.

# <a name="java-v12"></a>[Java V12](#tab/java)

Metoda [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) vyvolá výjimku, pokud adresář neexistuje nebo není prázdný. Vložte volání pro **odstranění** v `try/catch` bloku a zpracujte výjimku.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

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

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu souborů a adresářů ve sdílené složce Azure

# <a name="java-v12"></a>[Java V12](#tab/java)

Seznam souborů a adresářů získáte voláním [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). Metoda vrátí seznam objektů [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) , na kterých lze iterovat. Následující kód obsahuje seznam souborů a adresářů v adresáři určeném parametrem *dirname* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Získat seznam souborů a adresářů voláním **listFilesAndDirectories** na odkaz **CloudFileDirectory** . Metoda vrátí seznam objektů **ListFileItem** , na kterých lze iterovat. Následující kód obsahuje seznam souborů a adresářů v kořenovém adresáři.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Nahrání souboru

Přečtěte si, jak nahrát soubor z místního úložiště.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující kód nahraje místní soubor do služby Azure File Storage voláním metody [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) . Následující příklad metody vrátí hodnotu, `Boolean` která označuje, zda úspěšně nahrál zadaný soubor.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Získejte odkaz na adresář, do kterého se soubor nahraje, voláním metody **getRootDirectoryReference** v objektu Share.

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

---

## <a name="download-a-file"></a>Stažení souboru

Jednou z častých operací je stažení souborů ze služby Azure Files Storage.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující příklad stáhne zadaný soubor do místního adresáře zadaného v parametru *destDir* . Ukázková metoda umožňuje, aby stažený název souboru byl jedinečný pomocí předpřipraveného data a času.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Následující příklad stáhne SampleFile.txt a zobrazí jeho obsah.

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

---

## <a name="delete-a-file"></a>Odstranění souboru

Další běžnou operací se soubory Azure je odstraňování souborů.

# <a name="java-v12"></a>[Java V12](#tab/java)

Následující kód odstraní zadaný soubor. Nejprve příklad vytvoří [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) na základě parametru *dirname* . Potom kód získá [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) z klienta adresáře na základě parametru *filename* . Nakonec ukázková metoda volá [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) a soubor se odstraní.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Následující kód odstraní soubor s názvem SampleFile.txt uložený v adresáři s názvem **sampledir**.

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

---

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o dalších rozhraních API služby Azure Storage, postupujte podle těchto odkazů.

- [Azure pro vývojáře v Javě](/azure/developer/java)
- [Azure SDK pro Javu](https://github.com/azure/azure-sdk-for-java)
- [Sada Azure SDK pro Android](https://github.com/azure/azure-sdk-for-android)
- [Odkaz na klientskou knihovnu sdílení souborů Azure pro Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [REST API služby Azure Storage](/rest/api/storageservices/)
- [Blog týmu Azure Storage](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy-v10.md)
- [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
