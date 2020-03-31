---
title: Změna cesty k objektu blob od výchozího
description: Přečtěte si, jak nastavit funkci Azure pro přejmenování cesty k objektům blob.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270634"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Změna cesty k objektu blob z výchozí cesty

Když služba StorSimple Data Manager transformuje data, ve výchozím nastavení umístí transformované objekty BLOB do kontejneru úložiště, jak je určeno při vytváření cílového úložiště. Jako objekty BLOB dorazí do tohoto umístění, můžete chtít přesunout tyto objekty BLOB do alternativního umístění. Tento článek popisuje, jak nastavit funkci Azure přejmenovat výchozí cestu k souboru objektu blob a proto přesunout objekty BLOB do jiného umístění.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte správně nakonfigurovanou definici úlohy ve službě StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Chcete-li vytvořit funkci Azure, proveďte následující kroky:

1. Přejděte na [portál Azure](https://portal.azure.com/).

2. Klepněte na tlačítko **+ Vytvořit zdroj**. Do pole **Hledat** zadejte **Funkci aplikace** a stiskněte **Enter**. V seznamu zobrazených aplikací vyberte a klikněte na **Funkční aplikace.**

    ![Do vyhledávacího pole zadejte "Aplikace funkcí".](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klikněte na **Vytvořit**.

    ![Tlačítko "Vytvořit" okno Aplikace funkce](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. V okně konfigurace **aplikace function** app proveďte následující kroky:

    1. Zadejte jedinečný **název aplikace**.
    2. V rozevíracím seznamu vyberte **předplatné**. Toto předplatné by mělo být stejné jako předplatné přidružené ke službě StorSimple Data Manager.
    3. Vyberte **Vytvořit novou** skupinu prostředků.
    4. V rozevíracím seznamu **Hosting Plan** vyberte možnost **Plán spotřeby**.
    5. Zadejte umístění, kde je spuštěna vaše funkce. Chcete stejnou oblast, kde se nachází služba StorSimple Data Manager a účet úložiště přidružený k definici úlohy.
    6. Vyberte stávající účet úložiště nebo vytvořte nový. Účet úložiště se používá interně pro funkci.

        ![Zadání nových konfiguračních dat aplikace function App](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klikněte na **Vytvořit**. Aplikace funkce je vytvořena.
     
        ![Aplikace funkce vytvořena](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Vyberte **funkce**a klepněte na tlačítko **+ Nová funkce**.

    ![Klikněte na + Nová funkce](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Vyberte **C#** pro jazyk. V poli dlaždic šablony vyberte **C#** na dlaždici **QueueTrigger-CSharp.**

7. V **aktivační události Fronta**:

    1. Zadejte **název** funkce.
    2. Do pole **Název fronty** zadejte název definice úlohy transformace dat.
    3. V části **Připojení účtu úložiště**klikněte na **nový**. Ze seznamu účtů úložiště vyberte účet přidružený k definici úlohy. Poznamenejte si název připojení (zvýrazněný). Název je vyžadován později ve funkci Azure.

        ![Vytvoření nové funkce Jazyka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klikněte na **Vytvořit**. **Funkce** je vytvořena.

     
10. V okně Funkce spusťte soubor _.csx._

    ![Vytvoření nové funkce Jazyka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Nahraďte **STORAGE_CONNECTIONNAME** na lince 11 připojením účtu úložiště (viz krok 7c).

        ![Kopírovat název připojení úložiště](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Uložte** funkci.

        ![Uložit funkci](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Chcete-li funkci dokončit, přidejte ještě jeden soubor následujícím postupem:

    1. Klepněte na **tlačítko Zobrazit soubory**.

       ![Odkaz Zobrazit soubory](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klikněte na tlačítko **+ Přidat**.
        
        ![Odkaz Zobrazit soubory](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Zadejte **příkaz project.json**a stiskněte **klávesu Enter**. Do souboru **project.json** vložte následující kód:

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

        ![Odkaz Zobrazit soubory](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Vytvořili jste funkci Azure. Tato funkce se aktivuje pokaždé, když je vygenerován nový objekt blob úlohou transformace dat.

## <a name="next-steps"></a>Další kroky

[Použití ui Správce dat StorSimple k transformaci dat](storsimple-data-manager-ui.md)
