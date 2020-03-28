---
title: 'Příklad: Přidání ploch do skupiny osob – plocha'
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak přidat velký počet osob a tváří do objektu PersonGroup se službou Azure Cognitive Services Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169832"
---
# <a name="add-faces-to-a-persongroup"></a>Přidání ploch do skupiny osob

Tato příručka ukazuje, jak přidat velký počet osob a tváří do objektu PersonGroup. Stejná strategie platí také pro LargePersonGroup, FaceList a LargeFaceList objekty. Tato ukázka je zapsána v C# pomocí klientské knihovny Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Krok 1: Inicializace

Následující kód deklaruje několik proměnných a implementuje pomocnou funkci pro naplánování požadavků na přidání obličeje:

- `PersonCount` je celkový počet osob.
- `CallLimitPerSecond` je maximální počet volání za sekundu podle úrovně předplatného.
- `_timeStampQueue` je fronta zaznamenávající časová razítka požadavků.
- `await WaitCallLimitPerSecondAsync()`čeká, až bude platný pro odeslání další žádosti.

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

## <a name="step-2-authorize-the-api-call"></a>Krok 2: Autorizace volání rozhraní API

Při použití klientské knihovny, musíte předat klíč předplatného konstruktoru **faceclient** třídy. Například:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Pokud chcete získat klíč předplatného, přejděte na Azure Marketplace z webu Azure Portal. Další informace naleznete v [tématu Předplatná](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Vytvoření kolekce PersonGroup

Kolekce PersonGroup s názvem „MyPersonGroup“ slouží k ukládání osob.
Čas žádosti se zařadí do fronty `_timeStampQueue`, aby se zajistilo celkové ověření.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4: Vytvoření osob pro PersonGroup

Osoby jsou vytvořeny souběžně `await WaitCallLimitPerSecondAsync()` a používají se také k tomu, aby nepřekročily limit volání.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5: Přidání tváří osobám

Plochy přidané k různým osobám jsou zpracovávány současně. Plochy přidané pro jednu konkrétní osobu jsou zpracovávány postupně.
Opět `await WaitCallLimitPerSecondAsync()` je vyvolána k zajištění, že četnost požadavků je v rámci omezení.

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
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Souhrn

V této příručce jste se dozvěděli proces vytváření PersonGroup s obrovským počtem osob a tváří. Několik připomenutí:

- Tato strategie platí také pro FaceLists a LargePersonGroups.
- Přidání nebo odstranění ploch do různých facelists nebo osoby v LargePersonGroups jsou zpracovány současně.
- Přidání nebo odstranění ploch do jednoho konkrétního facelist nebo osoby v LargePersonGroup se provádí postupně.
- Pro jednoduchost, jak zpracovat potenciální výjimku je vynechán v této příručce. Pokud chcete zvýšit robustnost, použijte správné zásady opakování.

Byly vysvětleny a demonstrovány následující funkce:

- Vytvořte persongroups pomocí [PersonGroup - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) rozhraní API.
- Vytvořte osoby pomocí [persongroup person - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) rozhraní API.
- Přidání tváří k osobám pomocí [persongroup person - přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API pro obličej.

## <a name="related-topics"></a>Související témata

- [Identifikace tváří v obraze](HowtoIdentifyFacesinImage.md)
- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
- [Použití funkce ve velkém měřítku](how-to-use-large-scale.md)
