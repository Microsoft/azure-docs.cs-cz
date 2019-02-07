---
title: Migrace dat pro rozpoznávání tváře napříč předplatnými – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Tento průvodce vám ukáže, jak migrovat vaše data uložená pro rozpoznávání tváře z jednoho předplatného API pro rozpoznávání tváře do jiného.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 5eb198ecf76556e632c5f42bc22362b2f20f8916
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771540"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrace dat pro rozpoznávání tváře do jiného předplatného pro rozpoznávání tváře

Tento průvodce vám ukáže, jak přesunout data pro rozpoznávání tváře (jako je například uloženého **jeden objekt PersonGroup** ploch) do jiného předplatného API pro rozpoznávání tváře pomocí funkce snímku. To umožňuje vyhnout se tak nutnosti opakovaně sestavení a jejich trénování **jeden objekt PersonGroup** nebo **FaceList** při přesouvání nebo rozšiřování vaše operace. Například jste vytvořili **jeden objekt PersonGroup** pomocí bezplatné předplatné zkušební verze a teď chcete migrovat na placené předplatné, nebo možná budete muset synchronizovat data pro rozpoznávání tváře napříč oblastmi operace velký podnik.

Tato stejné strategie migrace platí také pro **LargePersonGroup** a **LargeFaceList** objekty. Pokud nejste obeznámeni s koncepty v tomto průvodci, najdete v jejich definice v [Glosář](../Glossary.md). Tato příručka používá Klientská knihovna .NET API pro rozpoznávání tváře s C#.

## <a name="prerequisites"></a>Požadavky

- Dvě API pro rozpoznávání tváře klíče předplatného (jeden s existujícími daty a jeden pro migraci do). Postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Řetězec ID předplatného API pro rozpoznávání tváře odpovídající cílové předplatné (součástí **přehled** okna na webu Azure portal). 
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Tento průvodce použije jednoduchou konzolovou aplikaci k provedení migrace dat pro rozpoznávání tváře. Úplnou implementaci, najdete v článku [ukázka snímku rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) na Githubu.

1. V sadě Visual Studio vytvořte nový **Konzolová aplikace (.NET Framework)** projektu a pojmenujte ho **FaceApiSnapshotSample**. 
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky NuGet**. Klikněte na tlačítko **Procházet** kartě a vyberte **zahrnout předběžné verze**; najít a nainstalovat balíček následující:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Vytvoření klientů pro rozpoznávání tváře

V **hlavní** metoda *Program.cs*, pak vytvoříte další dva **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** instancí zdrojové a cílové předplatné. V tomto příkladu použijeme pro rozpoznávání tváře předplatné v oblasti východní Asie jako zdroj a USA – západ předplatné jako cíl. To ukazuje, jak migrovat data z jedné oblasti Azure do jiné. Pokud vaše předplatné se v různých oblastech, bude nutné změnit `Endpoint` řetězce.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Musíte vyplnit předplatné hodnoty klíče a adresy URL koncových bodů pro zdrojové a cílové předplatné.


## <a name="prepare-a-persongroup-for-migration"></a>Příprava na migraci jeden objekt PersonGroup

Budete potřebovat ID **jeden objekt PersonGroup** ve vašem předplatném zdroje pro migraci do cílového odběru. Použití **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** metodu pro načtení seznamu vašich **jeden objekt PersonGroup** objekty; potom získá **[ PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** vlastnost. Tento proces bude vypadat jinak v závislosti na tom, co **jeden objekt PersonGroup** objekty máte. V tomto Průvodce zdroji **jeden objekt PersonGroup** ID je uložen v `personGroupId`.

> [!NOTE]
> [Ukázkový kód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) vytvoří a trénovat nový **jeden objekt PersonGroup** Pokud chcete migrovat, ale ve většině případů byste už měli mít **jeden objekt PersonGroup** používat.

## <a name="take-snapshot-of-persongroup"></a>Pořízení snímku jeden objekt PersonGroup

Snímek je dočasný vzdálené úložiště pro některé typy dat pro rozpoznávání tváře. Funguje jako typ schránky pro kopírování dat z jednoho předplatného do druhého. První uživatel "přebírá" snímek dat ve zdrojovém předplatném, a potom "použijí" je nový datový objekt v cílovém předplatném.

Použít zdrojové předplatné **FaceClient** instance pořízení snímku **jeden objekt PersonGroup**s použitím **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** s  **Jeden objekt PersonGroup** ID a ID cílového předplatného. Pokud máte více předplatných cíl, můžete je přidat jako položky pole v třetí parametr.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces převzetí a použití snímků nenaruší regulární volání zdroje nebo cíle **jeden objekt PersonGroup**s (nebo **FaceList**s). Však nedoporučujeme souběžných volání, které se mění zdrojového objektu ([volání správy pro rozpoznávání tváře seznamu](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist) nebo [osobu skupiny – Train](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train) volat, například), protože může operace vytvoření snímku spustit před nebo za tyto operace nebo může dojít k chybám. 

## <a name="retrieve-the-snapshot-id"></a>Načtení ID snímku

Snímku, přičemž – metoda je asynchronní, takže budete muset počkat na jeho dokončení (snímek, který operace se nedá zrušit.). V tomto kódu `WaitForOperation` metoda monitoruje asynchronní volání, kontrola stavu každých 100 ms. Po dokončení operace bude moct načíst ID operace. Můžete ji získat pomocí analýzy `OperationLocation` pole. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typické `OperationLocation` hodnota bude vypadat například takto:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation` Pomocná metoda, je zde:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Stav operace, když je označena jako `Succeeded`, pak můžete získat ID snímku analýzou `ResourceLocation` pole vráceného **stav OperationStatus** instance.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typické `resourceLocation` hodnota bude vypadat například takto:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Použití snímku do cílového odběru

Dále vytvořte novou **jeden objekt PersonGroup** v cílovém předplatném pomocí náhodně vygenerované ID. Pak pomocí cílové předplatné **FaceClient** instance má použít pro tento jeden objekt PersonGroup snímku předáním hodnoty ID snímku a nový **jeden objekt PersonGroup** ID. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Snímek objektu je platná pouze pro 48 hodin. Pokud máte v úmyslu použít pro migraci dat by měla pouze pořízení snímku krátce po.

Žádost o použít snímek vrátí ID jiné operace. Toto ID můžete získat pomocí analýzy `OperationLocation` pole vráceného **applySnapshotResult** instance. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces snímku aplikace je také asynchronní, takže znovu použít `WaitForOperation` počkat na její dokončení.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Test migrace dat

Po použití snímku, nové **jeden objekt PersonGroup** v cílovém předplatném mělo být vyplněno pomocí původní data pro rozpoznávání tváře. Ve výchozím nastavení, jsou zkopírovány také výsledky školení, takže nové **jeden objekt PersonGroup** bude připravený pro volání identifikace tváří, a to bez nutnosti přetrénování.

K otestování migrace dat, můžete spustit následující operace a porovnat výsledky, které se vypíše do konzoly.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Použijte následující metody pomocné rutiny:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Teď můžete začít používat nový **jeden objekt PersonGroup** v cílovém předplatném. 

Pokud chcete provést aktualizaci cílové **jeden objekt PersonGroup** znovu v budoucnu, budete muset vytvořit novou **jeden objekt PersonGroup** (podle kroků tohoto průvodce) pro příjem snímku. Jediný **jeden objekt PersonGroup** objekt může mít pouze snímku použit jednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení migrace dat pro rozpoznávání tváře, doporučujeme, abyste že ručně odstraňte objekt snímku.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Související témata

- [Snímek referenční dokumentace (sadu .NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Ukázkový snímek rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Postup přidání tváří](how-to-add-faces.md)
- [Postup rozpoznávání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Tom, jak identifikovat čelí obrázku](HowtoIdentifyFacesinImage.md)
