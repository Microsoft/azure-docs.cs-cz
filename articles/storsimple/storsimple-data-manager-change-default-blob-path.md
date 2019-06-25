---
title: Změnit cestu k objektu blob z výchozího | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit funkci Azure přejmenovat cestu k souboru objektu blob
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
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723662"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Změňte cestu k objektu blob z výchozí cestu

Když služba StorSimple Data Manager transformuje data, ve výchozím nastavení je umístí transformovaných objektech BLOB v kontejneru úložiště uvedený během vytváření cílového úložiště. Při doručování na tomto místě: objekty BLOB, můžete přesunout tyto objekty BLOB do alternativního umístění. Tento článek popisuje, jak vytvořit funkci Azure pro přejmenování výchozí cestu k souboru objektu blob a objekty BLOB proto přesunout do jiného umístění.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte definici úlohy správně nakonfigurovaná ve službě StorSimple Data Manageru.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Chcete-li vytvořit funkci Azure, postupujte následovně:

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Klikněte na tlačítko **+ vytvořit prostředek**. V **hledání** zadejte **aplikace Function App** a stiskněte klávesu **Enter**. Vyberte a klikněte na tlačítko **aplikace Function app** v seznamu zobrazené aplikace.

    ![Do vyhledávacího pole zadejte "Aplikace Function App"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klikněte na možnost **Vytvořit**.

    ![Tlačítko "Vytvořit" okno aplikace Function App](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na **aplikace Function App** okno konfigurace, proveďte následující kroky:

    1. Zadejte jedinečnou **název aplikace**.
    2. Z rozevíracího seznamu, vyberte **předplatné**. Toto předplatné by měla být stejná jako ta související s vaší službou StorSimple Data Manageru.
    3. Vyberte **vytvořit nový** skupinu prostředků.
    4. Pro **plánu hostování** rozevíracího seznamu vyberte **plánu Consumption**.
    5. Zadejte umístění, kde běží vaše funkce. Chcete, aby stejné oblasti, kde se nachází ve službě Správce dat StorSimple a účet úložiště přidružený k definici úlohy.
    6. Vyberte stávající účet úložiště nebo vytvořte nový. Účet úložiště se používá interně pro funkci.

        ![Zadejte novou aplikaci Function App konfigurační data](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klikněte na možnost **Vytvořit**. Aplikace function app se vytvoří.
     
        ![Vytvoření aplikace Function App](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Vyberte **funkce**a klikněte na tlačítko **+ nová funkce**.

    ![Klikněte na + nová funkce](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Vyberte **C#** pro jazyk. V poli šablony dlaždice, vyberte **C#** v **QueueTrigger-CSharp** dlaždici.

7. V **aktivační událost fronty**:

    1. Zadejte **název** funkce.
    2. V **název fronty** zadejte název definice úlohy transformace vaše data.
    3. V části **připojení k účtu úložiště**, klikněte na tlačítko **nové**. V seznamu účtů úložiště vyberte účet spojený s vaší definice úlohy. Poznamenejte si název připojení (zvýrazněno). Název je vyžadováno později v funkce Azure functions.

        ![Vytvořte nový C# – funkce](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klikněte na možnost **Vytvořit**. **Funkce** se vytvoří.

     
10. V okně funkce Spustit _.csx_ souboru.

    ![Vytvořte nový C# – funkce](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Nahraďte **STORAGE_CONNECTIONNAME** na řádku 11 s připojením k účtu úložiště (viz část o krok 7 c).

        ![Zkopírujte název připojení úložiště](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Uložit** funkce.

        ![Uložit – funkce](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. K dokončení funkci, přidejte jeden další soubor provedením následujících kroků:

    1. Klikněte na tlačítko **zobrazit soubory**.

       ![Odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klikněte na tlačítko **+ Přidat**.
        
        ![Odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.json**a potom stiskněte klávesu **Enter**. V **project.json** soubor, vložte následující kód:

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

        ![Odkaz "Zobrazit soubory"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Vytvořili jste funkci Azure. Tato funkce se spustí pokaždé, když úloha transformace dat vygeneruje nový objekt blob.

## <a name="next-steps"></a>Další postup

[Použití StorSimple Data Manager uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md)
