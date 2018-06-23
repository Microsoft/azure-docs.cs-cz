---
title: Určit tyto řezy v bitové kopie s rozhraním API vzhled | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Použijte rozhraní API řez v kognitivní služby k identifikaci řezy do bitových kopií.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343700"
---
# <a name="how-to-identify-faces-in-images"></a>Jak identifikovat tyto řezy v obrázcích

Tato příručka ukazuje, jak identifikovat neznámou řezy pomocí PersonGroups, které jsou předem vytvořené pomocí známých osob. Ukázky jsou napsané v C# pomocí klientské knihovny vzhled rozhraní API.

## <a name="concepts"></a> Koncepty

Pokud nejste obeznámeni s následující koncepty v této příručce, vyhledejte definice v našem [Glosář](../Glossary.md) kdykoli:

- Čelí – zjištění
- Čelí – identifikace
- PersonGroup

## <a name="preparation"></a> Příprava

V této ukázce ukazují následující:

- Postup vytvoření PersonGroup - tento PersonGroup obsahuje seznam známých osob.
- Jak přiřadit řezy každá osoba - tyto řezy slouží jako základ k identifikaci osoby. Doporučuje se použít zrušte přední plochy, stejně jako ID vašeho fotografii. Řada fotografie by mělo obsahovat řezy stejná osoba v různých představuje, se oblečení barvy nebo kříž styly.

K provedení této ukázky ukázky, je nutné připravit spoustu obrázky:

- Několik fotografie s obličeje. [Kliknutím sem stáhnete ukázka fotografie](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, faktury a Clare.
- Řadu testovací fotografie, které mohou nebo nemusí obsahovat řezy Anna, faktury nebo Clare použít k testování identifikace. Můžete také vybrat některé ukázkové obrázky z předchozí odkaz.

## <a name="step1"></a> Krok 1: Autorizovat volání rozhraní API

Každé volání rozhraní API vzhled vyžaduje klíč předplatného. Tento klíč může být buď předána parametr řetězce dotazu nebo zadaným v hlavičce žádosti. Předat klíč předplatného prostřednictvím řetězec dotazu, naleznete na adrese URL žádosti pro [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako příklad:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Jako alternativu, klíč předplatného můžete také uvést v hlavičce požadavku HTTP: **ocp-apim-subscription-key: &lt;klíč předplatného&gt;**  při použití klientské knihovny, je předaná klíč předplatného pomocí konstruktoru třídy FaceServiceClient. Příklad:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Klíč předplatného je možné získat ze stránky portálu Azure Marketplace. V tématu [odběry](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> Krok 2: Vytvoření PersonGroup

V tomto kroku jsme vytvořili PersonGroup, s názvem "MyFriends", který obsahuje tři osoby: Anna, faktury a Clare. Každý uživatel má několik řezy zaregistrován. Tyto řezy musí být zjištěna z bitové kopie. Po všech těchto kroků, máte PersonGroup jako na následujícím obrázku:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 definovat osoby pro PersonGroup
Uživatel je základní jednotkou identifikace. Uživatel může mít jeden nebo více známé řezy, které jsou registrované. Ale PersonGroup je kolekce uživatelů, a každá osoba je definována v rámci konkrétní PersonGroup. Identifikace provádí proti PersonGroup. Ano je vytvořte PersonGroup a pak vytvořte osoby v něm například Anna, faktury a Clare úlohu.

Nejprve musíte vytvořit nový PersonGroup. Tím se spustí pomocí [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API. Odpovídající klientské knihovny rozhraní API je metoda CreatePersonGroupAsync pro třídu FaceServiceClient. Zadané ID skupiny pro vytvoření skupiny je jedinečný pro každé předplatné – můžete také získat, aktualizovat nebo odstranit PersonGroups pomocí jiných PersonGroup rozhraní API. Po definování skupinu osob určeny pak v něm pomocí [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API. Metodu klientské knihovny je CreatePersonAsync. Po vytváří, můžete každému uživateli přidat řez.

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
### <a name="step2-2"></a> 2.2 zjistit řezy a registrujte je správný osobě
Detekce se provádí odesláním webový požadavek na "POST" [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API pomocí souboru bitové kopie v textu požadavku HTTP. Pokud pomocí klientské knihovny, je detekce vzhled spustit prostřednictvím DetectAsync metody pro třídu FaceServiceClient.

Pro každý řez nalezené můžete volat [PersonGroup osoba – přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) tím ho přidáte do požadované osoby.

Následující kód ukazuje proces o tom, jak zjistit řez z bitové kopie a přidat ji do osoby:
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
Všimněte si, že pokud bitová kopie obsahuje více než jeden řez, se přidá jenom největší písmo. Můžete přidat další řezy osobě předáním řetězec ve formátu "targetFace = vlevo, top, šířka a výška" k [PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API je parametr dotazu targetFace, nebo pomocí targetFace volitelný parametr pro Metoda AddPersonFaceAsync přidat další řezy. Každý řez přidat osobě, bude mu udělená jedinečná čelní strany trvalou ID, které mohou být používány [PersonGroup osoba – odstranit vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) a [čelí – identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> Krok 3: Cvičení PersonGroup

PersonGroup musí cvičení, než identifikaci je možné provést pomocí ho. Kromě toho musí být retrained po přidání nebo odebrání každá osoba nebo osoba, která má své registrované vzhled upravit. Je potřeba se školení [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) rozhraní API. Při použití klientské knihovny, je jednoduše volání metody TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Školení je asynchronní proces. Nemusí být dokončena, i když metoda TrainPersonGroupAsync vrátila. Budete muset zadat dotaz na stav školení podle [PersonGroup - získání stavu školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) rozhraní API nebo GetPersonGroupTrainingStatusAsync metoda klientské knihovny. Následující kód ukazuje jednoduchý logiky na čekající PersonGroup školení k dokončení:
 
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

## <a name="step4"></a> Krok 4: Určení řez proti definované PersonGroup
Při provádění identifikátorů, rozhraní API vzhled můžete vypočítat podobnosti tváře testovací mezi tyto řezy v rámci skupiny a vrátí nejvíce porovnatelný osobu nebo osoby pro tento testování řez. To se provádí prostřednictvím [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API nebo metodu IdentifyAsync klientské knihovny.

Testování tučné musí být zjištěna v předchozích krocích, a pak identifikace rozhraní API jako druhý argument předaný ID vzhled. Najednou lze identifikovat více vzhled ID a výsledek bude obsahovat všechny výsledky identifikace. Ve výchozím nastavení vrátí identitu jenom jedna osoba, který nejlépe odpovídá tučné test. Pokud dáváte přednost, můžete zadat volitelný parametr maxNumOfCandidatesReturned umožníte identifikace vrátit kandidátů na další.

Následující kód ukazuje proces identifikace:
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

Po dokončení kroků, můžete použít k identifikaci různých řezy a zobrazit, pokud tyto řezy Anna, faktury nebo Clare možné správně identifikovat, podle bitové kopie nahrál pro zjišťování vzhled. Podívejte se na následující příklady:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> Krok 5: Žádost o ve velkém měřítku

Protože je znám, mohou být uloženy až 10 000 osob kvůli omezení předchozí návrhu PersonGroup.
Další informace o až do milionů škálování scénáře, najdete v části [jak používat funkci ve velkém měřítku](how-to-use-large-scale.md).

## <a name="summary"></a> Souhrn

V této příručce jste se naučili proces vytváření PersonGroup a identifikaci osoby. Tady jsou rychlé připomenutí funkcí dříve vysvětlené a ukázán:

- Zjišťování otočená pomocí [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) rozhraní API
- Vytváření s použitím PersonGroups [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API
- Vytvoření osob pomocí [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API
- Cvičení PersonGroup pomocí [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) rozhraní API
- Identifikace neznámé řezy před použitím PersonGroup [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) rozhraní API

## <a name="related"></a> Související témata

- [Jak zjistit, kterým čelí v bitové kopii](HowtoDetectFacesinImage.md)
- [Postup přidání řezy](how-to-add-faces.md)
- [Jak používat funkci ve velkém měřítku](how-to-use-large-scale.md)
