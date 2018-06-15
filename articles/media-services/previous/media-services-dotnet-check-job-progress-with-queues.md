---
title: Sledujte oznámení úlohy Media Services pomocí rozhraní .NET pomocí Azure Queue storage | Microsoft Docs
description: Naučte se používat Azure Queue storage k monitorování oznámení úlohy Media Services. Ukázka kódu je napsána v jazyce C# a pomocí sady Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 5b0e3155023cb8ac4d359e440b561ae5c61a9195
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788659"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Pomocí Azure Queue storage monitorovat oznámení úlohy Media Services pomocí rozhraní .NET
Při spuštění úlohy kódování, často vyžadují způsob, jak sledovat průběh úlohy. Můžete nakonfigurovat Media Services pro doručování oznámení [Azure Queue storage](../../storage/storage-dotnet-how-to-use-queues.md). Průběh úlohy můžete sledovat získáním oznámení z Queue storage. 

Doručování zpráv do fronty úložiště přístupná odkudkoli na světě. Zasílání zpráv architektura fronty úložiště je spolehlivé a vysoce škálovatelná. Dotazování Queue storage pro zprávy, se doporučuje namísto použití jiných metod.

Jeden běžný scénář pro naslouchání oznámení Media Services je, že pokud vyvíjíte systém správy obsahu, který potřebuje provést některé další úlohy po dokončení kódování úlohy (například k aktivaci na další krok v pracovním postupu nebo publikovat obsah).

Tento článek ukazuje, jak získat oznamující zprávy z fronty úložiště.  

## <a name="considerations"></a>Požadavky
Při vývoji aplikací Media Services, které používají fronty úložiště, zvažte následující:

* Fronty úložiště neposkytuje zárukou toho first-in-first-out (FIFO) seřazené doručení. Další informace najdete v tématu [fronty Azure a Azure Service Bus fronty porovnání a Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* Fronty úložiště není nabízecí služby. Budete muset dotazovat fronty.
* Může mít libovolný počet front. Další informace najdete v tématu [rozhraní API REST služby fronty](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API).
* Fronty úložiště má určitá omezení a specifika znát. Tyto možnosti jsou popsány v [fronty Azure a Azure Service Bus fronty porovnání a Contrasted](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

## <a name="net-code-example"></a>Příklad kódu rozhraní .NET

Příklad kódu v této části provede následující akce:

1. Definuje **EncodingJobMessage** třídu, která se mapuje na formát zprávy oznámení. Kód deserializuje zprávy přijaté z fronty do objektů **EncodingJobMessage** typu.
2. Načte informace účtu Media Services a úložiště ze souboru app.config. Příklad kódu používá tyto informace k vytvoření **CloudMediaContext** a **CloudQueue** objekty.
3. Vytvoří frontu, která přijímá zprávy s oznámením o úlohy kódování.
4. Vytvoří koncový bod oznámení, který je namapovaný do fronty.
5. Připojí koncového bodu oznámení do úlohy a předá úlohy kódování. Může mít několik koncových bodů oznámení připojen k úloze.
6. Předává **NotificationJobState.FinalStatesOnly** k **AddNew** metoda. (V tomto příkladu jsme jsou jenom zájem o poslední stavy zpracování úloh).

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Pokud předáte **NotificationJobState.All**, získáte všechny následující oznámení změny stavu: zařazených do fronty, naplánované, zpracování a dokončení. Ale jak bylo uvedeno dříve, Queue storage nezaručuje doručení. Pořadí zprávy, použijte **časové razítko** vlastnosti (definovaná v **EncodingJobMessage** typu v následujícím příkladu). Je možné, duplicitní zprávy. Kontrola duplikátů **vlastnost ETag** (definované na **EncodingJobMessage** typ). Je také možné, že některé oznámení změny stavu získat přeskočit.
8. Čeká na získání kontrolou fronty každých 10 sekund do stavu dokončení úlohy. Odstraní zprávy po jejich zpracování.
9. Odstraní frontu a koncového bodu oznámení.

> [!NOTE]
> Doporučeným způsobem, jak sledovat stav úlohy je prostřednictvím naslouchání zpráv s oznámením, jak je znázorněno v následujícím příkladu:
>
> Alternativně můžete zkontrolovat stav úlohy s použitím **IJob.State** vlastnost.  Oznámení o dokončení úlohy může před stav doručení do **IJob** je nastaven na **dokončeno**. **IJob.State** vlastnost odráží přesného stavu se ke krátké prodlevě.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Vytvořte novou složku (složka může být kdekoli na místní disk) a zkopírujte soubor MP4, který chcete kódovat a Streamovat nebo progresivně stahovat. V tomto příkladu se používá cestu "C:\Media".
3. Přidat odkaz na **System.Runtime.Serialization** knihovny.

### <a name="code"></a>Kód

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

V předchozím příkladu vytváří následující výstup: vaše hodnoty se budou lišit.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
