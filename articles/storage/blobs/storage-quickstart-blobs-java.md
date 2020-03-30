---
title: 'Úvodní příručka: Knihovna úložiště objektů Blob Azure v12 – Java'
description: V tomto rychlém startu se dozvíte, jak pomocí knihovny klienta úložiště objektů Blob Azure verze 12 pro Javu vytvořit kontejner a objekt blob v úložišti objektů blob (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c883653754e1f69d3b2d79b256d57a036c70b58e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330131"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Úvodní příručka: Správa objektů BLOB pomocí sady Java v12 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí jazyka Java. Objekty BLOB jsou objekty, které mohou obsahovat velké množství textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a archivních dat. Nahrajete, stáhnete a zobrazíte objekty BLOB a vytvoříte a odstraníte kontejnery.

[Referenční dokumentace](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | rozhraní[Knihovna zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [Balíček (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Pokud chcete začít s předchozí verzí sady SDK, přečtěte si [úvodní příručku: Správa objektů BLOB pomocí sady Java v8 SDK](storage-quickstart-blobs-java-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště objektů Blob Azure v12 pro Jazyk Java.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Java s názvem *blob-quickstart-v12*.

1. V okně konzoly (například cmd, PowerShell nebo Bash) použijte maven k vytvoření nové konzolové aplikace s názvem *blob-quickstart-v12*. Zadejte následující příkaz **mvn** a vytvořte "Hello world!" Projekt Java.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Výstup z generování projektu by měl vypadat nějak takto:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Na straně *adresáře blob-quickstart-v12* vytvořte jiný adresář s názvem *data*. Toto je místo, kde budou vytvořeny a uloženy datové soubory objektů blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete soubor *pom.xml* v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Přejděte do adresáře */src/main/java/com/blobs/quickstart*
1. Otevření souboru *App.java* v editoru
1. Odstranit `System.out.println("Hello world!");` příkaz
1. Přidání `import` směrnic

Zde je kód:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů blob Azure je optimalizované pro ukládání obrovského množství nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů blob nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů blob](./media/storage-blobs-introduction/blob1.png)

K interakci s těmito prostředky použijte následující třídy Javy:

* [BlobServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html) `BlobServiceClient` Třída umožňuje manipulovat s prostředky úložiště Azure a kontejnery objektů blob. Účet úložiště poskytuje obor názvů nejvyšší úrovně pro službu objektů Blob.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): `BlobServiceClientBuilder` Třída poskytuje rozhraní API pro plynulé tvůrce, `BlobServiceClient` které pomáhá při konfiguraci a vytváření instancí objektů.
* [BlobContainerClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) `BlobContainerClient` Třída umožňuje manipulovat s kontejnery úložiště Azure a jejich objekty BLOB.
* [Objekt BlobClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html)Třída `BlobClient` umožňuje manipulovat s objekty BLOB úložiště Azure.
* [Objekt BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): Třída `BlobItem` představuje jednotlivé objekty `listBlobsFlat`BLOB vrácené z volání .

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce v klientské knihovně úložiště Azure Blob pro Jazyk Java:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
* [Zobrazí seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `Main` kód uvnitř metody:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Rozhodněte o názvu nového kontejneru. Níže uvedený kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dále vytvořte instanci třídy [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) a pak zavolejte metodu [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) a skutečně vytvořte kontejner ve vašem účtu úložiště.

Přidejte tento kód na `Main` konec metody:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním *datovém* adresáři.
1. Získá odkaz na objekt [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) voláním metody [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) v kontejneru z [oddílu Vytvořit kontejner.](#create-a-container)
1. Odešle místní textový soubor do objektu blob voláním metody [uploadFromFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) Tato metoda vytvoří objekt blob, pokud ještě neexistuje, ale nebude přepsat, pokud ano.

Přidejte tento kód na `Main` konec metody:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru voláním [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) metoda. V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt blob.

Přidejte tento kód na `Main` konec metody:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte si dříve vytvořený objekt blob voláním metody [downloadToFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) Ukázkový kód přidá k názvu souboru příponu "DOWNLOAD", takže oba soubory můžete zobrazit v místním systému souborů.

Přidejte tento kód na `Main` konec metody:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila odebráním celého kontejneru pomocí metody [delete.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) Odstraní také místní soubory vytvořené aplikací.

Aplikace pozastaví vstup uživatele `System.console().readLine()` voláním před odstraněním objektu blob, kontejneru a místních souborů. Toto je dobrá šance ověřit, že prostředky byly vytvořeny správně, před jejich odstraněním.

Přidejte tento kód na `Main` konec metody:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor v místní složce a nahraje jej do úložiště objektů Blob. V příkladu jsou uvedeny objekty BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře obsahujícího soubor *pom.xml* a `mvn` zkompilujte projekt pomocí následujícího příkazu.

```console
mvn compile
```

Potom vytvořte balíček.

```console
mvn package
```

Spusťte `mvn` následující příkaz pro spuštění aplikace.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Než začnete proces čištění, zkontrolujte, zda ve složce *dat* neobsahuje dva soubory. Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů odstraňte testovací soubory stisknutím klávesy **Enter** a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili nahrávat, stahovat a seznamovat objekty BLOB pomocí jazyka Java.

Pokud chcete zobrazit ukázkové aplikace úložiště objektů Blob, pokračujte v:

> [!div class="nextstepaction"]
> [Ukázky Azure Blob storage SDK v12 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Další informace najdete v [tématu Azure SDK pro Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Kurzy, ukázky, rychlé starty a další dokumentaci najdete na webu [vývojářů cloudu Azure for Java](/azure/java/).
