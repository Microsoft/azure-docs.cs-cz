---
title: 'Příklad: Přidání obličeje k osobě person-Face'
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak přidat velký počet osob a ploch do objektu person ve službě Azure Cognitive Services Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3643fad1c9e821a78df6d78edeede2341ec79ea8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303757"
---
# <a name="add-faces-to-a-persongroup"></a>Přidání obličeje k osobě

Tato příručka ukazuje, jak přidat velký počet osob a ploch do objektu Person. Stejná strategie platí i pro objekty LargePersonGroup, FaceList a LargeFaceList. Tato ukázka je napsaná v jazyce C# pomocí klientské knihovny Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Krok 1: Inicializace

Následující kód deklaruje několik proměnných a implementuje pomocnou funkci pro naplánování žádostí o přidání obličeje:

- `PersonCount` je celkový počet osob.
- `CallLimitPerSecond` je maximální počet volání za sekundu podle úrovně předplatného.
- `_timeStampQueue` je fronta zaznamenávající časová razítka požadavků.
- `await WaitCallLimitPerSecondAsync()` počká, dokud nebude platná k odeslání dalšího požadavku.

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

Při použití klientské knihovny musíte předat klíč předplatného konstruktoru třídy **FaceClient** . Například:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Klíč předplatného získáte tak, že v Azure Portal přejdete na Azure Marketplace. Další informace najdete v tématu [předplatná](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Vytvoření kolekce PersonGroup

Kolekce PersonGroup s názvem „MyPersonGroup“ slouží k ukládání osob.
Čas žádosti se zařadí do fronty `_timeStampQueue`, aby se zajistilo celkové ověření.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4: vytvoření osob pro danou osobu

Osoby jsou vytvořeny souběžně a `await WaitCallLimitPerSecondAsync()` jsou také aplikovány, aby nedocházelo k překročení limitu volání.

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

Plošky přidané různým osobám jsou zpracovávány souběžně. Plošky přidané pro jednu konkrétní osobu jsou zpracovávány postupně.
Znovu se vyvolá, aby se `await WaitCallLimitPerSecondAsync()` zajistilo, že frekvence požadavků spadá do rozsahu omezení.

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

V této příručce jste se dozvěděli o procesu vytvoření osoby typu osoba s obrovským počtem osob a ploch. Několik připomenutí:

- Tato strategie platí i pro FaceLists a LargePersonGroups.
- Přidávání a odstraňování plošek pro různé FaceListsy nebo osoby v LargePersonGroups se zpracovávají souběžně.
- Přidávání a odstraňování plošek na konkrétní FaceList nebo osoby v LargePersonGroup se provádí postupně.
- Pro zjednodušení je v této příručce uveden postup, jak zpracovat potenciální výjimku. Pokud chcete zvýšit robustnost, použijte zásady správného opakování.

Následující funkce byly vysvětleny a ukázaly:

- Vytvořte objektů persongroup pomocí rozhraní API [Person-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Vytvořte osoby pomocí uživatele [Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Přidejte obličeje k osobám pomocí rozhraní [Person osoby – přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API pro rozpoznávání tváře.

## <a name="related-topics"></a>Související témata

- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
- [Použití funkce ve velkém měřítku](how-to-use-large-scale.md)
