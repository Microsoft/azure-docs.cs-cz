---
title: Export a import registrací center oznámení Azure hromadně | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí hromadné podpory centra oznámení provádět velký počet operací v centru oznámení nebo exportovat všechny registrace.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212399"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Export a import registrací centra oznámení Azure hromadně
Existují scénáře, ve kterých je nutné vytvořit nebo upravit velký počet registrací v centru oznámení. Některé z těchto scénářů jsou aktualizace značek po dávkových výpočtech nebo migrace existující nabízené implementace pro použití center oznámení.

Tento článek vysvětluje, jak provést velký počet operací v centru oznámení nebo exportovat všechny registrace hromadně.

## <a name="high-level-flow"></a>Vysoký průtok
Dávková podpora je navržena tak, aby podporovala dlouhotrvající úlohy zahrnující miliony registrací. K dosažení tohoto měřítka dávková podpora používá Azure Storage k ukládání podrobností o úlohách a výstupu. U operací hromadné aktualizace je uživatel povinen vytvořit soubor v kontejneru objektů blob, jehož obsah je seznam operací aktualizace registrace. Při spuštění úlohy uživatel poskytne adresu URL vstupního objektu blob spolu s adresou URL do výstupního adresáře (také v kontejneru objektů blob). Po spuštění úlohy může uživatel zkontrolovat stav dotazem na umístění adresy URL poskytnuté při spuštění úlohy. Určitá úloha může provádět pouze operace určitého druhu (vytvoří, aktualizuje nebo odstraní). Exportní operace jsou prováděny analogicky.

## <a name="import"></a>Import

### <a name="set-up"></a>Nastavit
Tato část předpokládá, že máte následující entity:

- Zřízené centrum oznámení.
- Kontejner objektů blob úložiště Azure.
- Odkazy na [balíček Azure Storage NuGet](https://www.nuget.org/packages/windowsazure.storage/) a [balíček NuGet oznamovacích center](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Vytvoření vstupního souboru a jeho uložení do objektu blob
Vstupní soubor obsahuje seznam registrací serializovaných v jazyce XML, jeden na řádek. Pomocí sady Azure SDK následující příklad kódu ukazuje, jak serializovat registrace a nahrát je do kontejneru objektů blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Předchozí kód serializuje registrace v paměti a potom nahraje celý datový proud do objektu blob. Pokud jste nahráli soubor více než jen několik megabajtů, přečtěte si pokyny k objektu blob Azure o tom, jak provést tyto kroky; například [objekty BLOB bloku](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Vytvoření tokenů url
Po nahrání vstupního souboru vygenerujte adresy URL, které vám poskytnou centrum oznámení pro vstupní i výstupní adresář. Pro vstup a výstup můžete použít dva různé kontejnery objektů blob.

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
Pomocí dvou vstupních a výstupních adres URL můžete nyní spustit dávkovou úlohu.

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

Kromě vstupních a výstupních adres URL tento `NotificationHubJob` příklad vytvoří `JobType` objekt, který obsahuje objekt, což může být jeden z následujících typů:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Po dokončení volání úlohy pokračuje centrum oznámení a můžete zkontrolovat jeho stav s voláním [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Po dokončení úlohy můžete zkontrolovat výsledky pohledem na následující soubory ve výstupním adresáři:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Tyto soubory obsahují seznam úspěšných a neúspěšných operací z dávky. Formát souboru `.cvs`je , ve kterém každý řádek má číslo řádku původního vstupního souboru a výstup operace (obvykle vytvořený nebo aktualizovaný popis registrace).

### <a name="full-sample-code"></a>Úplný ukázkový kód
Následující ukázkový kód importuje registrace do centra oznámení.

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

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
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
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
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
Export registrace je podobný importu, s následujícími rozdíly:

- Potřebujete pouze výstupní adresu URL.
- Vytvoření NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Ukázkový fragment kódu
Zde je ukázkový fragment kódu pro export registrací v jazyce Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Další kroky
Další informace o registracích najdete v následujících článcích:

- [Správa registrace](notification-hubs-push-notification-registration-management.md)
- [Značky pro registrace](notification-hubs-tags-segment-push-message.md)
- [Registrace šablon](notification-hubs-templates-cross-platform-push-messages.md)
