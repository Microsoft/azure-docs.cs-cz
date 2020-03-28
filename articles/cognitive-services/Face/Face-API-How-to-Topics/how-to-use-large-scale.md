---
title: 'Příklad: Použití funkce Velké měřítko – plocha'
titleSuffix: Azure Cognitive Services
description: Tato příručka je článek o škálování z existujících PersonGroup a FaceList objekty LargePersonGroup a LargeFaceList objekty.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169772"
---
# <a name="example-use-the-large-scale-feature"></a>Příklad: Použití funkce ve velkém měřítku

Tato příručka je rozšířený článek o škálování z existujících PersonGroup a FaceList objekty LargePersonGroup a LargeFaceList objekty, v uvedeném pořadí. Tato příručka ukazuje proces migrace. Předpokládá základní znalost PersonGroup a FaceList objekty, [train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operace a funkce rozpoznávání obličeje. Další informace o těchto tématech naleznete v koncepční příručce [rozpoznávání tváří.](../concepts/face-recognition.md)

LargePersonGroup a LargeFaceList jsou souhrnně označovány jako rozsáhlé operace. LargePersonGroup může obsahovat až 1 milion osob, každý s maximálně 248 tvářemi. LargeFaceList může obsahovat až 1 milion ploch. Rozsáhlé operace jsou podobné konvenční PersonGroup a FaceList, ale mají některé rozdíly z důvodu nové architektury. 

Ukázky jsou zapsány v C# pomocí knihovny klienta Azure Cognitive Services Face.

> [!NOTE]
> Chcete-li povolit výkon hledání tváří pro identifikaci a FindSimilar ve velkém měřítku, zavést operaci Train předem zpracovat LargeFaceList a LargePersonGroup. Doba tréninku se pohybuje od sekund do asi půl hodiny v závislosti na skutečné kapacitě. Během tréninkového období je možné provést identifikace a FindSimilar, pokud byl úspěšný trénink ový provoz proveden dříve. Nevýhodou je, že nové přidané osoby a tváře se nezobrazí ve výsledku, dokud nebude dokončena nová migrace po rozsáhlém školení.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: Inicializovat objekt klienta

Při použití knihovny klienta Face, klíč předplatného a koncový bod předplatného jsou předány prostřednictvím konstruktoru FaceClient třídy. Například:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Pokud chcete získat klíč předplatného s odpovídajícím koncovým bodem, přejděte na Azure Marketplace z webu Azure Portal.
Další informace naleznete v [tématu Předplatná](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: Migrace kódu

Tato část se zaměřuje na způsob migrace PersonGroup nebo FaceList implementace LargePersonGroup nebo LargeFaceList. Přestože LargePersonGroup nebo LargeFaceList se liší od PersonGroup nebo FaceList v návrhu a vnitřní implementace, rozhraní rozhraní API jsou podobné pro zpětnou kompatibilitu.

Migrace dat není podporována. Místo toho znovu vytvoříte LargePersonGroup nebo LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrace skupiny osob do skupiny LargePersonGroup

Migrace z PersonGroup na LargePersonGroup je jednoduchá. Sdílejí přesně stejné operace na úrovni skupiny.

Pro persongroup nebo osoba související s implementací je nutné změnit pouze cesty rozhraní API nebo třídy/modulu Sady SDK na LargePersonGroup a LargePersonGroup Person.

Přidejte všechny tváře a osoby z PersonGroup do nové Skupiny LargePersonGroup. Další informace naleznete v tématu [Přidání ploch](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrace facelistu do largefacelistu

| Rozhraní API kolekce FaceList | Rozhraní API kolekce LargeFaceList |
|:---:|:---:|
| Vytvořit | Vytvořit |
| Odstranění | Odstranění |
| Získat | Získat |
| Seznam | Seznam |
| Aktualizace | Aktualizace |
| - | Trénování |
| - | Get Training Status |

Předchozí tabulka je srovnáním operací na úrovni seznamu mezi kolekcemi FaceList a LargeFaceList. Jak je ukázáno, LargeFaceList přichází s novými operacemi, Train a Get Training Status, ve srovnání s FaceList. Školení LargeFaceList je předpokladem [findsimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operace. Školení není nutné pro FaceList. Následující úryvek je pomocná funkce čekat na školení LargeFaceList:

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

Dříve typické použití FaceList s přidanými tvářemi a FindSimilar vypadalo takto:

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

Při migraci do LargeFaceList se stane následující:

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

Jak již bylo ukázáno dříve, správa dat a findsimilar část jsou téměř stejné. Jedinou výjimkou je, že čerstvé předzpracování train operace musí být dokončena v LargeFaceList před FindSimilar funguje.

## <a name="step-3-train-suggestions"></a>Krok 3: Návrhy vlaků

I když vlak provoz urychluje [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), školení čas trpí, zejména při příchodu do velkého rozsahu. Odhadovaná doba tréninku v různých měřítcích je uvedena v následující tabulce.

| Měřítko pro plochy nebo osoby | Odhadovaná doba tréninku |
|:---:|:---:|
| 1 000 | 1-2 s |
| 10 000 | 5-10 s |
| 100 000 | 1-2 min |
| 1 000 000 | 10-30 min |

Chcete-li lépe využít funkci ve velkém měřítku, doporučujeme následující strategie.

### <a name="step-31-customize-time-interval"></a>Krok 3.1: Přizpůsobení časového intervalu

Jak je `TrainLargeFaceList()`znázorněno v , je časový interval v milisekundách zpoždění procesu kontroly stavu nekonečné školení. U kolekce LargeFaceList s více tvářemi bude použití delšího intervalu znamenat nižší počet volání i náklady. Přizpůsobte časový interval podle očekávané kapacity LargeFaceList.

Stejná strategie platí i pro LargePersonGroup. Například při trénování LargePersonGroup s `timeIntervalInMilliseconds` 1 milion osob, může být 60 000, což je interval 1 minuty.

### <a name="step-32-small-scale-buffer"></a>Krok 3.2: Vyrovnávací paměť malého rozsahu

Osoby nebo tváře v LargePersonGroup nebo LargeFaceList jsou prohledávatelné pouze po školení. V dynamickém scénáři jsou neustále přidávány nové osoby nebo tváře, které musí být okamžitě prohledávatelné, ale školení může trvat déle, než je požadováno. 

Chcete-li tento problém zmírnit, použijte extra malé largepersongroup nebo LargeFaceList jako vyrovnávací paměť pouze pro nově přidané položky. Tato vyrovnávací paměť trvá kratší dobu trénovat z důvodu menší velikosti. Možnost okamžitého vyhledávání v této dočasné vyrovnávací paměti by měla fungovat. Použijte tuto vyrovnávací paměť v kombinaci s trénování na hlavní LargePersonGroup nebo LargeFaceList spuštěním hlavní školení v intervalu sparser. Příklady jsou uprostřed noci a denně.

Příklad pracovního postupu:

1. Vytvořte hlavní LargePersonGroup nebo LargeFaceList, což je hlavní kolekce. Vytvořte vyrovnávací paměť LargePersonGroup nebo LargeFaceList, což je kolekce vyrovnávací paměti. Kolekce vyrovnávací paměti je pouze pro nově přidané osoby nebo tváře.
1. Přidejte nové osoby nebo tváře do hlavní kolekce i kolekce vyrovnávací paměti.
1. Pouze trénování kolekce vyrovnávací paměti s krátkým časovým intervalem, aby bylo zajištěno, že nově přidané položky se projeví.
1. Identifikace volání nebo FindSimilar proti hlavní kolekce a kolekce vyrovnávací paměti. Sloučit výsledky.
1. Pokud se velikost kolekce vyrovnávací paměti zvětší na prahovou hodnotu nebo v době nečinnosti systému, vytvořte novou kolekci vyrovnávací paměti. Aktivuj operaci Train v hlavní kolekci.
1. Odstraňte starou kolekci vyrovnávací paměti po dokončení operace Train v hlavní kolekci.

### <a name="step-33-standalone-training"></a>Krok 3.3: Samostatný trénink

Pokud relativně dlouhá latence je přijatelná, není nutné aktivovat operaci Train hned po přidání nových dat. Místo toho můžete operaci Train oddělit od hlavní logiky a spouštět ji pravidelně. Tato strategie je vhodná pro dynamické scénáře s přijatelnou latencí. Lze jej použít na statické scénáře k dalšímu snížení frekvence vlaku.

Předpokládejme, že `TrainLargePersonGroup` existuje `TrainLargeFaceList`funkce podobná . Typické provádění samostatné školení na LargePersonGroup vyvoláním [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) třídy v `System.Timers` je:

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

Další informace o správě dat a implementacích souvisejících s identifikací naleznete v tématu [Přidání tváří](how-to-add-faces.md) a [Identifikace tváří v bitové kopii](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Souhrn

V této příručce jste se dozvěděli, jak migrovat existující persongroup nebo facelist kód, nikoli data, do LargePersonGroup nebo LargeFaceList:

- LargePersonGroup a LargeFaceList pracovat podobně jako PersonGroup nebo FaceList, s tím rozdílem, že train operace je vyžadována LargeFaceList.
- Využijte správnou strategii train k dynamické aktualizaci dat pro rozsáhlé datové sady.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů, jak přidat tváře do PersonGroup nebo provést operaci Identifikovat na PersonGroup.

- [Přidání tváří](how-to-add-faces.md)
- [Identifikace tváří v obraze](HowtoIdentifyFacesinImage.md)
