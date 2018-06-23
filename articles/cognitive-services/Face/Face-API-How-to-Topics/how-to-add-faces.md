---
title: Přidat tyto řezy s rozhraním API vzhled | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Použijte rozhraní API řez v kognitivní služby přidat řezy do bitové kopie.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342428"
---
# <a name="how-to-add-faces"></a>Postup přidání řezy

Tato příručka ukazuje osvědčený postup pro přidání do PersonGroup masivní počet osob a řezy.
Stejné strategie platí také pro FaceList a LargePersonGroup.
Ukázky jsou napsané v C# pomocí klientské knihovny vzhled rozhraní API.

## <a name="step1"></a> Krok 1: Inicializace

Několik proměnné jsou deklarovány a pomocné funkce je implementována při plánování požadavků.

- `PersonCount` je celkový počet lidí.
- `CallLimitPerSecond` je maximální počet volání za sekundu podle úrovně předplatného.
- `_timeStampQueue` je fronty k zaznamenání požadavku časová razítka.
- `await WaitCallLimitPerSecondAsync()` bude Počkejte, dokud je platná odeslat další požadavek.

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step2"></a> Krok 2: Autorizovat volání rozhraní API

Při použití klientské knihovny, klíč předplatného předána v konstruktoru třídy FaceServiceClient. Příklad:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Klíč předplatného je možné získat ze stránky portálu Azure Marketplace. V tématu [odběry](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> Krok 3: Vytvoření PersonGroup

PersonGroup, s názvem "MyPersonGroup" se vytvoří pro uložení osoby.
Doba požadavku je zařazených do fronty pro `_timeStampQueue` zajistit celkové ověření.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> Krok 4: Vytvoření osoby, které mají PersonGroup

Osob vytvářejí souběžně a `await WaitCallLimitPerSecondAsync()` platí také pro nedošlo k překročení limitu volání.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> Krok 5: Přidejte řezy osobám

Přidání řezy na jiné osoby, jsou zpracovávány současně, zatímco pro jeden konkrétní osoby je sekvenční.
Znovu `await WaitCallLimitPerSecondAsync()` je vyvolána k zajištění frekvence žádosti se v rámci oboru omezení.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> Souhrn

V této příručce jste se naučili procesem vytvoření PersonGroup s ohromnou počet osob a řezy. Několik připomenutí:

- Tato strategie platí také pro FaceList a LargePersonGroup.
- Přidání nebo odstranění řezy různých FaceLists nebo osobám v LargePersonGroup lze současně zpracovávat.
- Stejné operace jeden konkrétní FaceList nebo osobě v LargePersonGroup by mělo být provedeno postupně.
- Zachovat jednoduchost, zpracování potenciální výjimky vynechání v této příručce. Pokud chcete vylepšit další robustnost, bude použito zásady správné opakování.

Tady jsou rychlé připomenutí funkcí dříve vysvětlené a ukázán:

- Vytváření s použitím PersonGroups [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API
- Vytvoření osob pomocí [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API
- Přidání řezy osobám pomocí [PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API

## <a name="related"></a> Související témata
- [Jak identifikovat tyto řezy v bitové kopii](HowtoIdentifyFacesinImage.md)
- [Jak zjistit, kterým čelí v bitové kopii](HowtoDetectFacesinImage.md)
- [Jak používat funkci ve velkém měřítku](how-to-use-large-scale.md)
