---
title: 'Příklad: Identifikace tváří na obrázcích – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro rozpoznávání tváře můžete použít k identifikaci tváří na obrázcích.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: c61852763353189321b8f98711928e0e8b3a389d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208087"
---
# <a name="example-how-to-identify-faces-in-images"></a>Příklad: Postup identifikace tváří na obrázcích

Tato příručka ukazuje, jak identifikovat neznámé tváře pomocí skupin PersonGroup, které jsou předem vytvořené ze známých osob. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro rozpoznávání tváře.

## <a name="concepts"></a>Koncepty

Pokud nejste obeznámeni s následujícími koncepty v této příručce, můžete si kdykoli vyhledat definice v našem [glosáři](../Glossary.md):

- Face - Detect
- Face - Identify
- PersonGroup

## <a name="preparation"></a>Příprava

V této ukázce demonstrujeme:

- Jak vytvořit skupinu PersonGroup – tato skupina PersonGroup obsahuje seznam známých osob.
- Jak přiřadit tváře k jednotlivým osobám – tyto tváře slouží jako základ k identifikaci osob. Doporučuje se použít zřetelné tváře zabrané zepředu podobně jako na občanském průkazu. Správná množina fotek by měla obsahovat tváře stejné osoby v různých pozicích, barvách oblečení nebo účesech.

Abyste mohli provést demonstraci této ukázky, musíte si připravit sadu obrázků:

- Několik fotek s tváří určité osoby. [Kliknutím sem si stáhnete ukázkové fotky](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anny, Billa a Clare.
- Sérii fotek, které mohou ale nemusí obsahovat tváře Anny, Billa nebo Clare, sloužící k testu identifikace. Můžete také vybrat některé obrázky z předchozího odkazu.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Povolit volání rozhraní API

Ke každému volání rozhraní API pro rozpoznávání tváře potřebujete klíč předplatného. Tento klíč lze předat buď jako řetězcový parametr dotazu, nebo uvést v hlavičce žádosti. Pokud chcete klíč předplatného předat pomocí řetězce dotazu, použijte jako příklad adresu URL žádosti pro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Jako alternativu, klíč předplatného také dá se zadat v hlavičce požadavku protokolu HTTP: **ocp-apim-subscription-key: &lt;Klíč předplatného&gt;**  při použití klientské knihovny, klíč předplatného se předává v konstruktoru třídy FaceServiceClient. Příklad:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Klíč předplatného můžete získat z webu Marketplace nebo z portálu Azure Portal. Přečtěte si téma [Předplatná](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Vytvořte jeden objekt PersonGroup

V tomto kroku jsme vytvořili jeden objekt PersonGroup, s názvem "MyFriends", který obsahuje tři lidi: Anna, faktury a Clare. Každá osoba má zaregistrovaných několik tváří. Tyto tváře se musí detekovat z obrázků. Po provedení všech těchto kroků máte skupinu PersonGroup jako na následujícím obrázku:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Definování osob pro skupinu PersonGroup
Osoba je základní jednotkou identifikace. Osoba může mít zaregistrovanou jednu nebo několik známých tváří. PersonGroup je ale kolekce osob, přičemž každá osoba je definovaná v konkrétní skupině PersonGroup. Identifikace se provádí vůči skupině PersonGroup. Úkolem je tedy vytvořit skupinu PersonGroup a pak v ní vytvořit osoby, jako je Anna, Bill a Clare.

Napřed musíte vytvořit novou skupinu PersonGroup. K tomu slouží rozhraní API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). Odpovídající rozhraní API klientské knihovny je metoda CreatePersonGroupAsync třídy FaceServiceClient. ID skupiny zadané pro vytvoření této skupiny je jedinečné pro každé předplatné – skupiny PersonGroup můžete také získat, aktualizovat nebo odstranit pomocí jiných rozhraní API PersonGroup. Jakmile je skupina definovaná, mohou v ní pak být definované osoby pomocí rozhraní API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). Metoda klientské knihovny je CreatePersonAsync. Po vytvoření můžete k jednotlivým osobám přidat tvář.

```CSharp 
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
### <a name="step2-2"></a> 2.2 Detekce tváří a jejich registrace ke správné osobě
Detekce se provádí odesláním webové žádosti POST do rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) se souborem obrázku v textu požadavku HTTP. Při použití klientské knihovny se detekce tváří provádí prostřednictvím metody DetectAsync třídy FaceServiceClient.

U každé detekované tváře ji můžete voláním [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) přidat ke správné osobě.

Následující kód ukazuje proces detekce tváře z obrázku a její přidání k osobě:
```CSharp 
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
Pokud obrázek obsahuje více než jednu tvář, přidá se jen největší tvář. Další tváře můžete osobě přidat předáním řetězce ve formátu „targetFace = vlevo, nahoře, šířka, výška“ do parametru dotazu targetFace rozhraní API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), nebo pomocí volitelného parametru targetFace metody AddPersonFaceAsync pro přidání dalších tváří. Každá tvář přidaná osobě dostane jedinečné trvalé ID tváře, které lze následně použít v [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) a [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Trénování jeden objekt PersonGroup

Předtím, než lze skupinu PersonGroup použít k identifikaci, je potřeba ji vytrénovat. Po přidání nebo odebrání jakékoli osoby nebo při úpravě registrované tváře osoby se navíc musí vytrénovat znovu. K trénování se používá rozhraní API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Při použití této klientské knihovny jde o jednoduché volání metody TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Trénink je asynchronní proces. Nemusí být dokončený ani po vrácení metody TrainPersonGroupAsync. Na stav tréninku se můžete dotázat pomocí rozhraní API [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) nebo metody GetPersonGroupTrainingStatusAsync klientské knihovny. Následující kód ukazuje jednoduchou logiku čekání na dokončení tréninku skupiny PersonGroup:
 
```CSharp 
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

Při provádění identifikací dokáže rozhraní API pro rozpoznávání tváře vypočítat podobnost testovací tváře mezi všemi tvářemi ve skupině a vrátí osobu (osoby) nejpodobnější testovací tváři. To se provádí pomocí rozhraní API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) nebo metody IdentifyAsync klientské knihovny.

Testovací tvář musí být detekovaná pomocí předchozího postupu a následně se ID tváře předá identifikačnímu rozhraní API jako druhý argument. Najednou lze identifikovat více ID tváře a výsledek bude obsahovat všechny výsledky identifikace. Identifikace vrací standardně jen jednu osobu, která se nejlépe shoduje s testovací tváří. Pokud chcete, můžete zadáním nepovinného parametru maxNumOfCandidatesReturned nechat identifikaci vrátit více kandidátů.

Následující kód znázorňuje proces identifikace:

```CSharp 
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

Až tento postup dokončíte, můžete se pokusit identifikovat různé tváře a zjistit, jestli se tváře Anny, Billa a Clare správně identifikují podle obrázků nahraných pro detekci tváře. Podívejte se na následující příklady:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5: Žádost o ve velkém měřítku

Je známo, že skupina PersonGroup může kvůli omezením předchozí návrhu obsahovat až 10 000 osob.
Další informace o scénářích s až miliónem osob najdete v článku[Způsob použití funkce ve velkém měřítku](how-to-use-large-scale.md).

## <a name="summary"></a>Souhrn

V této příručce jste se naučili postup vytvoření skupiny PersonGroup a identifikace osoby. Následující odkazy slouží jako rychlé připomenutí dříve vysvětlených funkcí:

- Detekce tváří pomocí rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Vytvoření skupin PersonGroup pomocí rozhraní API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Vytvoření osob pomocí rozhraní API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Trénování skupiny PersonGroup pomocí rozhraní API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identifikace neznámých tváří vůči skupině PersonGroup pomocí rozhraní API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Související témata

- [Způsob rozpoznání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Způsob přidání tváří](how-to-add-faces.md)
- [Způsob použití funkce ve velkém měřítku](how-to-use-large-scale.md)
