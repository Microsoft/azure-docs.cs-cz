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
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 2d96a04b1287033999dd5f026dd7d8d017259eb4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859042"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Příklad: Jak používat funkci ve velkém měřítku

Tento průvodce je pokročilý článek o migraci kódu za účelem vertikálního navýšení kapacity z existujících kolekcí PersonGroup a FaceList do kolekcí LargePersonGroup a LargeFaceList.
Průvodce tímto procesem migrace předpokládá, že ovládáte základy použití kolekcí PersonGroup a FaceList.
Pokud se chcete seznámit se základními operacemi, přečtěte si jeden z kurzů, například o [identifikaci tváří v obrázcích](HowtoIdentifyFacesinImage.md).

Rozhraní API pro rozpoznávání tváře nedávno vydalo dvě funkce, které podporují použití ve velkém měřítku – kolekce LargePersonGroup a LargeFaceList. Tyto kolekce se souhrnně označují jako operace ve velkém měřítku.
Kolekce LargePersonGroup může obsahovat až 1 000 000 osob, kdy každá z nich může mít až 248 tváří, a kolekce LargeFaceList může obsahovat až 1 000 000 tváří.

Operace ve velkém měřítku jsou podobné běžným kolekcím PersonGroup a FaceList, ale vzhledem k nové architektuře je mezi nimi několik významných rozdílů.
Průvodce tímto procesem migrace předpokládá, že ovládáte základy použití kolekcí PersonGroup a FaceList.
Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro rozpoznávání tváře.

Pokud chcete ve velkém měřítku povolit vyhledávání tváří pro příkazy Identification a FindSimilar, budete muset zavést operaci Train, která kolekce LargeFaceList a LargePersonGroup předzpracuje.
Doba trénování může v závislosti na kapacitě trvat pár vteřin nebo i půl hodiny.
Během trénování můžete příkazy Identification a FindSimilar stále používat, pokud jste už někdy dřív školení úspěšně dokončili.
Nevýhodou však je, že dokud se trénink ve velkém měřítku nedokončí, nově přidané osoby či tváře se ve výsledcích nezobrazí.

## <a name="concepts"></a>Koncepty

Pokud jsou pro vás následující koncepty v tomto průvodci novinka, najdete jejich definice v [glosáři](../Glossary.md):

- LargePersonGroup: Kolekce osob s kapacitou až 1 000 000.
- LargeFaceList: V kolekci tváří s kapacitou až 1 000 000.
- Regrese: Předběžné proces identifikace/FindSimilar výkon.
- Identifikace: Identifikace tváří nejméně jeden objekt PersonGroup nebo LargePersonGroup.
- FindSimilar: Vyhledávání podobných tváří z FaceList nebo LargeFaceList.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Povolit volání rozhraní API

Když používáte klientskou knihovnu rozhraní API pro rozpoznávání tváře, klíč a koncový bod předplatného se předávají prostřednictvím konstruktoru třídy FaceServiceClient. Příklad:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Klíč předplatného s odpovídajícím koncovým bodem můžete získat z webu Marketplace nebo z portálu Azure Portal.
Přečtěte si téma [Předplatná](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration-in-action"></a>Krok 2: Migrace kódu v akci

Tato část se zaměřuje pouze na migraci implementace PersonGroup/FaceList do LargePersonGroup/LargeFaceList.
Přestože návrh a interní implementace kolekcí LargePersonGroup/LargeFaceList se od PersonGroup/FaceList liší, rozhraní API pro rozpoznávání tváře jsou si s ohledem na zpětnou kompatibilitu podobné.

Migrace dat není podporovaná, místo ní budete muset kolekce LargePersonGroup/LargeFaceList znovu vytvořit.

## <a name="step-21-migrate-persongroup-to-largepersongroup"></a>Krok 2.1: Migrovat jeden objekt PersonGroup LargePersonGroup

Migrace z PersonGroup do LargePersonGroup je snadná, protože tyto kolekce sdílí na úrovni skupiny úplně stejné operace.

U implementace týkající se PersonGroup/Person je potřeba změnit pouze cesty rozhraní API nebo třídu/modul sady SDK na LargePersonGroup a LargePersonGroup Person.

Pokud jde o migraci dat, přečtěte si článek o [přidání obličejů](how-to-add-faces.md).

## <a name="step-22-migrate-facelist-to-largefacelist"></a>Krok 2.2: Migrace FaceList LargeFaceList

| Rozhraní API kolekce FaceList | Rozhraní API kolekce LargeFaceList |
|:---:|:---:|
| Vytvořit | Vytvořit |
| Odstranění | Odstranění |
| Získat | Získat |
| Seznam | Seznam |
| Aktualizace | Aktualizace |
| - | Trénování |
| - | Get Training Status |

Předchozí tabulka je srovnáním operací na úrovni seznamu mezi kolekcemi FaceList a LargeFaceList.
Jak můžete vidět, kolekce LargeFaceList přináší v porovnání s kolekcí FaceList nové operace Train a Get Training Status.
Kolekce FaceList operaci Train nevyžaduje, ale trénování kolekce LargeFaceList je podmínkou pro operaci [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).
Následující fragment kódu je pomocná funkce, která počká na trénování kolekce LargeFaceList.

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

Dříve by typické použití kolekce FaceList s přidáním tváří a použitím operace FindSimilar vypadalo takto:

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

Při migraci do kolekce LargeFaceList by to mělo vypadat takto:

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

Jak je vidět výše, správa dat a operace FindSimilar jsou téměř stejné.
Jediným rozdílem je, že kolekce LargeFaceList k fungování operace FindSimilar vyžaduje aktuální předzpracování operací Train.

## <a name="step-3-train-suggestions"></a>Krok 3: Trénování návrhy

Přestože operace Train urychluje operace [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), doba trénování může zejména při přechodu na velké měřítko trvat.
Odhadovanou dobu trénování najdete podle měřítka v následující tabulce:

| Měřítko (tváře nebo osoby) | Odhadovaná doba trénování |
|:---:|:---:|
| 1 000 | 1–2 s |
| 10 000 | 5–10 s |
| 100 000 | 1–2 min |
| 1 000 000 | 10–30 min |

Pokud chcete funkce velkého měřítka lépe využít, doporučujeme zvážit některé z následujících strategií.

## <a name="step-31-customize-time-interval"></a>Krok 3.1: Upravit časový Interval

Jak je vidět v příkazu `TrainLargeFaceList()`, existuje parametr `timeIntervalInMilliseconds`, který odloží nekončící ověřování stavu trénování.
U kolekce LargeFaceList s více tvářemi bude použití delšího intervalu znamenat nižší počet volání i náklady.
Časový interval byste měli přizpůsobit podle očekávané kapacity kolekce LargeFaceList.

Stejná strategie platí taky u kolekce LargePersonGroup.
Když například trénujete kolekci LargePersonGroup s 1 000 000 osob, parametr `timeIntervalInMilliseconds` by mohl mít hodnotu 60 000 (tedy jednominutový interval).

## <a name="step-32-small-scale-buffer"></a>Krok 3.2 Malá vyrovnávací paměť

Osoby nebo tváře můžete v kolekci LargePersonGroup či LargeFaceList vyhledávat pouze po trénování.
V dynamickém scénáři se mohou nové osoby či tváře přidávat neustále a může být potřeba je okamžitě dohledat, ale trénink může trvat příliš dlouho.
Tento problém můžete zmírnit použitím velmi malé kolekce LargePersonGroup či LargeFaceList, která bude sloužit jako vyrovnávací paměť pro nově přidané položky.
Trénování této vyrovnávací paměti bude trvat kratší dobu, protože je mnohem menší, takže okamžité vyhledávání by zde mělo fungovat.
Tuto vyrovnávací paměť můžete použít v kombinaci s tréninkem hlavní kolekce LargePersonGroup či LargeFaceList a spouštět hlavní trénování v mnohem delších intervalech – například jednou denně uprostřed noci.

Příklad pracovního postupu:
1. Vytvořte hlavní kolekci LargePersonGroup nebo LargeFaceList a kolekci vyrovnávací paměti LargePersonGroup nebo LargeFaceList. Kolekce vyrovnávací paměti slouží pouze k přidávání nově přidaných osob nebo tváří.
1. Přidejte nové osoby či tváře do hlavní kolekce i kolekce vyrovnávací paměti.
1. Trénujte pouze kolekci vyrovnávací paměti a použijte krátký interval, abyste měli jistotu, že nově přidané položky bude možné vyhledat.
1. Zavolejte příkaz Identification nebo FindSimilar jak u hlavní kolekce, tak i u kolekce vyrovnávací paměti, a výsledky slučte.
1. Když se bude velikost kolekce vyrovnávací paměti blížit prahové hodnotě nebo když bude systém nečinný, vytvořte novou vyrovnávací paměť a spusťte trénování hlavní kolekce.
1. Po dokončení trénování hlavní kolekce kolekci vyrovnávací paměti odstraňte.

## <a name="step-33-standalone-training"></a>Krok 3.3 Samostatné trénování

Pokud je poměrně dlouhá latence přijatelná, není nutné trénování spouštět ihned po přidání nových dat.
Místo toho můžete operaci Train oddělit od hlavní logiky a spouštět ji pravidelně.
Tato strategie je vhodná u dynamických scénářů s přijatelnou latencí a můžete ji použít u statických scénářů, abyste frekvenci trénování ještě víc snížili.

Předpokládejme, že existuje funkce `TrainLargePersonGroup` podobná funkci `TrainLargeFaceList`.
Typická implementace samostatného trénování kolekce LargePersonGroup vyvoláním třídy [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx)
v `System.Timers` by vypadala takto:

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

Další informace o správě dat a implementacích souvisejících s identifikací naleznete v článcích o [přidávání tváří](how-to-add-faces.md) a [identifikaci tváří v obrázku](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili, jak migrovat existující kód (ne data) kolekce PersonGroup nebo FaceList do kolekce LargePersonGroup či LargeFaceList:

- Kolekce LargePersonGroup a LargeFaceList fungují podobně jako kolekce PersonGroup a FaceList, ale kolekce LargeFaceList vyžaduje operaci Train.
- Dynamická aktualizace dat vyžaduje u rozsáhlých datových sad vhodnou strategii.

## <a name="related-topics"></a>Související témata

- [Postup identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
- [Postup přidání tváří](how-to-add-faces.md)
