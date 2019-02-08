---
title: 'Příklad: Přidat tváří – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Použijte rozhraní API pro rozpoznávání tváře k přidání tváří do obrázků.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 722a09b782c902642b599460835151928c16c5f4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859024"
---
# <a name="example-how-to-add-faces"></a>Příklad: Postup přidání tváří

Tento průvodce ukazuje osvědčený postup pro přidání velkého množství osob a tváří do kolekce PersonGroup.
Stejná strategie platí taky pro kolekce FaceList a LargePersonGroup.
Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro rozpoznávání tváře.

## <a name="step-1-initialization"></a>Krok 1: Inicializace

Deklaruje se několik proměnných a implementuje se pomocná funkce pro plánování žádostí.

- `PersonCount` je celkový počet osob.
- `CallLimitPerSecond` je maximální počet volání za sekundu podle úrovně předplatného.
- `_timeStampQueue` je fronta zaznamenávající časová razítka požadavků.
- `await WaitCallLimitPerSecondAsync()` počká, než bude platné odeslat další požadavek.

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

## <a name="step-2-authorize-the-api-call"></a>Krok 2: Povolit volání rozhraní API

Když používáte klientskou knihovnu, klíč předplatného se předává prostřednictvím konstruktoru třídy FaceServiceClient. Příklad:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Klíč předplatného můžete získat z webu Marketplace nebo z portálu Azure Portal. Přečtěte si téma [Předplatná](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Vytvořte jeden objekt PersonGroup

Kolekce PersonGroup s názvem „MyPersonGroup“ slouží k ukládání osob.
Čas žádosti se zařadí do fronty `_timeStampQueue`, aby se zajistilo celkové ověření.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Krok 4: Vytvoření osoby, které mají jeden objekt PersonGroup

Osoby se vytváří současně, a aby nedošlo k překročení limitu volání, používá se příkaz `await WaitCallLimitPerSecondAsync()`.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5: Přidat tváře osoby

Přidávání tváří různým osobám se zpracovává souběžně, ale u konkrétní osoby je sekvenční.
Opět se vyvolává příkaz `await WaitCallLimitPerSecondAsync()`, aby se zajistilo, že frekvence žádostí nepřekročí limit.

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

## <a name="summary"></a>Souhrn

V tomto průvodci jste se naučili vytvořit kolekci PersonGroup s velkým množstvím osob a tváří. Několik připomenutí:

- Tato strategie platí taky u kolekcí FaceList a LargePersonGroup.
- Přidávání či odstraňování obličejů je možné v různých kolekcích FaceList nebo Person z kolekce LargePersonGroup zpracovávat souběžně.
- Stejné operace u jedné konkrétní kolekce FaceList nebo Person z kolekce LargePersonGroup byste měli provádět sekvenčně.
- Abychom zbytečně nezabíhali do podrobností, přeskočili jsme v tomto průvodci potenciální výjimky. Pokud chcete zlepšit robustnost, měli byste použít vhodné zásady opakování.

Následující odkazy slouží jako rychlé připomenutí dříve vysvětlených funkcí:

- Vytvoření kolekcí PersonGroups pomocí rozhraní API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Vytvoření osob pomocí rozhraní API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Přidání tváří osobám pomocí rozhraní API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Související témata

- [Postup identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
- [Postup rozpoznávání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Postup použití funkce ve velkém měřítku](how-to-use-large-scale.md)
