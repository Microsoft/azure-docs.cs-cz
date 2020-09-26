---
title: 'Příklad: Používejte velkou škálu funkcí – Face'
titleSuffix: Azure Cognitive Services
description: Tato příručka je článek týkající se horizontálního navýšení kapacity z existujících objektů Person a FaceList na objekty LargePersonGroup a LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 5341c2613624c6a52f1649dcd8a64b6746b84f67
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332385"
---
# <a name="example-use-the-large-scale-feature"></a>Příklad: použití funkce rozsáhlého škálování

Tato příručka je pokročilý článek týkající se horizontálního navýšení kapacity od stávajících objektů Person a FaceList do objektů LargePersonGroup a LargeFaceList. Tato příručka ukazuje proces migrace. Předpokládá základní znalost pomocí objektů Person a FaceList, operace [vlaku](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) a funkcí rozpoznávání obličeje. Další informace o těchto tématech najdete v koncepční příručce pro [rozpoznávání tváře](../concepts/face-recognition.md) .

LargePersonGroup a LargeFaceList jsou souhrnně označovány jako operace ve velkém rozsahu. LargePersonGroup může obsahovat až 1 000 000 lidí, z nichž každý má maximálně 248 ploch. LargeFaceList může obsahovat až 1 000 000 ploch. Rozsáhlé operace jsou podobné konvenčním osobám a FaceList, ale mají několik rozdílů z důvodu nové architektury. 

Ukázky jsou napsány v jazyce C# pomocí klientské knihovny Azure Cognitive Services Face.

> [!NOTE]
> Pokud chcete povolit výkon při hledání na tvář pro identifikaci a FindSimilar ve velkém měřítku, zaveďte vlakovou operaci pro předzpracování LargeFaceList a LargePersonGroup. Doba školení se v závislosti na skutečné kapacitě mění v rozmezí sekund až o půl hodiny. Během období školení je možné provést identifikaci a FindSimilar, pokud se úspěšně provedlo úspěšné školení. Nevýhodou je, že nově přidané osoby a plošky se ve výsledku nebudou zobrazovat, dokud se nedokončí nová migrace po migraci do rozsáhlého školení.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: inicializace objektu klienta

Při použití klientské knihovny pro tvář se klíč předplatného a koncový bod předplatného předávají prostřednictvím konstruktoru třídy FaceClient. Příklad:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Chcete-li získat klíč předplatného s odpovídajícím koncovým bodem, v Azure Portal Azure Marketplace.
Další informace najdete v tématu [předplatná](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: migrace kódu

Tato část se zaměřuje na migraci implementace person nebo FaceList na LargePersonGroup nebo LargeFaceList. I když se LargePersonGroup nebo LargeFaceList liší od Personu nebo FaceList v rámci navrhování a interní implementace, rozhraní API jsou podobná zpětné kompatibilitě.

Migrace dat není podporovaná. Místo toho jste znovu vytvořili LargePersonGroup nebo LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrace osoby typu Person na LargePersonGroup

Migrace ze služby Person na LargePersonGroup je jednoduchá. Sdílejí přesně stejné operace na úrovni skupiny.

Pro uživatele nebo implementaci související s osobou je nutné změnit pouze cesty rozhraní API nebo třídu nebo modul sady SDK na LargePersonGroup a LargePersonGroup Person.

Přidejte všechny plošky a osoby ze strany Person do nové LargePersonGroup. Další informace najdete v tématu [Přidání plošek](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrace FaceListu na LargeFaceList

| Rozhraní API kolekce FaceList | Rozhraní API kolekce LargeFaceList |
|:---:|:---:|
| Vytvořit | Vytvořit |
| Odstranit | Odstranit |
| Získat | Získat |
| Seznam | Seznam |
| Aktualizace | Aktualizace |
| - | Trénování |
| - | Get Training Status |

Předchozí tabulka je srovnáním operací na úrovni seznamu mezi kolekcemi FaceList a LargeFaceList. Jak je zobrazeno, LargeFaceList přináší nové operace, výuku a získání stavu školení ve srovnání s FaceList. Školení LargeFaceList je podmínkou operace [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) . Pro FaceList se nevyžaduje školení. Následující fragment kódu je pomocná funkce, která čeká na školení LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Dříve typické použití FaceList s přidanými ploškami a FindSimilar vypadalo takto:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Když ho migrujete na LargeFaceList, bude se jednat o následující:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Jak bylo uvedeno dříve, Správa dat a část FindSimilar jsou téměř stejné. Jedinou výjimkou je to, že je potřeba, aby se v LargeFaceList, než FindSimilar funguje, dokončila operace nového předběžného zpracování vlaku.

## <a name="step-3-train-suggestions"></a>Krok 3: návrhy vlaku

I když se operace vlaků zrychlí [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [Identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), je doba výuky, zejména při přicházejících do velkého rozsahu. Odhadovaná doba školení v různých měřítkech je uvedena v následující tabulce.

| Škálování pro plošky nebo osoby | Odhadovaná doba školení |
|:---:|:---:|
| 1 000 | 1-2 sec |
| 10 000 | 5-10 sec |
| 100 000 | 1-2 min. |
| 1 000 000 | 10-30 min. |

Abychom lépe využili velkou škálu funkcí, doporučujeme následující strategie.

### <a name="step-31-customize-time-interval"></a>Krok 3,1: přizpůsobení časového intervalu

Jak je znázorněno v `TrainLargeFaceList()` , je časový interval v milisekundách pro zpoždění procesu kontroly stavu nekonečného školení. U kolekce LargeFaceList s více tvářemi bude použití delšího intervalu znamenat nižší počet volání i náklady. Upravte časový interval podle očekávané kapacity LargeFaceList.

Stejná strategie platí i pro LargePersonGroup. Například když provedete LargePersonGroupi s 1 000 000 osobami, `timeIntervalInMilliseconds` může to být 60 000, což je 1 minutový interval.

### <a name="step-32-small-scale-buffer"></a>Krok 3,2: vyrovnávací paměť se zmenšeným škálováním

Osoby nebo obličeje v LargePersonGroup nebo LargeFaceList jsou prohledávatelné až po vyškolení. V dynamickém scénáři se nové osoby nebo obličeje stále přidávají a je nutné je ihned prohledávat, ale školení může trvat déle, než je potřeba. 

Pokud chcete tento problém zmírnit, použijte jako vyrovnávací paměť další LargePersonGroup nebo LargeFaceList pro nově přidané položky. Analýza této vyrovnávací paměti trvá kvůli menší velikosti kratší dobu. Funkce okamžitého vyhledávání v této dočasné vyrovnávací paměti by měla fungovat. Tuto vyrovnávací paměť použijte v kombinaci s školením na hlavní LargePersonGroup nebo LargeFaceList, a to spuštěním hlavního školení v intervalu zhuštění. Příklady jsou uprostřed noční a každodenní.

Příklad pracovního postupu:

1. Vytvořte hlavní LargePersonGroup nebo LargeFaceList, což je hlavní kolekce. Vytvořte vyrovnávací paměť LargePersonGroup nebo LargeFaceList, což je kolekce vyrovnávací paměti. Kolekce vyrovnávací paměti je určena pouze pro nově přidané osoby nebo plošky.
1. Přidejte nové osoby nebo obličeje do hlavní kolekce i do kolekce vyrovnávací paměti.
1. Shromažďování vyrovnávací paměti pouze v krátkém časovém intervalu, aby bylo zajištěno, že se nově přidané položky projeví.
1. Identifikaci nebo FindSimilar volání pro hlavní kolekci i kolekci vyrovnávací paměti. Sloučí výsledky.
1. Když se velikost kolekce vyrovnávací paměti zvyšuje na prahovou hodnotu nebo v době nečinnosti systému, vytvořte novou kolekci vyrovnávací paměti. Aktivovat operaci vlaku na hlavní kolekci.
1. Po dokončení operace vlaku na hlavní kolekci odstraňte starou kolekci vyrovnávací paměti.

### <a name="step-33-standalone-training"></a>Krok 3,3: samostatné školení

Pokud je přijatelné poměrně dlouhé latence, nemusíte aktivovat operaci vlaku hned po přidání nových dat. Místo toho můžete operaci Train oddělit od hlavní logiky a spouštět ji pravidelně. Tato strategie je vhodná pro dynamické scénáře s přijatelnou latencí. Dá se použít ke statickým scénářům k dalšímu snížení četnosti vlaků.

Předpokládejme, že je `TrainLargePersonGroup` funkce podobná `TrainLargeFaceList` . Typická implementace samostatného školení na LargePersonGroup vyvoláním [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) třídy v `System.Timers` je:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Další informace o implementacích týkajících se správy dat a identifikace najdete v tématu [Přidání plošek](how-to-add-faces.md).

## <a name="summary"></a>Shrnutí

V této příručce jste zjistili, jak migrovat existující kód person nebo FaceList, ne data, na LargePersonGroup nebo LargeFaceList:

- LargePersonGroup a LargeFaceList fungují podobně jako person nebo FaceList, s tím rozdílem, že operace vlaku vyžaduje LargeFaceList.
- Využijte správnou strategii vlaků k aktualizaci dynamických dat pro velké datové sady.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů průvodce, abyste se seznámili s tím, jak přidat plošky do pole person nebo napsat skript, který provede operaci identifikace ve službě Person.

- [Přidání tváří](how-to-add-faces.md)
- [Rychlý úvod do klientské knihovny](../Quickstarts/client-libraries.md)