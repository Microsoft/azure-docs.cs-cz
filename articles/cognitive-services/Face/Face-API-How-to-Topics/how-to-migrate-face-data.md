---
title: Migrace dat pro rozpoznávání tváře napříč předplatnými – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Tento průvodce vám ukáže, jak migrovat vaše data uložená pro rozpoznávání tváře z jednoho předplatného API pro rozpoznávání tváře do jiného.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913787"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrace dat pro rozpoznávání tváře do jiného předplatného pro rozpoznávání tváře

Tato příručka ukazuje, jak přesunout do jiného předplatného Azure Cognitive Services Face API pro rozpoznávání tváře dat, jako jsou uložené jeden objekt PersonGroup objekt s tváří. Pro přesun dat, můžete použít funkci snímku. Tímto způsobem se vyhnete nutnosti opakovaně sestavení a jejich trénování objekt jeden objekt PersonGroup nebo FaceList při přesunutí nebo rozšířit vaše operace. Například možná jste vytvořili objekt jeden objekt PersonGroup pomocí bezplatné předplatné zkušební verze a chcete migrovat na placené předplatné. Nebo možná budete muset synchronizovat data pro rozpoznávání tváře napříč oblastmi operace velký podnik.

Tato stejné strategie migrace platí také pro LargePersonGroup a LargeFaceList objekty. Pokud nejste obeznámeni s koncepty v tomto průvodci, najdete v jejich definice v [rozpoznávání koncepty pro rozpoznávání tváře](../concepts/face-recognition.md) průvodce. Tato příručka používá Klientská knihovna .NET API pro rozpoznávání tváře s C#.

## <a name="prerequisites"></a>Požadavky

Potřebujete následující položky:

- Dvě API pro rozpoznávání tváře klíče předplatného, jedno s existujícími daty a jedno k migraci do. K odběru služby API pro rozpoznávání tváře a získejte klíč, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- API pro rozpoznávání tváře řetězec ID předplatného, který odpovídá cílové předplatné. Chcete-li ji najít, vyberte **přehled** na webu Azure Portal. 
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Tento průvodce použije jednoduchou konzolovou aplikaci pro spuštění migrace dat pro rozpoznávání tváře. Úplnou implementaci, najdete v článku [ukázkové rozhraní API pro rozpoznávání tváře snímku](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) na Githubu.

1. V sadě Visual Studio vytvořte nový projekt konzolové aplikace .NET Framework. Pojmenujte ji **FaceApiSnapshotSample**.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky NuGet**. Vyberte **Procházet** kartu a vyberte **zahrnout předběžné verze**. Najít a nainstalovat balíček následující:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Vytvoření klientů pro rozpoznávání tváře

V **hlavní** metoda *Program.cs*, pak vytvoříte další dva [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) instancí zdrojové a cílové předplatné. Tento příklad používá pro rozpoznávání tváře předplatné v oblasti východní Asie jako zdroje a USA – západ předplatného jako cíl. Tento příklad ukazuje, jak migrovat data z jedné oblasti Azure do jiné. Pokud vaše předplatná jsou v různých oblastech, změňte `Endpoint` řetězce.

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

Vyplňte hodnoty klíče předplatného a adresy URL koncových bodů pro zdrojové a cílové předplatné.


## <a name="prepare-a-persongroup-for-migration"></a>Příprava na migraci jeden objekt PersonGroup

Potřebujete ID jeden objekt PersonGroup ve vašem předplatném zdroje pro migraci do cílového odběru. Použití [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metodu pro načtení seznamu objektů jeden objekt PersonGroup. Potom získejte [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) vlastnost. Tento proces vypadá jinak v závislosti na jakou jeden objekt PersonGroup objekty máte. V této příručce se zdroji ID jeden objekt PersonGroup je uložen v `personGroupId`.

> [!NOTE]
> [Ukázkový kód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) vytvoří a trénovat nové jeden objekt PersonGroup k migraci. Ve většině případů byste už měli mít jeden objekt PersonGroup používat.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pořízení snímku jeden objekt PersonGroup

Snímek je dočasný vzdálené úložiště pro některé typy dat pro rozpoznávání tváře. Funguje jako typ schránky pro kopírování dat z jednoho předplatného do druhého. Nejprve vytvořte snímek dat ve zdrojovém předplatném. Potom je použijete na nový datový objekt v cílovém předplatném.

Použijte k vytvoření snímku jeden objekt PersonGroup instanci FaceClient zdrojové předplatné. Použití [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) s ID jeden objekt PersonGroup a ID cílového předplatného. Pokud máte více předplatných cíl, je přidáte jako položky pole v třetí parametr.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces převzetí a použití snímků není narušit jakékoli pravidelné volání zdroje nebo cílové objektů Persongroup nebo FaceLists. Neprovádějte souběžných volání, které mění zdroje objektu, například [volání správy FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) nebo [jeden objekt PersonGroup trénování](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) volat, například. Operace vytvoření snímku může spustit před nebo za tyto operace nebo může dojít k chybám.

## <a name="retrieve-the-snapshot-id"></a>Načtení ID snímku

Metodu použitou k pořizování snímků je asynchronní, takže musíte počkat na jeho dokončení. Operace snímku nelze zrušit. V tomto kódu `WaitForOperation` monitoruje asynchronního volání metody. Kontroluje stav každých 100 ms. Po dokončení operace načtení ID operace pomocí analýzy `OperationLocation` pole. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typické `OperationLocation` hodnotu vypadá takto:

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

Po operaci stav zobrazuje `Succeeded`, získejte ID snímku analýzou `ResourceLocation` pole vrácená instance stav OperationStatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typické `resourceLocation` hodnotu vypadá takto:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Použití snímku do cílového odběru

Dále vytvořte novou jeden objekt PersonGroup v cílovém předplatném pomocí náhodně vygenerované ID. Pomocí instance FaceClient cílové předplatné používat pro tento jeden objekt PersonGroup snímku. Předat ve snímku ID a nové ID jeden objekt PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Snímek objektu je platný pouze 48 hodin. Pouze pořízení snímku, pokud máte v úmyslu použít pro migraci dat krátce po.

Žádost o použít snímek vrátí ID jiné operace. Toto ID získáte analyzovat `OperationLocation` pole vrácené applySnapshotResult instance. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces snímku aplikace je také asynchronní, takže znovu použít `WaitForOperation` počkat na její dokončení.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Test migrace dat

Po použití snímku, naplní se nové jeden objekt PersonGroup v cílovém předplatném s původní data pro rozpoznávání tváře. Ve výchozím nastavení jsou zkopírovány také výsledky školení. Nové jeden objekt PersonGroup je připraven pro volání identifikace tváře bez nutnosti přetrénování.

K otestování migrace dat, spusťte následující operace a porovnat výsledky, které se vypíše do konzoly:

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

Teď můžete použít novou jeden objekt PersonGroup v cílovém předplatném. 

Aktualizovat cíl jeden objekt PersonGroup v budoucnu znovu, vytvořte nové jeden objekt PersonGroup přijímat snímku. Chcete-li to provést, postupujte podle kroků v této příručce. Jeden objekt PersonGroup jeden objekt může mít snímku použit pouze jednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení migrace dat pro rozpoznávání tváře, ručně odstraňte objekt snímku.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Další postup

V dalším kroku najdete v příslušné referenční dokumentaci k rozhraní API, prozkoumat ukázkovou aplikaci, která používá funkci snímku, nebo postupujte podle nepředstavuje průvodce spustit pomocí jiné operace rozhraní API uvedených zde:

- [Snímek referenční dokumentace (sadu .NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Ukázkový snímek rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Přidat tváří](how-to-add-faces.md)
- [Rozpoznávání tváří v obrázku](HowtoDetectFacesinImage.md)
- [Identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
