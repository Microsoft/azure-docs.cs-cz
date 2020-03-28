---
title: 'Příklad: Identifikace tváří v obrázcích – plocha'
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak identifikovat neznámé tváře pomocí PersonGroup objekty, které jsou vytvořeny z známých osob předem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169917"
---
# <a name="example-identify-faces-in-images"></a>Příklad: Identifikace tváří v obrázcích

Tato příručka ukazuje, jak identifikovat neznámé tváře pomocí PersonGroup objekty, které jsou vytvořeny z známých osob předem. Ukázky jsou zapsány v C# pomocí knihovny klienta Azure Cognitive Services Face.

## <a name="preparation"></a>Příprava

Tento vzorek ukazuje:

- Jak vytvořit PersonGroup. Tato persongroup obsahuje seznam známých osob.
- Jak přiřadit tváře každé osobě. Tyto plochy se používají jako směrný plán k identifikaci osob. Doporučujeme používat jasné čelní pohledy na plochy. Příkladem je id fotografie. Dobrá sada fotografií obsahuje tváře stejné osoby v různých pózách, barvách oblečení nebo účesech.

Pro provedení předvádění tohoto vzorku se připraví:

- Několik fotek s tváří určité osoby. [Stáhněte si ukázkové fotografie](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) pro Anna, Bill a Clare.
- Série testovacích fotografií. Fotografie mohou nebo nemusí obsahovat tváře Anny, Billa nebo Clare. Používají se k testování identifikace. Vyberte také některé ukázkové obrázky z předchozího odkazu.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autorizace volání rozhraní API

Ke každému volání rozhraní API pro rozpoznávání tváře potřebujete klíč předplatného. Tento klíč může být předán parametrem řetězce dotazu nebo zadán v hlavičce požadavku. Pokud chcete předat klíč předplatného prostřednictvím řetězce dotazu, podívejte se na adresu URL požadavku pro [face - detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako příklad:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Jako alternativu zadejte klíč odběru v hlavičce požadavku HTTP **ocp-apim-subscription-key: &lt;Klíč předplatného&gt;**.
Při použití klientské knihovny je klíč předplatného předán prostřednictvím konstruktoru třídy FaceClient. Například:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Pokud chcete získat klíč předplatného, přejděte na Azure Marketplace z webu Azure Portal. Další informace naleznete v [tématu Předplatná](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Vytvoření skupiny PersonGroup

V tomto kroku persongroup s názvem "MyFriends" obsahuje Anna, Bill a Clare. Každá osoba má zaregistrovaných několik tváří. Plochy musí být detekovány z obrázků. Po provedení všech těchto kroků máte skupinu PersonGroup jako na následujícím obrázku:

![Moji přátelé](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2.1: Definování osob pro skupinu osob
Osoba je základní jednotkou identifikace. Osoba může mít zaregistrovanou jednu nebo několik známých tváří. PersonGroup je kolekce lidí. Každá osoba je definována v rámci určité skupiny PersonGroup. Identifikace se provádí proti PersonGroup. Úkolem je vytvořit PersonGroup a potom vytvořit osoby v ní, jako je například Anna, Bill a Clare.

Nejprve vytvořte novou PersonGroup pomocí [PersonGroup - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API. Odpovídající rozhraní API klientské knihovny je metoda CreatePersonGroupAsync pro třídu FaceClient. ID skupiny, které je určeno k vytvoření skupiny, je jedinečné pro každé předplatné. PersonGroups můžete také získat, aktualizovat nebo odstranit pomocí jiných api persongroup. 

Po definování skupiny můžete definovat osoby v ní pomocí [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Metoda klientské knihovny je CreatePersonAsync. Po vytvoření můžete každé osobě přidat obličej.

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
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Krok 2.2: Detekce tváří a jejich registrace na správnou osobu
Detekce se provádí odesláním webové žádosti POST do rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) se souborem obrázku v textu požadavku HTTP. Při použití klientské knihovny, detekce obličeje se provádí prostřednictvím jednoho z Detect.. Asynchronní metody třídy FaceClient.

Pro každou zjištěnou plochu zavolejte [persongroup person – přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a přidat ji ke správné osobě.

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
Pokud obraz obsahuje více než jednu plochu, přidá se pouze největší plocha. K osobě můžete přidat další tváře. Předaj te řetězec ve formátu "targetFace = left, top, width, height" [persongroup person - add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) api's targetFace query parametr. Můžete také použít targetFace volitelný parametr pro AddPersonFaceAsync metoda přidat další tváře. Každá tvář přidaná osobě dostane jedinečné trvalé ID obličeje. Toto ID můžete použít v [persongroup person – odstranit obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) a obličej – [identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Trénování skupiny PersonGroup

PersonGroup musí být vyškoleni před identifikací lze provést pomocí. PersonGroup musí být retrained po přidání nebo odebrání jakékoli osoby, nebo pokud upravíte osoby registrované tvář. K trénování se používá rozhraní API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Při použití klientské knihovny je volání trainPersonGroupAsync metoda:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Školení je asynchronní proces. Nemusí být dokončena i po TrainPersonGroupAsync metoda vrátí. Možná budete muset dotaz na stav školení. Použijte [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API nebo GetPersonGroupTrainingStatusAsync metoda klientské knihovny. Následující kód ukazuje jednoduchou logiku čekání na dokončení školení PersonGroup:
 
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

Když služba Face provádí identifikaci, vypočítá podobnost testovací plochy mezi všemi plochami ve skupině. Vrátí nejvíce srovnatelné osoby pro testovací tvář. Tento proces se provádí prostřednictvím [Face - Identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API nebo IdentifyAsync metoda klientské knihovny.

Testovací plocha musí být detekována pomocí předchozích kroků. Potom id obličeje je předán a identifikační rozhraní API jako druhý argument. Najednou lze identifikovat více id obličeje. Výsledek obsahuje všechny identifikované výsledky. Ve výchozím nastavení vrátí proces identifikace pouze jednu osobu, která nejlépe odpovídá testovací ploše. Pokud dáváte přednost, zadejte volitelný parametr maxNumOfCandidatesReturned, aby proces identifikace vrátil více kandidátů.

Následující kód ukazuje proces identifikace:

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

Po dokončení kroků se pokuste identifikovat různé plochy. Podívejte se, jestli tváře Anny, Billa nebo Clare mohou být správně identifikovány podle obrázků nahraných pro detekci obličeje. Podívejte se na následující příklady:

![Identifikace různých ploch](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5: Žádost o rozsáhlé měřítko

PersonGroup může pojmout až 10 000 osob na základě předchozího omezení návrhu.
Další informace o scénářích s až miliónem osob najdete v článku[Způsob použití funkce ve velkém měřítku](how-to-use-large-scale.md).

## <a name="summary"></a>Souhrn

V této příručce jste se dozvěděli proces vytváření PersonGroup a identifikaci osoby. Byly vysvětleny a demonstrovány následující funkce:

- Detekujte tváře pomocí [rozhraní Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API.
- Vytvořte persongroups pomocí [PersonGroup - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API.
- Vytvořte osoby pomocí [persongroup person - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API.
- Trénování PersonGroup pomocí [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifikujte neznámé tváře proti PersonGroup pomocí [Face - Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API.

## <a name="related-topics"></a>Související témata

- [Koncepty rozpoznávání tváří](../concepts/face-recognition.md)
- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
- [Přidání tváří](how-to-add-faces.md)
- [Použití funkce ve velkém měřítku](how-to-use-large-scale.md)
