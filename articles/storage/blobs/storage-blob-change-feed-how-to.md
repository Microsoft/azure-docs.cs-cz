---
title: Informační kanál změn procesu v azure blob storage (preview) | Dokumenty společnosti Microsoft
description: Zjistěte, jak zpracovat protokoly informačního kanálu o změnách v klientské aplikaci .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111865"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Informační kanál změn procesů ve službě Azure Blob Storage (preview)

Kanál změn poskytuje protokoly transakcí všech změn, ke kterým dochází k objektům BLOB a metadatům objektů blob v účtu úložiště. Tento článek ukazuje, jak číst záznamy kanálu změn pomocí knihovny procesoru kanálu blob změnit.

Další informace o kanálu změn najdete [v tématu Změna informačního kanálu v Azure Blob Storage (Preview).](storage-blob-change-feed.md)

> [!NOTE]
> Kanál změn je ve verzi Public Preview a je k dispozici v oblastech **westcentralus** a **westus2.** Další informace o této funkci spolu se známými problémy a omezeními najdete [v tématu Změna podpory informačního kanálu ve službě Azure Blob Storage](storage-blob-change-feed.md). Knihovna procesoru kanálu změn se může měnit mezi dneškem a tím, kdy bude tato knihovna obecně dostupná.

## <a name="get-the-blob-change-feed-processor-library"></a>Získání knihovny procesoru kanálu blob změnit kanál

1. V sadě Visual Studio `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` přidejte adresu URL do zdrojů balíčku NuGet. 

   Chcete-li se dozvědět, jak, viz [zdroje balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Ve Správci balíčků NuGet najděte balíček **Microsoft.Azure.Storage.Changefeed** a nainstalujte ho do projektu. 

   Informace o tom, jak najdete v [tématu Vyhledání a instalace balíčku](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Připojení k účtu úložiště

Analyzovat připojovací řetězec voláním [CloudStorageAccount.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) metoda. 

Potom vytvořte objekt, který představuje úložiště objektů blob ve vašem účtu úložiště voláním [metody CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inicializovat kanál změn

Přidejte následující příkazy pomocí horní části souboru kódu. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Potom vytvořte instanci třídy **ChangeFeed** voláním metody **GetContainerReference.** Předaj název kontejneru kanálu změn.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Čtení záznamů

> [!NOTE]
> Kanál změn je neměnná entita jen pro čtení ve vašem účtu úložiště. Libovolný počet aplikací může číst a zpracovávat kanál změn současně a nezávisle na vlastní pohodlí. Záznamy nejsou odebrány z kanálu změn při čtení aplikace. Stav čtení nebo iterace každého náročného čteče je nezávislý a udržuje pouze vaše aplikace.

Nejjednodušší způsob čtení záznamů je vytvořit instanci třídy **ChangeFeedReader.** 

Tento příklad itetuje všechny záznamy v kanálu změn a potom vytiskne do konzoly několik hodnot z každého záznamu. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Obnovení čtení záznamů z uložené pozice

Můžete si vybrat, zda chcete uložit pozici pro čtení ve zdroji změn a pokračovat v iterace záznamů v budoucnu. Stav iterace kanálu změn můžete kdykoli uložit pomocí metody **ChangeFeedReader.SerializeState().** Stav je **řetězec** a aplikace můžete uložit tento stav na základě návrhu aplikace (například: do databáze nebo souboru).

```csharp
    string currentReadState = processor.SerializeState();
```

Můžete pokračovat iterace prostřednictvím záznamů z posledního stavu vytvořením **ChangeFeedReader** pomocí **CreateChangeFeedReaderFromPointerAsync** metoda.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Zpracování datových proudů záznamů

Můžete se rozhodnout zpracovat záznamy informačního kanálu o změnách při jejich doručení. Viz [Specifikace](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Čtení záznamů v časovém rozsahu

Kanál změn je uspořádán do hodinových segmentů na základě času události změny. Viz [Specifikace](storage-blob-change-feed.md#specifications). Záznamy můžete číst ze segmentů kanálu změn, které spadají do určitého časového rozsahu.

Tento příklad získá počáteční časy všech segmentů. Poté iteruje tento seznam, dokud počáteční čas nepřekročí čas posledního segmentu spotřebního materiálu nebo za koncový čas požadovaného rozsahu. 

### <a name="selecting-segments-for-a-time-range"></a>Výběr segmentů pro časový rozsah

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Čtení záznamů v segmentu

Můžete číst záznamy z jednotlivých segmentů nebo rozsahů segmentů.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Čtení záznamů od času

Záznamy kanálu změn můžete číst od počátečního segmentu až do konce. Podobně jako čtení záznamů v časovém rozsahu můžete vypsat segmenty a vybrat segment, ze který chcete začít iterace.

Tento příklad získá [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) první segment ke zpracování.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Tento příklad zpracovává záznamy kanálu změny počínaje [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) počátečnísegment.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Segment může mít protokoly kanálu změn v jednom nebo více *chunkFilePath*. V případě více *chunkFilePath* systém interně rozdělil záznamy do více oddílů pro správu propustnost publikování. Je zaručeno, že každý oddíl segmentu bude obsahovat změny pro vzájemně se vylučující objekty BLOB a může být zpracován nezávisle bez porušení pořadí. Třídu **ChangeFeedSegmentShardReader** můžete použít k iterátu prostřednictvím záznamů na úrovni úlomku, pokud je to pro váš scénář nejefektivnější.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o protokolech zdrojů změn. Viz [Kanál změn ve službě Azure Blob Storage (preview)](storage-blob-change-feed.md)
