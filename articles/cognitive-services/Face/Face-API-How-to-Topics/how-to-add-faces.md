---
title: 'Příklad: Přidat tváří jeden objekt PersonGroup – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Použijte rozhraní API pro rozpoznávání tváře k přidání tváří do obrázků.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124502"
---
# <a name="add-faces-to-a-persongroup"></a>Přidat tváří jeden objekt PersonGroup

Tato příručka ukazuje, jak přidat velké množství osob a tváří na jeden objekt PersonGroup objekt. Stejné strategie platí také pro LargePersonGroup FaceList a LargeFaceList objekty. Tato ukázka je napsána v C# pomocí klientské knihovny Azure Cognitive Services Face API .NET.

## <a name="step-1-initialization"></a>Krok 1: Inicializace

Následující kód deklaruje několik proměnných a implementuje přidejte pomocnou funkci rozhraním pro rozpoznávání tváře naplánování požadavků:

- `PersonCount` je celkový počet osob.
- `CallLimitPerSecond` je maximální počet volání za sekundu podle úrovně předplatného.
- `_timeStampQueue` je fronta zaznamenávající časová razítka požadavků.
- `await WaitCallLimitPerSecondAsync()` počká, až je platný odeslat další požadavek.

```csharp
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

## <a name="step-2-authorize-the-api-call"></a>Krok 2: Povolit volání rozhraní API

Při použití klientské knihovny, je nutné předat do konstruktoru třídy FaceServiceClient váš klíč předplatného. Příklad:

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Klíč předplatného najdete na webu Azure Marketplace na webu Azure Portal. Další informace najdete v tématu [předplatná](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Vytvořte jeden objekt PersonGroup

Kolekce PersonGroup s názvem „MyPersonGroup“ slouží k ukládání osob.
Čas žádosti se zařadí do fronty `_timeStampQueue`, aby se zajistilo celkové ověření.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4: Vytvoření osob pro jeden objekt PersonGroup

Osob jsou vytvořeny současně, a `await WaitCallLimitPerSecondAsync()` platí také pro nedošlo k překročení limitu volání.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5: Přidat tváře osoby

Přidat do jiné osoby tváří souběžného zpracování. Tváří, které jsou přidány pro jeden konkrétní osobu, která se provádějí postupně.
Opět `await WaitCallLimitPerSecondAsync()` se vyvolá, aby Ujistěte se, že frekvence požadavku v rámci oboru omezení.

```csharp
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

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, procesem vytvoření jeden objekt PersonGroup s velké množství osob a tváří. Několik připomenutí:

- Tato strategie platí také pro FaceLists a LargePersonGroups.
- Přidání nebo odstranění tváří různých FaceLists nebo osobám v LargePersonGroups souběžného zpracování.
- Přidání nebo odstranění tváře, abyste jeden konkrétní FaceList nebo osoba LargePersonGroup se provádějí postupně.
- Pro zjednodušení je vynecháno zpracování potenciální výjimky v této příručce. Pokud chcete vylepšit další odolnosti, použijte zásady opakování správné.

Následující funkce byly vysvětlení a jsme vám ukázali:

- Vytvoření s použitím objektů Persongroup [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API.
- S vytvořením osob [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API.
- Přidat tváře osoby pomocí [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API.

## <a name="related-topics"></a>Související témata

- [Identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
- [Rozpoznávání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Použít funkci ve velkém měřítku](how-to-use-large-scale.md)
