---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12 – Java'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro Java vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: dc8df24c1ad20aa3380009fe4c0abb29e31823e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021720"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Rychlý Start: Správa objektů BLOB pomocí sady Java V12 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Java. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Můžete nahrávat, stahovat a vypisovat objekty BLOB a vytvářet a odstraňovat kontejnery.

Další prostředky:

* [Referenční dokumentace k rozhraní API](/java/api/overview/azure/storage-blob-readme)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Balíček (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [ukázky](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou služby Azure Blob Storage V12 for Java.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Java s názvem *BLOB-Start-V12*.

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte Maven a vytvořte novou konzolovou aplikaci s názvem *objekt BLOB-rychlý Start-V12*. Zadáním následujícího příkazu **MVN** vytvořte "Hello World!" Projekt Java.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Výstup z generování projektu by měl vypadat přibližně takto:

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

1. V adresáři *objektů BLOB pro rychlý Start – V12* vytvořte další adresář s názvem *data*. V tomto umístění se vytvoří a uloží datové soubory objektů BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Přejděte do adresáře */Src/Main/Java/com/BLOBs/Quickstart*
1. Otevřete soubor *App. Java* v editoru.
1. Odstranění `System.out.println("Hello world!");` příkazu
1. Přidat `import` direktivy

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

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

K interakci s těmito prostředky použijte následující třídy Java:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB. Účet úložiště poskytuje obor názvů nejvyšší úrovně pro Blob service.
* [BlobServiceClientBuilder](/java/api/com.azure.storage.blob.blobserviceclientbuilder): `BlobServiceClientBuilder` Třída poskytuje rozhraní API pro tvůrce Fluent, které pomáhá pomoci při konfiguraci a vytváření instancí `BlobServiceClient` objektů.
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): `BlobContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/java/api/com.azure.storage.blob.blobclient): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.
* [BlobItem](/java/api/com.azure.storage.blob.models.blobitem): `BlobItem` Třída reprezentuje jednotlivé objekty blob vrácené voláním [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs).

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou služby Azure Blob Storage pro jazyk Java:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `Main` metody:

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

Určete název nového kontejneru. Následující kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dále vytvořte instanci třídy [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) a potom zavolejte metodu [Create](/java/api/com.azure.storage.blob.blobcontainerclient.create) , která ve skutečnosti vytvoří kontejner v účtu úložiště.

Přidejte tento kód na konec `Main` metody:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním *datovém* adresáři.
1. Získá odkaz na objekt [BlobClient](/java/api/com.azure.storage.blob.blobclient) voláním metody [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) na kontejneru z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje místní textový soubor do objektu BLOB voláním metody [uploadFromFile](/java/api/com.azure.storage.blob.blobclient.uploadfromfile) . Tato metoda vytvoří objekt blob, pokud ještě neexistuje, ale nepřepíše jej, pokud k tomu dojde.

Přidejte tento kód na konec `Main` metody:

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

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru zavoláním metody [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec `Main` metody:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [downloadToFile](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile) . Vzorový kód přidá příponu "DOWNLOAD" do názvu souboru, aby bylo možné zobrazit oba soubory v místním systému souborů.

Přidejte tento kód na konec `Main` metody:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odebráním celého kontejneru pomocí metody [Delete](/java/api/com.azure.storage.blob.blobcontainerclient.delete) . Odstraní také místní soubory vytvořené aplikací.

Aplikace pozastaví vstup uživatele voláním `System.console().readLine()` před odstraněním objektu blob, kontejneru a místních souborů. Tato možnost je vhodná pro ověření, že se prostředky správně vytvořily, než se odstraní.

Přidejte tento kód na konec `Main` metody:

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

Tato aplikace vytvoří testovací soubor v místní složce a nahraje ho do úložiště objektů BLOB. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře obsahujícího soubor *pom.xml* a zkompilujte projekt pomocí následujícího `mvn` příkazu.

```console
mvn compile
```

Pak Sestavte balíček.

```console
mvn package
```

Spusťte následující `mvn` příkaz, který aplikaci spustí.

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

Než zahájíte proces vyčištění, vyhledejte tyto dva soubory ve složce *dat* . Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů stiskněte klávesu **ENTER** , aby se odstranily soubory testu, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí Java.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky V12 Java sady SDK pro Azure Blob Storage](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Další informace najdete v tématu [sada Azure SDK pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Výukové programy, ukázky, rychlé starty a další dokumentace najdete na webu [Azure pro vývojáře v jazyce Java pro Cloud](/azure/developer/java/).