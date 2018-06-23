---
title: Použít funkci ve velkém měřítku stěně rozhraní API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Použijte funkci ve velkém rozsahu v vzhled rozhraní API pro kognitivní služby.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342524"
---
# <a name="how-to-use-the-large-scale-feature"></a>Jak používat funkci ve velkém měřítku

Tento průvodce je pokročilé článek na kód migrace přejít z existující PersonGroup a FaceList LargePersonGroup a LargeFaceList v uvedeném pořadí.
Tato příručka ukazuje, proces migrace předpokladu zjistit základní informace o využití PersonGroup a FaceList.
Získání obeznámeni s základních operací, najdete v tématu ostatní kurzy, jako [zjištění tyto řezy v obrázcích](HowtoIdentifyFacesinImage.md),

Rozhraní API služby Microsoft vzhled nedávno vydané dvě funkce povolit scénáře, ve velkém měřítku, LargePersonGroup a LargeFaceList, které se souhrnně označují jako rozsáhlé operace.
LargePersonGroup může obsahovat až 1 000 000 osob každý delší než 248 řezy a LargeFaceList může obsahovat až 1 000 000 řezy.

Ve velkém měřítku operace jsou podobné konvenční PersonGroup a FaceList, ale existují některé významné rozdíly, z důvodu nové architektury.
Tato příručka ukazuje, proces migrace předpokladu zjistit základní informace o využití PersonGroup a FaceList.
Ukázky jsou napsané v C# pomocí klientské knihovny vzhled rozhraní API.

Chcete-li řez výkonu vyhledávání pro identifikaci a FindSimilar ve velkém měřítku, budete muset zavést Train operaci předběžné zpracování LargeFaceList a LargePersonGroup.
Čas školení se liší od sekund se chystáte půl hodiny v závislosti na aktuální kapacita.
Během období školení je stále možné provádět identifikace a FindSimilar-li úspěšně školení provádí ještě před.
Nevýhodou je ale, že přidané nové osob nebo řezy nezobrazí ve výsledku až do dokončení nové po migraci pro rozsáhlé školení.

## <a name="concepts"></a> Koncepty

Pokud nejste obeznámeni s následující koncepty v této příručce, definice lze nalézt v [Glosář](../Glossary.md):

- LargePersonGroup: Kolekce osob s kapacitou až 1 000 000.
- LargeFaceList: Kolekce řezy s kapacitou až 1 000 000.
- Train: Před proces identifikace/FindSimilar výkon.
- Identifikace: Určete jeden nebo více řezy z PersonGroup nebo LargePersonGroup.
- FindSimilar: Řezy podobné vyhledávání z FaceList nebo LargeFaceList.

## <a name="initialization"></a> Krok 1: Autorizovat volání rozhraní API

Pokud používáte rozhraní API vzhled klientské knihovny, klíč předplatného a koncový bod předplatného se předaly v konstruktoru třídy FaceServiceClient. Příklad:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Klíč předplatného s odpovídající koncového bodu je možné získat ze stránky portálu Azure Marketplace.
V tématu [odběry](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Krok 2: Migrace kódu v akci

Tato část se zaměřuje jenom na migrace implementace PersonGroup/FaceList k LargePersonGroup/LargeFaceList.
I když LargePersonGroup/LargeFaceList se liší od PersonGroup nebo FaceList v návrhu a implementace interní, rozhraní API jsou podobné pro back kompatibility.

Nepodporuje migraci dat, budete muset znovu vytvořit LargePersonGroup/LargeFaceList místo.

## <a name="largepersongroup"></a> Krok 2.1: Migrace PersonGroup do LargePersonGroup

Migrace z PersonGroup LargePersonGroup je smooth, protože sdílejí přesně stejné operace na úrovni skupiny.

PersonGroup nebo osoba související s implementací, pouze je nutné změnit cesty rozhraní API nebo sady SDK nebo modul třídy LargePersonGroup a LargePersonGroup osoby.

Z hlediska migraci dat najdete v části [postup přidání otočená](how-to-add-faces.md) pro referenci.

## <a name="largefacelist"></a> Krok 2.2: Migrace FaceList do LargeFaceList

| Rozhraní API FaceList | Rozhraní API LargeFaceList |
|:---:|:---:|
| Vytvořit | Vytvořit |
| Odstranění | Odstranění |
| Získat | Získat |
| Seznam | Seznam |
| Aktualizace | Aktualizace |
| - | Trénování |
| - | Získat stav školení |

V předchozí tabulce je uvedeno porovnání operace na úrovni seznamu mezi FaceList a LargeFaceList.
Jak je vidět, LargeFaceList se dodává s nových operací, Train a získat stav školení ve srovnání s FaceList.
Získání LargeFaceList cvičení je podmínkou pro [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operace při zde není žádná Train požadované pro FaceList.
Následující fragment kódu je pomocné funkce čekání na školení LargeFaceList.

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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

Dřív by typické použití FaceList přidávání řezy a FindSimilar

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Při migraci se LargeFaceList, měl být

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Jak se uvádí výše, Správa dat a část FindSimilar jsou téměř stejné.
Jedinou výjimkou je, že nový předběžného zpracování operace Train musí dokončení v LargeFaceList před FindSimilar funguje.

## <a name="train"></a>Krok 3: Train návrhy

I když urychluje operace Train [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), čas školení vyskytne, zejména v případě, že přejdete-li na velkém měřítku.
Čas odhadované školení v různých měřítek je uvedené v následující tabulce:

| Škálování (řezy nebo osoby) | Odhadovaný čas školení |
|:---:|:---:|
| 1 000 | s 1 – 2 |
| 10 000 | 5 až 10 s |
| 100,000 | 1 - 2 min. |
| 1 000 000 | 10 - 30 min. |

Chcete-li lépe využít funkci ve velkém měřítku, se doporučuje vzít v úvahu několik strategií.

## <a name="interval"></a>Krok 3.1: Přizpůsobení časový Interval

Jak je znázorněno v `TrainLargeFaceList()`, je `timeIntervalInMilliseconds` zpoždění nekonečné školení stav proces kontroly.
Pro LargeFaceList s další řezy pomocí delší interval snižuje počet volání a náklady.
Časový interval by měl přizpůsobit podle očekávané kapacitu LargeFaceList.

Stejné strategie platí také pro LargePersonGroup.
Při školení LargePersonGroup s 1 000 000 osob, například `timeIntervalInMilliseconds` může být 60 000 (také známa jako interval 1 minutu).

## <a name="buffer"></a>Krok 3.2 rozsáhlé vyrovnávací paměti

Osoby nebo řezy v LargePersonGroup/LargeFaceList se prohledávatelné jenom po probíhajícího cvičení.
Ve scénáři dynamické nové osob nebo řezy jsou neustále přidány a musí být okamžitě prohledávatelné dosud školení může trvat déle, než je potřeba.
Tento problém tak zmírnit, můžete velmi rozsáhlé LargePersonGroup/LargeFaceList jako vyrovnávací paměť pouze pro nově přidané položky.
Této vyrovnávací paměti trvá kratší dobu cvičení kvůli mnohem menší velikost a okamžitou hledání na toto dočasný vyrovnávací paměti by měla fungovat.
Pomocí této vyrovnávací paměti v kombinaci s školení na hlavní LargePersonGroup/LargeFaceList spuštěním hlavní školení v intervalu více zhuštěných, například v noci střední a denně.

Ukázku pracovního postupu:
1. Vytvořte hlavní LargePersonGroup/LargeFaceList (hlavní kolekce) a vyrovnávací paměť LargePersonGroup/LargeFaceList (shromažďování vyrovnávací paměti). Kolekce vyrovnávací paměti je pouze pro nově přidaného osob nebo řezy.
1. Přidáte nové osob nebo řezy hlavní kolekce a kolekce vyrovnávací paměti.
1. Pouze cvičení kolekci vyrovnávací paměti s krátkého časového intervalu zajistit neprojevuje nově přidané položky.
1. Volání identifikace/FindSimilar proti kolekci hlavní a kolekci vyrovnávací paměti a sloučení výsledky.
1. Pokud velikost vyrovnávací paměti kolekce zvyšuje prahovou hodnotu nebo nečinné současně systému, vytvořit novou kolekci vyrovnávací paměti a aktivuje train na hlavní kolekce.
1. Odstraňte staré vyrovnávací paměti kolekci po dokončení školení na hlavní kolekce.

## <a name="standalone"></a>Krok 3.3 samostatné školení

Pokud poměrně dlouho latence je přijatelná, není nutné spustit operaci Train hned po přidání nová data.
Operaci Train místo toho můžete rozdělit z hlavní logika a aktivuje pravidelně.
Tuto strategii je vhodná pro scénáře dynamických při zachování přijatelné latence a lze použít pro statické scénáře k dalšímu snížení frekvence vlaku.

Předpokládejme, že je `TrainLargePersonGroup` podobné funkce `TrainLargeFaceList`.
Typická implementace samostatné cvičení na LargePersonGroup vyvoláním [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) třídy v `System.Timers` by:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Další informace o identifikaci související implementace a správa dat naleznete v [postup přidání otočená](how-to-add-faces.md) a [jak identifikovat otočená v bitové kopii](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Souhrn

V této příručce jste se naučili, jak migrovat existující kód PersonGroup/FaceList (ne data) LargePersonGroup/LargeFaceList:

- LargePersonGroup a LargeFaceList funguje podobně jako PersonGroup nebo FaceList, s výjimkou Train operace nevyžadovala LargeFaceList malá a velká písmena.
- Trvat správné train strategie aktualizace dynamická data pro rozsáhlé datové sady.

## <a name="related"></a> Související témata

- [Jak identifikovat tyto řezy v bitové kopii](HowtoIdentifyFacesinImage.md)
- [Postup přidání řezy](how-to-add-faces.md)
