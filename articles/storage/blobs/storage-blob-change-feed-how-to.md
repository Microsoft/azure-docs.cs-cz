---
title: Postup změny kanálu v Azure Blob Storage | Microsoft Docs
description: Informace o zpracování protokolů změn kanálu v klientské aplikaci .NET
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568247"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Zpracování kanálu změn v Azure Blob Storage

Změna kanálu poskytuje transakční protokoly všech změn, ke kterým dojde, do objektů BLOB a metadat objektů BLOB ve vašem účtu úložiště. V tomto článku se dozvíte, jak číst záznamy kanálu změn pomocí knihovny změn v modulu BLOB Change feed Processor.

Další informace o kanálu změn najdete v tématu [Změna kanálu v Azure Blob Storage](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>Získání knihovny pro změnu objektu BLOB Processor

1. Otevřete příkazové okno (například prostředí Windows PowerShell).
2. Z adresáře projektu nainstalujte [balíček NuGet **Azure. Storage. BLOBs. Changefeed** ](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Čtení záznamů

> [!NOTE]
> Kanál změn je v účtu úložiště neměnný a entita s oprávněními jen pro čtení. Libovolný počet aplikací může číst a zpracovávat kanál změn současně a nezávisle na svém vlastním pohodlí. Záznamy se neodstraňují z kanálu změn, když je aplikace přečte. Stav čtení nebo iterace každého náročného čtecího modulu je nezávislý a spravovaný pouze vaší aplikací.

Tento příklad projde všechny záznamy v kanálu změn, přidá je do seznamu a potom tento seznam vrátí volajícímu.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Tento příklad vytiskne v konzole několik hodnot z každého záznamu v seznamu. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Pokračovat v čtení záznamů z uložené pozice

Můžete zvolit, že se má vaše pozice pro čtení uložit v informačním kanálu, a pak pokračovat v iteraci záznamů v budoucnu. Pozici pro čtení můžete uložit získáním kurzoru pro změnu kanálu. Kurzor je **řetězec** a aplikace může tento řetězec uložit jakýmkoli způsobem, který dává smysl pro návrh vaší aplikace (například k souboru nebo databázi).

Tento příklad projde všechny záznamy v kanálu změn, přidá je do seznamu a uloží kurzor. Seznam a kurzor se vrátí volajícímu. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Zpracování datových proudů záznamů

Můžete zvolit zpracování záznamů kanálu změn, když jsou potvrzeny do kanálu změn. Viz [specifikace](storage-blob-change-feed.md#specifications). Události změny jsou v průměru publikovány do kanálu změn v období od 60 sekund. Při zadávání intervalu cyklického dotazování doporučujeme, abyste si v tomto období vydávali dotaz na nové změny.

V tomto příkladu se pravidelně dotazuje na změny.  Pokud existují záznamy změn, tento kód tyto záznamy zpracovává a ukládá kurzor na změnu kanálu. To znamená, že pokud se proces zastaví a pak znovu spustí, aplikace může použít kurzor pro pokračování ve zpracování záznamů, kde naposledy skončila. Tento příklad uloží kurzor do konfiguračního souboru místní aplikace, ale aplikace ho může uložit v jakémkoli formuláři, který má pro váš scénář největší smysl. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Čtení záznamů v časovém rozsahu

Můžete číst záznamy, které spadají do určitého časového rozsahu. Tento příklad prochází všechny záznamy v informačním kanálu změn, které spadají do 2 2020. března a 2:00 v 3:00.7 2020 srpna, přidá je do seznamu a potom tento seznam vrátí volajícímu.

### <a name="selecting-segments-for-a-time-range"></a>Výběr segmentů pro časový rozsah

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Čas zahájení, který zadáte, se zaokrouhlí dolů na nejbližší hodinu a koncový čas se zaokrouhlí na nejbližší hodinu. Může se stát, že uživatelé uvidí události, ke kterým došlo před časem spuštění a po ukončení. Je také možné, že se nezobrazí některé události, ke kterým dojde mezi začátkem a koncovým časem. To je proto, že události mohou být zaznamenány během hodiny před časem spuštění nebo během hodiny po čase ukončení.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o protokolech změn kanálu. Viz [Změna kanálu v Azure Blob Storage](storage-blob-change-feed.md)
