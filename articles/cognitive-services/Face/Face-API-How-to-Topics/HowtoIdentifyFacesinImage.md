---
title: 'Příklad: identifikace plošeks na obrázcích – Face'
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak identifikovat neznámé plošky pomocí objektů Person, které jsou vytvořené od známých osob předem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.openlocfilehash: 607f67258c5d069590f934891c09ccada780c977
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "85918753"
---
# <a name="example-identify-faces-in-images"></a>Příklad: identifikace plošek na obrázcích

V této příručce se dozvíte, jak identifikovat neznámé plošky pomocí objektů Person, které jsou vytvořené od známých osob předem. Ukázky jsou napsány v jazyce C# pomocí klientské knihovny Azure Cognitive Services Face.

Tato ukázka demonstruje:

- Jak vytvořit objekt Person. Tento objekt Person obsahuje seznam známých lidí.
- Jak přiřadit obličeje všem uživatelům. Tyto obličeje slouží jako základ k identifikaci osob. Doporučujeme, abyste používali jasná přední zobrazení obličeje. Příkladem je ID fotografie. Dobrá sada fotek zahrnuje i plošky stejné osoby v různých případech, oděvech nebo hairstyles.

## <a name="prerequisites"></a>Předpoklady
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku** a zkopírujte svůj klíč.
* Pár fotek u ploch identifikovaných lidí. [Stáhněte si ukázkové fotky](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) pro Anna, Bill a Clare.
* Série testovacích fotek. Fotografie mohou nebo nemusí obsahovat plošky identifikovaných osob. Použijte některé obrázky z odkazu ukázka fotky.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `face-identify` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autorizace volání rozhraní API

Ke každému volání rozhraní API pro rozpoznávání tváře potřebujete klíč předplatného. Tento klíč lze buď předat parametrem řetězce dotazu, nebo zadat v hlavičce požadavku. Když použijete klientskou knihovnu, klíč předplatného se předává prostřednictvím konstruktoru třídy **FaceClient** . Do metody **Main** souboru *program.cs* přidejte následující kód.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Krok 2: Vytvoření skupiny PersonGroup

V tomto kroku obsahuje **osoba** s názvem "MyFriends" Anna, fakturaci a Clare. Každá osoba má zaregistrovaných několik tváří. Na obrázcích musí být zjištěny plošky. Po všech těchto krocích máte **osobu** , podobně jako na následujícím obrázku:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2,1: Definujte lidi pro uživatele
Osoba je základní jednotkou identifikace. Osoba může mít zaregistrovanou jednu nebo několik známých tváří. Skupina **Person** je kolekce lidí. Každá osoba je definována v rámci konkrétní **osoby**. Identifikace se provádí proti **osobě typu osoba**. Úkolem je vytvořit **osobu**a potom v ní vytvořit lidi, jako je Anna, faktura a Clare.

Nejdřív vytvořte novou samostatnou **osobu** pomocí rozhraní API [pro vytvoření osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) . Odpovídající rozhraní API klientské knihovny je metoda **CreatePersonGroupAsync** třídy **FaceClient** . ID skupiny, která je určená k vytvoření skupiny, je pro každé předplatné jedinečné. Pomocí jiných rozhraní API pro **uživatele** můžete také získat, aktualizovat nebo odstranit **uživatele**. 

Po definování skupiny můžete v ní definovat lidi pomocí rozhraní [Person person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) . Metoda klientské knihovny je **CreatePersonAsync**. Po vytvoření můžete pro každou osobu přidat plošku.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Krok 2,2: detekce plošek a jejich registrace pro správnou osobu
Detekce se provádí odesláním webové žádosti POST do rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) se souborem obrázku v textu požadavku HTTP. Když použijete klientskou knihovnu, detekce obličeje se provádí pomocí jednoho z těchto detekcí. Asynchronní metody třídy FaceClient

Pro každou nalezenou plošku zavolejte [osobu person – přidejte plochu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) , abyste ji mohli přidat ke správné osobě.

Následující kód ukazuje proces detekce tváře z obrázku a její přidání k osobě:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Pokud obrázek obsahuje více než jednu plošku, je přidána pouze největší plocha. K osobě můžete přidat další plošky. Předejte řetězec ve formátu "targetFace = Left, Top, Width, Height" na person [Person-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a targetFace parametr dotazu rozhraní API pro rozpoznávání tváře. K přidání dalších ploch můžete použít také volitelný parametr targetFace pro metodu AddPersonFaceAsync. Každé ploškě přidané uživateli se předali jedinečné trvalé ID obličeje. Toto ID můžete použít v [osobě person – odstranit obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) a [tvář – identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Trénování skupiny PersonGroup

Aby bylo možné provést identifikaci, musí být tato **osoba** vyškolena, aby ji mohl použít. Po přidání nebo odebrání jakékoli osoby nebo při úpravě registrovaného obličeje osoby musí být **osoba** členem převlakovaná. K trénování se používá rozhraní API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Při použití klientské knihovny se jedná o volání metody **TrainPersonGroupAsync** :
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Školení je asynchronní proces. Nemusí být dokončen ani po návratu metody **TrainPersonGroupAsync** . Možná budete muset zadat dotaz na stav školení. Použijte rozhraní API pro [stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) nebo metodu **GetPersonGroupTrainingStatusAsync** klientské knihovny. Následující kód demonstruje jednoduchou logiku čekání na dokončení školení **Persone** :
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Krok 4: Identifikace tváře vůči definované skupině PersonGroup

Když služba obličeje provede identifikace, vypočítá podobnost zkušební plochy mezi všemi ploškami v rámci skupiny. Vrátí nejsrovnatelnou osobu pro testovací plochu. Tento proces se provádí prostřednictvím rozhraní API [pro identifikaci obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) nebo metody IdentifyAsync klientské knihovny.

Test FACET se musí detekovat pomocí předchozích kroků. Pak je ID obličeje předáno rozhraní API pro identifikaci jako druhý argument. V jednom okamžiku se dá identifikovat víc ID tváře. Výsledek obsahuje všechny zjištěné výsledky. Ve výchozím nastavení se v procesu identifikace vrátí pouze jedna osoba, která se shoduje s nejlepší testovou ploškou. Pokud dáváte přednost, zadejte nepovinný parametr _maxNumOfCandidatesReturned_ , aby proces identifikace mohl vrátit další kandidáty.

Následující kód demonstruje proces identifikace:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Po dokončení kroků se pokuste identifikovat různé plošky. Podívejte se, jestli se plošky Anna, Bill nebo Clare dají správně identifikovat podle imagí nahraných pro detekci obličeje. Podívejte se na následující příklady:

![Identifikace různých plošek](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5: požadavek na velké měřítko

**Osoba** může na základě předchozího omezení návrhu obsahovat až 10 000 osob. Další informace o scénářích s až miliónem osob najdete v článku[Způsob použití funkce ve velkém měřítku](how-to-use-large-scale.md).

## <a name="summary"></a>Shrnutí

V této příručce jste se dozvěděli o procesu vytvoření **osoby** a identifikaci osoby. Následující funkce byly vysvětleny a ukázaly:

- Rozpoznávání plošek pomocí rozhraní API [pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Vytvořte objektů persongroup pomocí rozhraní API [Person-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Vytvořte osoby pomocí uživatele [Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Pomocí rozhraní API pro poučení s využitím sady [Person –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) vlak.
- Identifikujte neznámé plošky proti osobě pomocí rozhraní API [pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="related-topics"></a>Související témata

- [Koncepce rozpoznávání obličeje](../concepts/face-recognition.md)
- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
- [Přidání tváří](how-to-add-faces.md)
- [Použití funkce ve velkém měřítku](how-to-use-large-scale.md)
