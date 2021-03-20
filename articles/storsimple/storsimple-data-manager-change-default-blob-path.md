---
title: Změnit cestu objektu BLOB z výchozího nastavení
description: Naučte se, jak nastavit funkci Azure pro přejmenování výchozí cesty k souboru BLOB a přesunout objekty blob do jiného umístění.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011434"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Změna výchozí cesty k objektu blob

Když Služba StorSimple Data Manager transformuje data, ve výchozím nastavení umístí transformované objekty blob do kontejneru úložiště, jak je zadané během vytváření cílového úložiště. Vzhledem k tomu, že objekty blob přicházejí do tohoto umístění, možná budete chtít přesunout tyto objekty blob do alternativního umístění. Tento článek popisuje, jak nastavit funkci Azure pro přejmenování výchozí cesty k souboru BLOB, a proto přesunout objekty blob na jiné místo.

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že máte ve službě StorSimple Data Manager správnou nakonfigurovanou definici úlohy.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Pokud chcete vytvořit funkci Azure Functions, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Klikněte na **+ Vytvořit prostředek**. Do **vyhledávacího** pole zadejte **Function App** a stiskněte klávesu **ENTER**. V zobrazeném seznamu aplikací vyberte a klikněte na **aplikace Function App** .

    ![Do vyhledávacího pole zadejte "Function App"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klikněte na **Vytvořit**.

    ![Tlačítko pro Function App okno pro vytvoření](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. V okně konfigurace **Function App** proveďte následující kroky:

    1. Zadejte jedinečný **název aplikace**.
    2. V rozevíracím seznamu vyberte **předplatné**. Tento odběr by měl být stejný jako ten, který je přidružený ke službě StorSimple Data Manager.
    3. Vyberte **vytvořit novou** skupinu prostředků.
    4. V rozevíracím seznamu **plán hostování** vyberte **plán spotřeby**.
    5. Zadejte umístění, ve kterém je funkce spuštěná. Chcete mít stejnou oblast, kde se nachází Služba StorSimple Data Manager a účet úložiště přidružené k definici úlohy.
    6. Vyberte stávající účet úložiště nebo vytvořte nový. Účet úložiště se interně používá pro funkci.

        ![Zadejte nová konfigurační data Function App](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klikněte na **Vytvořit**. Aplikace Function App se vytvoří.
     
        ![Function App vytvořena](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Vyberte **funkce** a klikněte na **+ Nová funkce**.

    ![Kliknout na + nová funkce](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Pro jazyk vyberte **C#** . V poli dlaždic šablon vyberte v dlaždici **QueueTrigger-CSharp** možnost **C#** .

7. **Aktivační událost fronty**:

    1. Zadejte **název** funkce.
    2. Do pole **název fronty** zadejte název definice úlohy transformace dat.
    3. V části **připojení k účtu úložiště** klikněte na **Nový**. V seznamu účtů úložiště vyberte účet přidružený k definici úlohy. Poznamenejte si název připojení (zvýrazněný). Název je požadován později ve funkci Azure Function.

        ![Vytvoření nové funkce jazyka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klikněte na **Vytvořit**. **Funkce** je vytvořena.

     
10. V okně funkce spusťte soubor _. csx_ .

    ![Vytvoření nové funkce jazyka C# 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Proveďte následující kroky.

    1. Vložte následující kód:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Položku **STORAGE_CONNECTIONNAME** na řádku 11 nahraďte vaším připojením k účtu úložiště (viz krok 7c).

        ![Kopírovat název připojení úložiště](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Uložte** funkci.

        ![Save – funkce](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. K dokončení této funkce přidejte další soubor pomocí následujících kroků:

    1. Klikněte na **Zobrazit soubory**.

       ![Odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klikněte na **+ Přidat**.
        
        ![Přidat novou funkci přidat soubor](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Zadejte **project.js** a potom stiskněte klávesu **ENTER**. Do **project.jsv** souboru vložte následující kód:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klikněte na **Uložit**.

        ![Nový projekt funkce – JSON](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Vytvořili jste funkci Azure Function. Tato funkce se aktivuje pokaždé, když úloha transformace dat vygeneruje nový objekt BLOB.

## <a name="next-steps"></a>Další kroky

[Použití uživatelského rozhraní StorSimple Data Manager k transformaci dat](storsimple-data-manager-ui.md)
