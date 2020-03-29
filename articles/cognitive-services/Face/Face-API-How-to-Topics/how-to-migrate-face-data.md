---
title: Migrace osobních dat mezi předplatnými – Face
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak migrovat uložená data o obličeji z jednoho předplatného face do druhého.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169816"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrace dat obličeje do jiného předplatného face

Tato příručka ukazuje, jak přesunout data obličeje, jako je například uložený objekt PersonGroup s plochami, do jiného předplatného Azure Cognitive Services Face. Chcete-li přesunout data, použijte funkci Snímek. Tímto způsobem se vyhnete nutnosti opakovaně vytvářet a trénovat objekt PersonGroup nebo FaceList při přesunutí nebo rozšíření operací. Například jste vytvořili objekt PersonGroup pomocí bezplatného zkušebního předplatného a nyní ho chcete migrovat do placeného předplatného. Nebo možná budete muset synchronizovat data obličeje mezi předplatnými v různých oblastech pro velké podnikové operace.

Stejná strategie migrace platí také pro LargePersonGroup a LargeFaceList objekty. Pokud nejste obeznámeni s koncepty v této příručce, podívejte se na jejich definice v průvodci [koncepty rozpoznávání tváří.](../concepts/face-recognition.md) Tato příručka používá klientskou knihovnu Face .NET s c#.

## <a name="prerequisites"></a>Požadavky

Potřebujete následující položky:

- Dva face klíče předplatného, jeden s existujícími daty a jeden migrovat. Chcete-li se přihlásit ke službě Face a získat klíč, postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Face id předplatných řetězec, který odpovídá cílové předplatné. Pokud ho chcete najít, vyberte **Přehled** na webu Azure Portal. 
- Libovolné vydání [Visual Studia 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Tato příručka používá jednoduchou konzolovou aplikaci ke spuštění migrace dat obličeje. Úplné provedení najdete na [ukázce face snímek](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) na GitHubu.

1. V sadě Visual Studio vytvořte nový projekt konzolové aplikace .NET Framework. Pojmenujte jej **FaceApiSnapshotSample**.
1. Získejte požadované balíčky NuGet. Klepněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte **spravovat balíčky NuGet**. Vyberte kartu **Procházet** a vyberte **Zahrnout předběžnou verzi**. Vyhledejte a nainstalujte následující balíček:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Vytvořit klienty s obličejem

V **Main** metoda v *Program.cs*, vytvořte dvě instance [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) pro vaše zdrojové a cílové odběry. Tento příklad používá face předplatné v oblasti východní Asie jako zdroj a západní USA předplatné jako cíl. Tento příklad ukazuje, jak migrovat data z jedné oblasti Azure do jiné. 

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

Vyplňte hodnoty klíče předplatného a adresy URL koncového bodu pro vaše zdrojové a cílové odběry.


## <a name="prepare-a-persongroup-for-migration"></a>Příprava skupiny PersonGroup na migraci

K migraci do cílového předplatného potřebujete ID skupiny osob ve zdrojovém předplatném. Pomocí metody [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) načtěte seznam objektů PersonGroup. Potom získejte [persongroup.personGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) vlastnost. Tento proces vypadá jinak v závislosti na objektech PersonGroup, které máte. V této příručce je zdroj PersonGroup `personGroupId`ID uloženv .

> [!NOTE]
> [Ukázkový kód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) vytvoří a vlaky nové PersonGroup migrovat. Ve většině případů byste již měli mít PersonGroup k použití.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pořízení snímku skupiny PersonGroup

Snímek je dočasné vzdálené úložiště pro určité typy dat Face. Funguje jako druh schránky pro kopírování dat z jednoho předplatného do druhého. Nejprve pořízení snímku dat ve zdrojovém předplatném. Potom jej použijete na nový datový objekt v cílovém předplatném.

Použijte instanci FaceClient zdrojového předplatného k pořízení snímku PersonGroup. Použijte [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) s ID PersonGroup a ID cílového předplatného. Pokud máte více cílových odběrů, přidejte je jako položky pole ve třetím parametru.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces pořizování a použití snímků nenarušuje žádné pravidelné volání do zdroje nebo cílové PersonGroups nebo FaceLists. Neprovávejte simultánní volání, která mění zdrojový objekt, například [volání správy FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) nebo volání [PersonGroup Train.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) Operace snímek může spustit před nebo po těchto operacích nebo může dojít k chybám.

## <a name="retrieve-the-snapshot-id"></a>Načtení ID snímku

Metoda použitá k pořízení snímků je asynchronní, takže je nutné počkat na jeho dokončení. Operace snímku nelze zrušit. V tomto kódu `WaitForOperation` metoda monitoruje asynchronní volání. Kontroluje stav každých 100 ms. Po dokončení operace načtěte ID operace analýzou `OperationLocation` pole. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typická `OperationLocation` hodnota vypadá takto:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Pomocná `WaitForOperation` metoda je zde:

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

Po zobrazení `Succeeded`stavu operace získáte ID snímku analýzou `ResourceLocation` pole vrácené instance OperationStatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typická `resourceLocation` hodnota vypadá takto:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Použití snímku u cílového předplatného

Dále vytvořte novou PersonGroup v cílovém předplatném pomocí náhodně generovaného ID. Potom použijte instanci FaceClient cílového předplatného k použití snímku pro tuto PersonGroup. Předaj id snímku a nové ID PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Objekt Snímek je platný pouze 48 hodin. Snímek pořkaďte pouze v případě, že jej chcete použít pro migraci dat brzy poté.

Snímek použít požadavek vrátí jiné ID operace. Chcete-li získat toto `OperationLocation` ID, analyzovat pole vrácené applySnapshotResult instance. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikace snímek je také asynchronní, takže znovu použít `WaitForOperation` čekat na dokončení.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testování migrace dat

Po použití snímku, nové PersonGroup v cílové matné předplatné naplní původní data tváře. Ve výchozím nastavení se kopírují také výsledky školení. Nová PersonGroup je připravena pro volání identifikace obličeje bez nutnosti rekvalifikace.

Chcete-li migraci dat otestovat, spusťte následující operace a porovnejte výsledky, které vytisknou, s konzolou:

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

Nyní můžete použít nové PersonGroup v cílové předplatné. 

Chcete-li v budoucnu cílovou skupinu PersonGroup znovu aktualizovat, vytvořte novou skupinu persongroup pro příjem snímku. Chcete-li to provést, postupujte podle pokynů v této příručce. Jeden objekt PersonGroup může mít snímek použít pouze jednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení migrace dat plochy ručně odstraňte objekt snímku.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Další kroky

Dále se podívejte na příslušnou referenční dokumentaci rozhraní API, prozkoumejte ukázkovou aplikaci, která používá funkci Snímek, nebo postupujte podle pokynů pro spuštění dalších zde uvedených operací rozhraní API:

- [Referenční dokumentace snímku (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Ukázka snímku plochy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Přidání tváří](how-to-add-faces.md)
- [Rozpoznávání tváří na obrázku](HowtoDetectFacesinImage.md)
- [Identifikace tváří v obraze](HowtoIdentifyFacesinImage.md)
