---
title: Hromadná export a import registrací Azure Notification Hubs | Microsoft Docs
description: Naučte se používat hromadnou podporu Notification Hubs k provádění velkého počtu operací v centru oznámení nebo pro export všech registrací.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: c0771864229c8a3918da076de48fb6e033d2cf5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018174"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Hromadné export a import registrací Azure Notification Hubs

Existují scénáře, ve kterých je nutné vytvořit nebo upravit velký počet registrací v centru oznámení. Některé z těchto scénářů jsou aktualizace značky po výpočtech služby Batch nebo migrace stávající implementace nabízených oznámení na používání Azure Notification Hubs.

Tento článek vysvětluje, jak provést velký počet operací v centru oznámení nebo exportovat všechny registrace hromadně.

## <a name="high-level-flow"></a>Tok na nejvyšší úrovni

Podpora služby Batch je navržená tak, aby podporovala dlouhodobě běžící úlohy, které zahrnují miliony registrací. Pro dosažení tohoto škálování podpora dávky používá Azure Storage k ukládání podrobností a výstupu úlohy. Pro operace hromadné aktualizace musí uživatel vytvořit soubor v kontejneru objektů blob, jehož obsah je seznam operací aktualizace registrace. Při spuštění úlohy uživatel poskytne adresu URL vstupního objektu BLOB společně s adresou URL pro výstupní adresář (také v kontejneru objektů BLOB). Po zahájení úlohy může uživatel zjistit stav pomocí dotazu na umístění adresy URL zadané na začátku úlohy. Konkrétní úloha může provádět pouze operace určitého druhu (vytvoří, aktualizuje nebo odstraní). Operace exportu se provádějí obdobně.

## <a name="import"></a>Importovat

### <a name="set-up"></a>Nastavení

V této části se předpokládá, že máte následující entity:

- Zřízené centrum oznámení.
- Azure Storage kontejner objektů BLOB.
- Odkazuje na [balíček nuget Azure Storage](https://www.nuget.org/packages/windowsazure.storage/) a [Notification Hubs balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Vytvořit vstupní soubor a uložit ho do objektu BLOB

Vstupní soubor obsahuje seznam registrací serializovaných v XML, jeden pro každý řádek. Pomocí sady Azure SDK následující příklad kódu ukazuje, jak serializovat registrace a nahrát je do kontejneru objektů BLOB:

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Předchozí kód deserializace registrace v paměti a pak nahraje celý datový proud do objektu BLOB. Pokud jste nahráli soubor o více než několik megabajtů, přečtěte si téma pokyny k objektu blob Azure, jak provést tyto kroky. například [objekty blob bloku](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Vytváření tokenů URL

Po nahrání vstupního souboru vygenerujte adresy URL, které se poskytují do centra oznámení pro vstupní soubor i výstupní adresář. Pro vstup a výstup můžete použít dva různé kontejnery objektů BLOB.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Odeslání úlohy

Se dvěma vstupními a výstupními adresami URL teď můžete spustit dávkovou úlohu.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Kromě vstupních a výstupních adres URL tento příklad vytvoří `NotificationHubJob` objekt, který obsahuje `JobType` objekt, což může být jeden z následujících typů:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Až se volání dokončí, úloha bude pokračovat centrem oznámení a můžete zjistit jeho stav voláním [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Po dokončení úlohy můžete zkontrolovat výsledky zobrazením následujících souborů ve výstupním adresáři:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Tyto soubory obsahují seznam úspěšných a neúspěšných operací z dávky. Formát souboru je `.cvs` , ve kterém každý řádek obsahuje číslo řádku původního vstupního souboru a výstup operace (obvykle se jedná o vytvořený nebo aktualizovaný popis registrace).

### <a name="full-sample-code"></a>Úplný ukázkový kód

Následující vzorový kód importuje registrace do centra oznámení.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Export

Export registrace je podobný jako při importu, a to s těmito rozdíly:

- Potřebujete jenom výstupní adresu URL.
- Vytvoříte NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Ukázka fragmentu kódu

Následuje ukázka fragmentu kódu pro export registrací v jazyce Java:

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Další kroky

Další informace o registracích najdete v následujících článcích:

- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Značky pro registrace](notification-hubs-tags-segment-push-message.md)
- [Registrace šablon](notification-hubs-templates-cross-platform-push-messages.md)
