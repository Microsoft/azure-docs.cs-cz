---
title: 'Příklad: Použít funkci ve velkém měřítku – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Použijte funkci v rozhraní API pro rozpoznávání tváře ve velkém měřítku.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449046"
---
# <a name="example-use-the-large-scale-feature"></a>Příklad: Použití funkce ve velkém měřítku

Tento průvodce je pokročilá článek o tom, jak vertikálně navýšit kapacitu z existujících objektů jeden objekt PersonGroup a FaceList LargePersonGroup a LargeFaceList objektů, v uvedeném pořadí. Tato příručka ukazuje, proces migrace. Předpokládá základní znalost jeden objekt PersonGroup a FaceList objekty [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operace a funkce rozpoznávání tváře. Další informace o těchto tématech najdete v tématu [rozpoznávání tváře](../concepts/face-recognition.md) koncepční průvodce.

LargePersonGroup a LargeFaceList se souhrnně označují jako operace ve velkém měřítku. LargePersonGroup může obsahovat až 1 milion osob, každou s maximálně 248 tváří. LargeFaceList může obsahovat až 1 milión tváří. Rozsáhlé operace jsou podobné konvenční jeden objekt PersonGroup a FaceList ale několik rozdílů kvůli novou architekturu. 

Ukázky jsou napsané v C# pomocí klientské knihovny Azure Cognitive Services Face API.

> [!NOTE]
> Pokud chcete povolit hledání výkonu tváří pro identifikaci a FindSimilar ve velkém měřítku, představují operace trénování se předběžně zpracovat LargeFaceList a LargePersonGroup. Čas školení se liší od sekund přibližně půl hodiny na základě skutečné kapacity. Během období školení je možné provést rozpoznání a FindSimilar Pokud úspěšné školení provozní bylo provedeno před. Nevýhodou je, nový přidaný osob a tváří nezobrazí ve výsledcích až do dokončení nové po migraci k trénování ve velkém měřítku.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: Inicializace objektu klienta

Při použití klientské knihovny rozhraní API pro rozpoznávání tváře, klíč předplatného a koncový bod předplatného se předává v konstruktoru třídy FaceClient. Příklad:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Klíč předplatného s jeho odpovídající koncový bod, najdete na webu Azure Marketplace na webu Azure Portal.
Další informace najdete v tématu [předplatná](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: Migrace kódu

Tato část se zaměřuje na tom, jak migrovat jeden objekt PersonGroup nebo FaceList implementace LargePersonGroup nebo LargeFaceList. I když LargePersonGroup nebo LargeFaceList se liší od jeden objekt PersonGroup nebo FaceList v návrhu a interní implementaci, rozhraní API jsou podobné z důvodu zpětné kompatibility.

Migrace dat se nepodporuje. Můžete znovu vytvořit LargePersonGroup nebo LargeFaceList místo.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrace jeden objekt PersonGroup LargePersonGroup

Migrace z jeden objekt PersonGroup do LargePersonGroup je jednoduché. Sdílejí přesně stejné operace na úrovni skupiny.

Pro jeden objekt PersonGroup nebo osoba související implementace je nutné změnit cesty k rozhraní API nebo sady SDK a modul třídy LargePersonGroup a LargePersonGroup osoby.

Přidejte všechny tváří a osoby z jeden objekt PersonGroup nové LargePersonGroup. Další informace najdete v tématu [přidat tváří](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrace FaceList LargeFaceList

| Rozhraní API kolekce FaceList | Rozhraní API kolekce LargeFaceList |
|:---:|:---:|
| Vytvořit | Vytvořit |
| Odstranění | Odstranění |
| Získat | Získat |
| List | List |
| Aktualizace | Aktualizace |
| - | Trénování |
| - | Get Training Status |

Předchozí tabulka je srovnáním operací na úrovni seznamu mezi kolekcemi FaceList a LargeFaceList. Jak je znázorněno, LargeFaceList obsahuje nové operace trénování a získat školení stavu, ve srovnání s FaceList. Školení LargeFaceList je podmínkou [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operace. Není vyžadováno pro FaceList školení. Následující fragment kódu je pomocná funkce čekat na školení LargeFaceList:

```CSharp
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

Typické použití FaceList přidaných ploch a FindSimilar dříve, hledá vypadat asi takto:

```CSharp
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

Při migraci ho do LargeFaceList, stane se následující:

```CSharp
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

Jak už jsme ukázali Správa dat a další část FindSimilar je téměř stejný. Jedinou výjimkou je, že funkce předběžného zpracování operace trénování, musíte dokončit v LargeFaceList FindSimilar funguje.

## <a name="step-3-train-suggestions"></a>Krok 3: Trénování návrhy

I když urychluje operace trénování [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), odkážete čas školení, zejména v případě, že se chystá se velkého rozsahu. Školení odhadovaný čas v různých měřítek je uvedené v následující tabulce.

| Škálování pro tváří nebo osoby | Školení odhadovaný čas |
|:---:|:---:|
| 1 000 | 1-2 s |
| 10,000 | 5 až 10 sekundu |
| 100 000 | 1 až 2 minuty |
| 1 000 000 | 10 – 30 min |

Jak lépe využít funkci ve velkém měřítku, doporučujeme následující strategie.

### <a name="step-31-customize-time-interval"></a>Krok 3.1: Upravit časový interval

Jak je znázorněno v `TrainLargeFaceList()`, je časový interval v milisekundách pro odložené proces kontroly stavu nekonečné školení. U kolekce LargeFaceList s více tvářemi bude použití delšího intervalu znamenat nižší počet volání i náklady. Upravte časový interval podle očekávané kapacitu LargeFaceList.

Stejné strategie platí také pro LargePersonGroup. Například při trénování LargePersonGroup s 1 milion osob `timeIntervalInMilliseconds` asi 60 000, což je interval 1 minuta.

### <a name="step-32-small-scale-buffer"></a>Krok 3.2: Rozsáhlé vyrovnávací paměti

Osoby nebo tváří LargePersonGroup nebo LargeFaceList je možné prohledávat pouze po se školení. V případě dynamických nové osoby nebo plochy se neustále přidávají a musí být okamžitě prohledávatelná dosud školení může trvat déle, než je potřeba. 

Ke zmírnění tohoto problému, používejte velmi rozsáhlé LargePersonGroup nebo LargeFaceList jako vyrovnávací paměť pouze pro nově přidané položky. Tuto vyrovnávací paměť trvá kratší čas pro učení z důvodu menší velikost. Možnost okamžitého vyhledávání na této dočasné vyrovnávací paměti by mělo fungovat. Pomocí této vyrovnávací paměti v kombinaci s přípravou na hlavní LargePersonGroup nebo LargeFaceList spuštěním hlavní školení na sparser intervalu. Příkladem mohou být uprostřed noci a každý den.

Příklad pracovního postupu:

1. Vytvořte hlavní LargePersonGroup nebo LargeFaceList, což je hlavní sada. Vytvořte zásobník LargePersonGroup nebo LargeFaceList, což je kolekce vyrovnávací paměti. Vyrovnávací paměti kolekce je určena pouze pro nově přidané osoby nebo plochy.
1. Přidáte nový osoby nebo tváří do hlavní kolekce a kolekce vyrovnávací paměti.
1. Kolekce vyrovnávací paměti s intervalem krátkého formátu času k zajištění, že nově přidané položky se projeví pouze trénování.
1. Identifikace volání nebo FindSimilar proti kolekci hlavní a kolekci vyrovnávací paměti. Sloučit výsledky.
1. Pokud velikost vyrovnávací paměti kolekce zvyšuje prahové hodnoty nebo na nečinnost systému, vytvořte novou kolekci vyrovnávací paměti. Aktivace operace trénování na hlavní kolekce.
1. Po dokončení operace trénování na hlavní sada, odstraňte starý kolekci vyrovnávací paměti.

### <a name="step-33-standalone-training"></a>Krok 3.3: Samostatné školení

Pokud poměrně dlouhé latence je přijatelná, není nutné spouštěcí správné operace trénování, poté, co přidáte nová data. Místo toho můžete operaci Train oddělit od hlavní logiky a spouštět ji pravidelně. Tato strategie je vhodná pro scénáře dynamických s přijatelnou latencí. Lze použít ke statické scénářům další snížení frekvence trénování.

Předpokládejme, že je `TrainLargePersonGroup` funkce podobné `TrainLargeFaceList`. Obvyklá implementace samostatné školení na LargePersonGroup vyvoláním [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) třídy v `System.Timers` je:

```CSharp
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

Další informace o správě dat a souvisejícím s identifikace implementace najdete v tématu [přidat tváří](how-to-add-faces.md) a [identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, jak migrovat existující jeden objekt PersonGroup nebo FaceList kód, ne data do LargePersonGroup nebo LargeFaceList:

- LargePersonGroup a LargeFaceList fungují podobně jako jeden objekt PersonGroup nebo FaceList, s tím rozdílem, že operace trénování nevyžadovala LargeFaceList.
- Provést řádné strategie trénování k aktualizaci dynamických dat pro rozsáhlé datové sady.

## <a name="next-steps"></a>Další postup

Použijte postupy příručku a zjistěte, jak přidat tváří jeden objekt PersonGroup nebo provedení operace identifikovat na jeden objekt PersonGroup.

- [Přidat tváří](how-to-add-faces.md)
- [Identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)