---
title: Migrace dat na obličej napříč předplatnými – tvář
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak migrovat uložená data z jednoho předplatného na jiný.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: c8d3c5b10c670e7aa4f1fd00f47ef47e772416cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706856"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrace vašich obličejových dat na jiný obličejový odběr

V této příručce se dozvíte, jak přesunout data obličeje, jako je uložený objekt Person s obličejemi, do jiného předplatného Azure Cognitive Services Face. Chcete-li přesunout data, použijte funkci Snapshot. Tímto způsobem se vyhnete opakovanému sestavování a učení objektu person nebo FaceList při přesouvání nebo rozšiřování vašich operací. Můžete třeba vytvořit objekt Person s bezplatným předplatným a teď ho chtít migrovat do placeného předplatného. Nebo může být nutné synchronizovat data z oblasti v rámci předplatných v různých oblastech pro velkou podnikovou operaci.

Tato strategie migrace se vztahuje také na objekty LargePersonGroup a LargeFaceList. Pokud nejste obeznámeni s koncepty v této příručce, přečtěte si téma jejich definice v příručce věnovaném [koncepcím rozpoznávání obličeje](../concepts/face-recognition.md) . Tato příručka používá klientskou knihovnu rozhraní .NET s jazykem C#.

## <a name="prerequisites"></a>Předpoklady

Potřebujete následující položky:

- Dva klíče předplatného, jeden s existujícími daty a jeden pro migraci na. Pokud se chcete přihlásit k odběru služby obličeje a získat svůj klíč, postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](../../cognitive-services-apis-create-account.md).
- Řetězec ID předplatného obličeje, který odpovídá cílovému předplatnému. Pokud ho chcete najít, vyberte v Azure Portal **Přehled** . 
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Tato příručka používá jednoduchou konzolovou aplikaci ke spuštění migrace dat na tvář. Úplnou implementaci najdete v [ukázce snímku obličeje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) na GitHubu.

1. V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET Framework projektu. Pojmenujte ho **FaceApiSnapshotSample**.
1. Získejte požadované balíčky NuGet. V Průzkumník řešení klikněte pravým tlačítkem na svůj projekt a vyberte **Spravovat balíčky NuGet**. Vyberte kartu **Procházet** a vyberte **Zahrnout předprodejní verze**. Vyhledejte a nainstalujte následující balíček:
    - [Microsoft. Azure. Cognitiveservices Account. Vision. Face 2.3.0-Preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Vytváření klientů obličeje

V metodě **Main** v *programu program. cs* vytvořte dvě instance [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) pro své zdrojové a cílové předplatné. V tomto příkladu se jako cíl používá odběr obličeje v Východní Asie oblasti jako zdroj a Západní USA předplatné. Tento příklad ukazuje, jak migrovat data z jedné oblasti Azure do jiné. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

Zadejte hodnoty klíče předplatného a adresy URL koncového bodu pro vaše zdrojové a cílové předplatné.


## <a name="prepare-a-persongroup-for-migration"></a>Příprava osoby na migraci

Abyste mohli migrovat do cílového předplatného, potřebujete ID oddělení person ve zdrojovém předplatném. Pomocí metody [PersonGroupOperationsExtensions. ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) načtěte seznam objektů objektu Person. Pak Získejte vlastnost [Person. PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) . Tento proces se liší v závislosti na tom, jaké objekty objektu person máte. V této příručce je zdrojové ID prodejce Uloženo v `personGroupId` .

> [!NOTE]
> [Vzorový kód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) vytvoří a navlakuje novou samostatnou osobu k migraci. Ve většině případů byste už měli mít k dispozici osobu, kterou by používala.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pořídit snímek osoby

Snímek je dočasné vzdálené úložiště pro určité datové typy obličeje. Funguje jako typ schránky pro kopírování dat z jednoho předplatného do jiného. Nejdřív si pořídíte snímek dat ve zdrojovém předplatném. Pak ho použijete pro nový datový objekt v cílovém předplatném.

Pomocí instance FaceClient zdrojového předplatného si pořídit snímek pracovní třídy Person. Použijte [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) s ID objektu Person a ID cílového předplatného. Pokud máte více cílových předplatných, přidejte je jako položky pole do třetího parametru.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces pořízení a použití snímků neruší žádná pravidelná volání do zdrojového nebo cílového objektů persongroup nebo FaceLists. Neprovádějte souběžná volání, která mění zdrojový objekt, jako jsou například [volání správy FaceList](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) nebo volání metody [Person](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations) . Operaci snímku můžete spustit před nebo po těchto operacích nebo se může setkat s chybami.

## <a name="retrieve-the-snapshot-id"></a>Načtení ID snímku

Metoda použitá pro pořizování snímků je asynchronní, takže musíte počkat na její dokončení. Operace snímku nelze zrušit. V tomto kódu `WaitForOperation` Metoda monitoruje asynchronní volání. Kontroluje stav každých 100 ms. Po dokončení operace načtěte ID operace, která analyzuje `OperationLocation` pole. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typická `OperationLocation` hodnota vypadá takto:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation`Pomocná metoda je tady:

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

Po zobrazení stavu operace `Succeeded` Získejte ID snímku analýzou `ResourceLocation` pole vrácené instance stav operationstatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typická `resourceLocation` hodnota vypadá takto:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Použití snímku na cílové předplatné

V dalším kroku vytvořte novou samostatnou osobu v cílovém předplatném pomocí náhodně generovaného ID. Pak použijte instanci FaceClient cílového předplatného k aplikování snímku na tuto osobu Person. Předejte ID snímku a ID nového prodejce.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Objekt snímku je platný pouze 48 hodin. Snímek si pořídit jenom v případě, že ho chcete použít k migraci dat hned po.

Požadavek na použití snímku vrátí další ID operace. Chcete-li získat toto ID, analyzujte `OperationLocation` pole vrácené instance applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikace snímku je také asynchronní, proto znovu použijte `WaitForOperation` k čekání na jeho dokončení.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testování migrace dat

Po použití snímku naplní nová pole person v cílovém předplatném původní data obličeje. Ve výchozím nastavení se zkopírují i výsledky školení. Nová pracovní plocha je připravena pro volání identifikace obličeje bez nutnosti přeškolení.

K otestování migrace dat spusťte následující operace a porovnejte výsledky, které tisknou do konzoly:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Použijte následující pomocné metody:

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

Nyní můžete použít novou osobu person v cílovém předplatném. 

Chcete-li cílovou objekt person aktualizovat znovu v budoucnu, vytvořte novou objekt person pro příjem snímku. Postupujte podle kroků v této příručce. K jednomu objektu ve vaší osobě se dá použít snímek jenom jednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení migrace dat obličeje ručně odstraňte objekt snímku.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Další kroky

Dále si přečtěte relevantní referenční dokumentaci k rozhraní API, prozkoumejte ukázkovou aplikaci, která používá funkci snímků, nebo postupujte podle pokynů průvodce, abyste mohli začít používat jiné operace rozhraní API uvedené tady:

- [Dokumentace k odkazům na snímky (.NET SDK)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Ukázka snímku obličeje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Přidání tváří](how-to-add-faces.md)
- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
