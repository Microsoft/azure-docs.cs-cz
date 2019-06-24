---
title: 'Příklad: Identifikace tváří na obrázcích – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro rozpoznávání tváře můžete použít k identifikaci tváří na obrázcích.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 1696a20094357d084ba54739767509b8d50c4ad5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341298"
---
# <a name="example-identify-faces-in-images"></a>Příklad: Identifikace tváří na obrázcích

Tato příručka ukazuje, jak se identifikovat neznámé tváří pomocí jeden objekt PersonGroup objekty, které jsou předem vytvořené od známých osob. Ukázky jsou napsané v C# pomocí klientské knihovny Azure Cognitive Services Face API.

## <a name="preparation"></a>Příprava

V této ukázce:

- Postup vytvoření jeden objekt PersonGroup. Tento jeden objekt PersonGroup obsahuje seznam známých osob.
- Jak přiřadit tváří jednotlivým uživatelům. Tyto tváří slouží jako základ k identifikaci osoby. Doporučujeme použít vymazat čelní zobrazení ploch. Příkladem je identifikátor fotky Správnou sadu fotografie obsahuje tváří tatáž osoba v různých představuje, oblečení barvy nebo hairstyles.

Provádět ukázku této ukázce připravte:

- Několik fotek s tváří určité osoby. [Stáhněte si ukázkový fotografie](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, faktury a Clare.
- Řada fotografie testu. Fotografie mohou nebo nemusí obsahovat tváří Anna, faktury nebo Clare. Slouží k otestování identifikace. Také vyberte obrázky z předchozího odkazu.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Povolit volání rozhraní API

Ke každému volání rozhraní API pro rozpoznávání tváře potřebujete klíč předplatného. Tento klíč lze předat prostřednictvím parametru řetězce dotazu nebo zadat v hlavičce požadavku. Předáním klíče předplatného pomocí řetězce dotazu, najdete v adrese URL požadavku pro [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako příklad:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Jako alternativu, zadejte klíč předplatného v hlavičce žádosti HTTP **ocp-apim-subscription-key: &lt;Klíč předplatného&gt;** .
Při použití klientské knihovny klíč předplatného předána do konstruktoru třídy FaceServiceClient. Příklad:
 
```csharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Klíč předplatného najdete na webu Azure Marketplace na webu Azure Portal. Další informace najdete v tématu [předplatná](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Vytvořte jeden objekt PersonGroup

V tomto kroku obsahuje jeden objekt PersonGroup, s názvem "MyFriends" Anna, faktury a Clare. Každá osoba má zaregistrovaných několik tváří. Musí být rozpoznány plochy z imagí. Po provedení všech těchto kroků máte skupinu PersonGroup jako na následujícím obrázku:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2.1: Definování lidí pro jeden objekt PersonGroup
Osoba je základní jednotkou identifikace. Osoba může mít zaregistrovanou jednu nebo několik známých tváří. Jeden objekt PersonGroup je kolekce uživatelů. Každý uživatel, který je definován v rámci konkrétní jeden objekt PersonGroup. Identifikace se provádí proti jeden objekt PersonGroup. Vytvoření jeden objekt PersonGroup, a pak vytvořte osob, jako je například Anna, faktury a Clare je úloha.

Nejprve vytvořte nový jeden objekt PersonGroup pomocí [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API. Odpovídající rozhraní API klientské knihovny je metoda CreatePersonGroupAsync třídy FaceServiceClient. ID skupiny, který je určen k vytvoření skupiny je jedinečný pro každé předplatné. Také můžete získat, aktualizaci nebo odstranění objektů Persongroup pomocí jiných jeden objekt PersonGroup rozhraní API. 

Po definování skupiny lidí v něm můžete definovat pomocí [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API. Metoda klientské knihovny je CreatePersonAsync. Pro každou osobu, která můžete přidat plochy po jejich vytvoření.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> Krok 2.2: Rozpoznávání tváří a zaregistrujte je správné osoby
Detekce se provádí odesláním webové žádosti POST do rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) se souborem obrázku v textu požadavku HTTP. Při použití klientské knihovně pro rozpoznávání tváře se provádí prostřednictvím DetectAsync metody pro třídu FaceServiceClient.

Pro každou tvář, který je zjištěn, volání [jeden objekt PersonGroup osobě – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) se přidá do správné osoby.

Následující kód ukazuje proces detekce tváře z obrázku a její přidání k osobě:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Pokud bitová kopie obsahuje více než jeden pro rozpoznávání tváře, přidá se jenom největší rozpoznávání tváře. Můžete přidat další tváří osobě. Předat řetězec ve formátu "targetFace = vlevo, horní, šířka, výška" k [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) parametr dotazu targetFace rozhraní API. Také můžete použít volitelný parametr targetFace AddPersonFaceAsync metody pro přidání dalších tváří. Každý obličej přidána osobě, dostane trvalých tváří jedinečné ID. Toto ID v můžete použít [jeden objekt PersonGroup osobě – odstranit tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) a [pro rozpoznávání tváře – identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Trénování jeden objekt PersonGroup

Jeden objekt PersonGroup musí školení, před provedením identifikaci usnadňuje využívání. Jeden objekt PersonGroup musí retrained po přidání nebo odebrání osoba nebo při úpravě registrované rozpoznávání tváře osoby. K trénování se používá rozhraní API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Při použití klientské knihovny je volání metody TrainPersonGroupAsync:
 
```csharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Školení je asynchronní proces. To nemusí být dokončeno i po TrainPersonGroupAsync metoda vrátí. Můžete potřebovat dotaz na stav školení. Použití [jeden objekt PersonGroup – získání stavu školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) metody rozhraní API nebo GetPersonGroupTrainingStatusAsync klientské knihovny. Následující kód ukazuje jednoduchý logiku, čekající na jeden objekt PersonGroup školení na dokončení:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Krok 4: Identifikace tváře proti definovaný jeden objekt PersonGroup

Pokud rozhraní API pro rozpoznávání tváře provádí identifikace, vypočítá podobnosti tváře testu mezi všech ploch v rámci skupiny. Vrátí největší srovnatelné osob pro testování rozpoznávání tváře. Tento proces se provádí prostřednictvím [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API nebo metodu IdentifyAsync klientské knihovny.

Pomocí předchozího postupu musí být rozpoznány testování rozpoznávání tváře. Face ID je pak předán identifikace rozhraní API jako druhý argument. Najednou můžete identifikovat více face ID. Výsledek obsahuje všechny zjištěné výsledky. Ve výchozím proces identifikace vrátí jenom jedna osoba, která nejlépe odpovídá rozpoznávání tváře testu. Pokud dáváte přednost, zadejte maxNumOfCandidatesReturned volitelný parametr, který chcete, aby proces identifikace vrátit více kandidáty.

Následující kód znázorňuje proces identifikace:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Po dokončení kroků, pokuste se identifikovat různé tváří. Podívejte se, pokud tváří Anna, faktury nebo Clare možné správně identifikovat podle obrázky nahrané pro rozpoznávání tváře. Podívejte se na následující příklady:

![Identifikace různých tváří](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5: Žádost o velkém měřítku

Jeden objekt PersonGroup může obsahovat až 10 000 osob, které jsou založené na předchozí omezení návrhu.
Další informace o scénářích s až miliónem osob najdete v článku[Způsob použití funkce ve velkém měřítku](how-to-use-large-scale.md).

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, proces vytváření jeden objekt PersonGroup a identifikaci osoby. Následující funkce byly vysvětlení a jsme vám ukázali:

- Pomocí rozpoznávání tváří [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) rozhraní API.
- Vytvoření s použitím objektů Persongroup [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API.
- S vytvořením osob [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API.
- Trénování jeden objekt PersonGroup pomocí [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) rozhraní API.
- Identifikace neznámé tváří proti jeden objekt PersonGroup pomocí [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API.

## <a name="related-topics"></a>Související témata

- [Koncepty rozpoznávání tváře](../concepts/face-recognition.md)
- [Rozpoznávání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Přidat tváří](how-to-add-faces.md)
- [Použít funkci ve velkém měřítku](how-to-use-large-scale.md)
