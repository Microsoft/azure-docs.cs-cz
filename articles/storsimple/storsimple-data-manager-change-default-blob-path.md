---
title: Změna cesty objektu blob z výchozího | Microsoft Docs
description: Zjistěte, jak nastavit Azure funkce přejmenování cestu k objektu blob souboru
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862396"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Změňte cestu k objektu blob z výchozí cestu

Pokud službu StorSimple Manager dat transformuje data, ve výchozím nastavení umístí transformovaných objektů BLOB v kontejneru úložiště jako zadaný během vytváření cílové úložiště. Při doručování: objekty BLOB v tomto umístění, můžete přesunout tyto objekty BLOB do alternativního umístění. Tento článek popisuje, jak nastavit Azure funkce přejmenujte výchozí cestu souboru blob a objekty BLOB proto přesunout do jiného umístění.

## <a name="prerequisites"></a>Požadavky

Je třeba splnit definici správně nakonfigurována úlohy ve službě StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Vytvoření Azure funkce

Pokud chcete vytvořit Azure funkce, proveďte následující kroky:

1. Přejděte na [portál Azure](http://portal.azure.com/).

2. Klikněte na tlačítko **+ vytvořit prostředek**. V **vyhledávání** zadejte **aplikaci funkce** a stiskněte klávesu **Enter**. Vyberte a klikněte na **aplikaci funkce** v seznamu aplikace zobrazí.

    ![Do vyhledávacího pole zadejte "Funkce aplikace"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klikněte na možnost **Vytvořit**.

    ![Tlačítko "Vytvořit" okno aplikace – funkce](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na **aplikaci funkce** okno konfiguraci, proveďte následující kroky:

    1. Zadejte jedinečný **název aplikace**.
    2. Z rozevíracího seznamu vyberte **předplatné**. Tento odběr musí být stejná jako ta, přidružené služby StorSimple Data Manager.
    3. Vyberte **vytvořit nový** skupinu prostředků.
    4. Pro **hostování plánování** rozevíracího seznamu vyberte **plánování spotřeba**.
    5. Zadejte umístění, kde je spuštěna funkce. Chcete stejné oblasti, kde jsou umístěny služby StorSimple Manager dat a účtu úložiště přidruženého k definici úlohy.
    6. Vyberte stávající účet úložiště nebo vytvořte nový. Účet úložiště se používá interně pro funkci.

        ![Zadejte novou aplikaci funkce konfigurační data](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klikněte na možnost **Vytvořit**. Vytvoření funkce aplikace.
     
        ![Vytvořit aplikaci funkce](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Vyberte **funkce**a klikněte na tlačítko **+ nové funkce**.

    ![Klikněte na + nové funkce](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Vyberte **C#** pro jazyk. V poli dlaždice šablony, vyberte **C#** v **QueueTrigger CSharp** dlaždici.

7. V **aktivační událost fronty**:

    1. Zadejte **název** pro funkce.
    2. V **název fronty** zadejte název definice úlohy transformace vaše data.
    3. V části **připojení účtu úložiště**, klikněte na tlačítko **nové**. Ze seznamu účtů úložiště vyberte účet přidružený k vaší definice úlohy. Poznamenejte si název připojení (zvýrazněné). Později v Azure funkce je vyžadován název.

        ![Vytvořte novou funkci jazyka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klikněte na možnost **Vytvořit**. **Funkce** je vytvořena.

     
10. V okně funkce Spustit _.csx_ souboru.

    ![Vytvořte novou funkci jazyka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Nahraďte **STORAGE_CONNECTIONNAME** na řádek 11 s připojením k účtu úložiště (viz. krok 7 c).

        ![Název připojení kopie úložiště](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Uložit** funkce.

        ![Uložit – funkce](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. K dokončení funkci, přidejte jeden další soubor provedením následujících kroků:

    1. Klikněte na tlačítko **zobrazit soubory**.

       ![Na odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klikněte na tlačítko **+ přidat**.
        
        ![Na odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.json**a potom stiskněte klávesu **Enter**. V **project.json** souboru, vložte následující kód:

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

        ![Na odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Vytvořili jste Azure funkce. Tato funkce se aktivuje vždy, když nový objekt blob je generován úlohu transformace dat.

## <a name="next-steps"></a>Další postup

[Data Manager zařízení StorSimple pomocí uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md)
