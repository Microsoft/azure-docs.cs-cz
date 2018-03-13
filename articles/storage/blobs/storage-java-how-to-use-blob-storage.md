---
title: "Jak používat úložiště objektů Blob v Azure (úložiště objektů) z Javy | Microsoft Docs"
description: "Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů)."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 4b243aa7a4e9aa3028259258292d1271867d28f4
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="how-to-use-blob-storage-from-java"></a>Používání úložiště Blob z Javy
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Přehled
Úložiště objektů blob v Azure je služba, která ukládá nestrukturovaná data v cloudu jako objekty nebo objekty blob. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů.

Tento článek vám ukáže, jak provádět běžné scénáře s využitím Microsoft Azure Blob storage. Ukázky jsou napsané v jazyce Java a použít [sada SDK úložiště Azure pro jazyk Java][Azure Storage SDK for Java]. Pokryté scénáře zahrnují **odesílání**, **výpis**, **stahování**, a **odstraňování** objekty BLOB. Další informace o objekty BLOB, najdete v článku [další kroky](#Next-Steps) části.

> [!NOTE]
> Sada SDK je k dispozici pro vývojáře, kteří na zařízení se systémem Android používají Azure Storage. Další informace najdete v tématu [sada SDK úložiště Azure pro Android][Azure Storage SDK for Android].
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
V tomto článku budete používat funkce úložiště, které lze spustit v rámci aplikace Java místně nebo v kódu běžící v rámci webovou roli nebo role pracovního procesu v Azure.

To pokud chcete udělat, musíte nainstalovat Java Development Kit (JDK) a vytvoření účtu úložiště Azure ve vašem předplatném Azure. Jakmile provedete, budete muset ověřit, jestli váš vývojový systém splňuje minimální požadavky a závislosti, které jsou uvedeny v [sada SDK úložiště Azure pro jazyk Java] [ Azure Storage SDK for Java] úložišti na Githubu. Pokud váš systém splňuje tyto požadavky, můžete podle pokynů ke stažení a instalaci knihovny úložiště Azure pro jazyk Java systému z tohoto úložiště. Po dokončení těchto úloh, bude moci vytvořit aplikaci Java, která používá v příkladech v tomto článku.

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurace aplikace pro přístup k úložišti objektů Blob
Přidejte následující příkazy pro import do horní části souboru Java, ve které chcete používat rozhraní API úložiště Azure pro přístup k objektům BLOB.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavit připojovací řetězec službě Azure Storage
Klienta Azure Storage používá připojovací řetězec úložiště k ukládání koncových bodů a pověření pro přístup ke službám dat správy. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a primární přístupový klíč pro účet úložiště uvedené v [portál Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Následující příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Ve spuštěné aplikace v rámci role ve službě Microsoft Azure, můžete tento řetězec uložené v konfiguračním souboru služby *souboru ServiceConfiguration.cscfg*a je přístupný pomocí volání **RoleEnvironment.getConfigurationSettings** metoda. Následující příklad získá připojovací řetězec z **nastavení** element s názvem *StorageConnectionString* v konfiguračním souboru služby.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Následující ukázky předpokládejme, že používáte jednu z těchto dvou metod k získání připojovacího řetězce úložiště.

## <a name="create-a-container"></a>Vytvoření kontejneru
A **CloudBlobClient** objektu umožňuje získat odkaz na objekty pro kontejnery a objekty BLOB. Následující kód vytvoří **CloudBlobClient** objektu.

> [!NOTE]
> Chcete-li vytvořit další způsoby **CloudStorageAccount** objekty; Další informace najdete v tématu **CloudStorageAccount** v [odkaz SDK klienta úložiště Azure].
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Použít **CloudBlobClient** objekt, který chcete získat odkaz na kontejner, který chcete použít. Kontejner můžete vytvořit, pokud neexistuje s **createIfNotExists** metodu, která v opačném případě vrátí existující kontejner. Ve výchozím nastavení je nový kontejner privátní, proto přístupový klíč k úložišti musí určit (stejně jako dříve) ke stažení z tohoto kontejneru objektů BLOB.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>Volitelné: Konfigurace kontejner pro veřejný přístup
Kontejneru oprávnění jsou ve výchozím nastavení nakonfigurované pro soukromý přístup, ale můžete snadno konfigurovat kontejneru oprávnění pro povolení přístupu veřejné, jen pro čtení pro všechny uživatele v síti Internet:

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Chcete-li nahrát soubor do objektu blob, získejte odkaz na kontejner a umožňuje vám získat odkaz na objekt blob. Až budete mít odkaz na objekt blob, můžete nahrát jakýkoli proud voláním nahrávání na odkaz na objekt blob. Tato operace vytvoří objekt blob, pokud není neexistuje, nebo ho přepíše, pokud ji nemá. Následující příklad kódu ukazuje to a předpokládá, že kontejner byl vytvořen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
Pro zobrazení seznamu objektů BLOB v kontejneru, nejdřív získejte odkaz na kontejner stejně, jako jste nahrát objekt blob. Můžete použít metodu kontejneru **listBlobs** metoda s **pro** smyčky. Následující kód výstupy identifikátor Uri jednotlivých objektů blob v kontejneru ke konzole.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Všimněte si, že můžete pojmenovat objekty BLOB, informace o cestě jejich názvy. Tím se vytvoří struktura virtuálního adresáře, kterou můžete organizovat a měnit podle potřeby jako u tradičních systémů souborů. Všimněte si, že struktura adresářů je jenom virtuální - jediné prostředky, které jsou dostupné v úložišti objektů Blob,  jsou kontejnery a objekty blob. Však nabízí klientské knihovny **CloudBlobDirectory** objekt, který má odkazovat na virtuální adresář a zjednodušit tak práci s objekty BLOB, které jsou tímto způsobem uspořádány.

Například můžete mít kontejner s názvem "fotografie", ve kterých může odeslat objekty BLOB s názvem "rootphoto1", "2010 nebo photo1", "2010 nebo photo2" a "2011/photo1". Tím se vytvoří virtuální adresáře "2010" a "2011" v "fotografie" kontejneru. Při volání **listBlobs** na kontejneru "fotografie" vrátí kolekce, která bude obsahovat **CloudBlobDirectory** a **CloudBlob** objekty, které představují adresáře a objekty BLOB obsažené na nejvyšší úrovni. V takovém případě by byla vrácena adresáře "2010" a "2011", jakož i photo "rootphoto1". Můžete použít **instanceof** operátor k rozlišení tyto objekty.

Volitelně můžete předat parametry, které **listBlobs** metoda s **useFlatBlobListing** parametr nastaven na hodnotu true. Výsledkem bude každý objekt blob se vrátí, a to bez ohledu na adresář. Další informace najdete v tématu **CloudBlobContainer.listBlobs** v [odkaz SDK klienta úložiště Azure].

## <a name="download-a-blob"></a>Stažení objektu blob
Chcete-li stáhnout objekty BLOB, postupujte stejným způsobem, jako jste to udělali nahrát objekt blob, pokud chcete získat odkaz na objekt blob. V příkladu odesílání volá nahrávání na objekt blob. V následujícím příkladu volání stažení k přenosu obsahu objektu blob na objekt proudu, jako **FileOutputStream** , můžete použít k uchování objektu blob do místního souboru.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Odstranění objektu blob
Chcete-li odstranit objekt blob, získání odkaz na objekt blob a volání **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>Odstranit kontejner objektů blob
Nakonec pokud chcete odstranit kontejner objektů blob, získání objektu blob odkaz na kontejner a volání **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání Blob storage, postupujte podle následujících odkazech na další informace o složitějších úlohách úložiště.

* [Úložiště Azure SDK pro jazyk Java][Azure Storage SDK for Java]
* [Referenční informace sady SDK úložiště Azure klienta][odkaz SDK klienta úložiště Azure]
* [REST API pro Azure Storage][Azure Storage REST API]
* [Blog týmu Azure Storage][Azure Storage Team Blog]

Další informace najdete v tématu taky [Azure pro vývojáře v jazyce Java](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[odkaz SDK klienta úložiště Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
