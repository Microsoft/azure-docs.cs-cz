---
title: Export a import registrace Azure Notification Hubs hromadné | Dokumentace Microsoftu
description: Zjistěte, jak používat Notification Hubs hromadné podporu k provedení velký počet operací v centru oznámení nebo chcete-li exportovat všechny registrace.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: d7e38e8eca58c06fc6896887522b320a797fc42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575297"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Export a import hromadnou registrací Azure Notification Hubs
Existují scénáře, ve kterých je potřeba vytvořit nebo upravit velký počet registrací v centru oznámení. Některé z těchto scénářů jsou značky aktualizace následujících batch výpočtů nebo migrujete stávající implementaci nabízených oznámení používat Notification Hubs.

Tento článek vysvětluje, jak provádět velký počet operací v centru oznámení nebo chcete-li exportovat všechny registrace hromadně.

## <a name="high-level-flow"></a>Základní tok
Podpora služby batch je navržen pro podporu dlouhotrvajících úloh zahrnující miliony registrací. K dosažení této stupnice, podpory služby batch používá Azure Storage k ukládání podrobností o úloze a výstup. Pro hromadné operace aktualizace je potřeba k vytvoření souboru v kontejneru objektů blob, jehož obsah je seznam registrace – aktualizovat operace uživatele. Při spuštění úlohy, uživatel zadá adresu URL k vstupnímu objektu blob, spolu s adresu URL do výstupního adresáře (také v kontejneru objektů blob). Po spuštění úlohy, uživatel stav můžete zkontrolovat pomocí dotazu na umístění adresy URL poskytnuté na spuštění úlohy. Určité úlohy můžete provádět operace pouze konkrétním druhu (vytvoří, aktualizuje nebo odstraní). Operace exportu provádějí analogicky.

## <a name="import"></a>Import

### <a name="set-up"></a>Nastavení
V této části se předpokládá, že máte následující entity:

- Centrum oznámení zřízené.
- Kontejner objektů blob služby Azure Storage.
- Odkazy [balíčku NuGet pro Azure Storage](https://www.nuget.org/packages/windowsazure.storage/) a [balíčku Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Vytvoření vstupní soubor a uložte ho do objektu BLOB
Vstupní soubor obsahuje seznam registrací serializovanou ve formátu XML, jeden pro každý řádek. Pomocí sady Azure SDK, následující příklad kódu ukazuje, jak k serializaci registrace a nahrání do kontejneru objektů blob.

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
> Předchozí kód serializuje registrace v paměti a poté odešle celého datového proudu do objektu blob. Pokud jste nahráli soubor více než pár v megabajtech, přečtěte si pokyny objektů blob v Azure o tom, jak provést tyto kroky; například [objekty BLOB bloku](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Vytvořit adresu URL tokeny
Po nahrání vstupního souboru generování adres URL pro zadání souboru vstupní a výstupní adresář pro své Centrum oznámení. Dva kontejnery různých objektů blob můžete použít pro vstup a výstup.

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
Dva vstupní a výstupní adresy URL můžete nyní spustit úlohy služby batch.

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

Kromě vstupní a výstupní adresy URL v tomto příkladu vytvoří `NotificationHubJob` objekt, který obsahuje `JobType` objektu, který může být jedna z následujících typů:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Po dokončení volání, úloha navázat tak, že Centrum oznámení a můžete zkontrolovat její stav pomocí volání do [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Po dokončení úlohy si můžete prohlédnout výsledky podle následující soubory v adresáři výstupu:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Tyto soubory obsahují seznam úspěšných a neúspěšných operací ze služby batch. Formát souboru je `.cvs`, ve které každý řádek obsahuje číslo řádku z původního vstupního souboru a výstup operace (obvykle popis vytvořené nebo aktualizované registrace).

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
Export registrace je podobný import s těmito rozdíly:

- Stačí výstupní adresy URL.
- Můžete vytvořit NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Ukázka fragmentu kódu
Tady je ukázka fragmentu kódu pro export registrace v jazyce Java:

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

## <a name="next-steps"></a>Další postup
Další informace o registraci, najdete v následujících článcích:

- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Značky pro registrace](notification-hubs-tags-segment-push-message.md)
- [Registrace šablon](notification-hubs-templates-cross-platform-push-messages.md)
