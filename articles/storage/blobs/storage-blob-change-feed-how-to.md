---
title: Zpracování kanálu změn ve službě Azure Blob Storage (Preview) | Microsoft Docs
description: Informace o zpracování protokolů změn kanálu v klientské aplikaci .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111865"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Zpracování kanálu změn ve službě Azure Blob Storage (Preview)

Změna kanálu poskytuje transakční protokoly všech změn, ke kterým dojde, do objektů BLOB a metadat objektů BLOB ve vašem účtu úložiště. V tomto článku se dozvíte, jak číst záznamy kanálu změn pomocí knihovny změn v modulu BLOB Change feed Processor.

Další informace o kanálu změn najdete v tématu [Změna kanálu v Azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> Kanál změn je ve verzi Public Preview a je dostupný v oblastech **westcentralus** a **westus2** . Další informace o této funkci spolu se známými problémy a omezeních najdete v tématu [Změna podpory kanálu v Azure Blob Storage](storage-blob-change-feed.md). Knihovna Change feed Processor se může změnit mezi nyní a až bude tato knihovna všeobecně dostupná.

## <a name="get-the-blob-change-feed-processor-library"></a>Získání knihovny pro změnu objektu BLOB Processor

1. Do sady Visual Studio přidejte `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` URL do zdrojů balíčků NuGet. 

   Informace o postupu najdete v tématu [zdroje balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Ve Správci balíčků NuGet Najděte balíček **Microsoft. Azure. Storage. Changefeed** a nainstalujte ho do svého projektu. 

   Informace o postupu najdete v tématu [vyhledání a instalace balíčku](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Připojení k účtu úložiště

Analyzujte připojovací řetězec voláním metody [CloudStorageAccount. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) . 

Pak vytvořte objekt, který představuje Blob Storage v účtu úložiště voláním metody [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) .

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

## <a name="initialize-the-change-feed"></a>Inicializace kanálu změn

Do horní části souboru kódu přidejte následující příkazy using. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Pak vytvořte instanci třídy **ChangeFeed** voláním metody **GetContainerReference** . Předejte název kontejneru kanálu změn.

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
> Kanál změn je v účtu úložiště neměnný a entita s oprávněními jen pro čtení. Libovolný počet aplikací může číst a zpracovávat kanál změn současně a nezávisle na svém vlastním pohodlí. Záznamy se neodstraňují z kanálu změn, když je aplikace přečte. Stav čtení nebo iterace každého náročného čtecího modulu je nezávislý a spravovaný pouze vaší aplikací.

Nejjednodušší způsob, jak číst záznamy, je vytvořit instanci třídy **ChangeFeedReader** . 

Tento příklad prochází všechny záznamy v kanálu změn a pak tiskne do konzoly několik hodnot z každého záznamu. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Obnovování čtení záznamů z uložené pozice

Můžete zvolit, že se má vaše pozice pro čtení uložit v informačním kanálu, a pokračovat v iteraci záznamů v budoucím čase. Stav iterace kanálu změn můžete kdykoli uložit pomocí metody **ChangeFeedReader. SerializeState ()** . Stav je **řetězec** a aplikace může tento stav uložit na základě návrhu vaší aplikace (například: do databáze nebo souboru).

```csharp
    string currentReadState = processor.SerializeState();
```

Můžete pokračovat v iteraci pomocí záznamů z posledního stavu vytvořením **ChangeFeedReader** pomocí metody **CreateChangeFeedReaderFromPointerAsync** .

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

Můžete zvolit zpracování záznamů kanálu změn při jejich doručení. Viz [specifikace](storage-blob-change-feed.md#specifications).

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

Kanál změn je uspořádán do hodinových segmentů na základě času události změny. Viz [specifikace](storage-blob-change-feed.md#specifications). Můžete číst záznamy z segmentů změny kanálu, které spadají do určitého časového rozsahu.

Tento příklad získá počáteční časy všech segmentů. Pak tento seznam projde do tohoto seznamu, dokud počáteční čas nenásleduje za časem posledního přípraváho segmentu nebo mimo koncový čas požadovaného rozsahu. 

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

Můžete číst záznamy kanálu změn od počátečního segmentu až do konce. Podobně jako při čtení záznamů v časovém rozsahu můžete vypsat segmenty a vybrat segment, ze kterého se má spustit iterace.

Tento příklad načte hodnotu [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) prvního segmentu ke zpracování.

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

Tento příklad zpracovává záznamy kanálu změn počínaje od [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) počátečního segmentu.

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
> Segment může mít v jednom nebo více *chunkFilePath*protokoly změny kanálu. V případě více *chunkFilePath* systém interně rozdělil záznamy do více horizontálních oddílů pro správu propustnosti publikování. Je zaručeno, že každý oddíl segmentu bude obsahovat změny pro vzájemně se vylučující objekty BLOB a lze je zpracovat nezávisle bez porušení řazení. Třídu **ChangeFeedSegmentShardReader** můžete použít k iteraci záznamů na úrovni horizontálních oddílů, pokud je to pro váš scénář nejúčinnější.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o protokolech změn kanálu. Viz [Změna kanálu v Azure Blob Storage (Preview)](storage-blob-change-feed.md)
