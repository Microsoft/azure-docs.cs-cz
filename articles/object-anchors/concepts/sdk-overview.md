---
title: Přehled sady SDK modulu runtime
description: Seznámení se sadou SDK modulu runtime ukotvení objektů.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 551374824610c0257aaf52c45768d31849026524
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047537"
---
# <a name="runtime-sdk-overview"></a>Přehled sady SDK modulu runtime

V této části najdete podrobný přehled sady SDK ukotvení objektů, které se používají ke zjišťování objektů pomocí modelu ukotvení objektů. Získáte přehled o tom, jak je objekt reprezentován a k čemu se používají různé komponenty.

Všechny níže popsané typy lze nalézt v oboru názvů **Microsoft. MixedReality. ObjectAnchors** .

## <a name="types"></a>Typy

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) představuje geometrii fyzických objektů a kóduje potřebné parametry pro detekci a odhad pozice. Musí být vytvořen pomocí [služby ukotvení objektů](../quickstarts/get-started-model-conversion.md). Aplikace pak může načíst vygenerovaný soubor modelu pomocí rozhraní API kotev objektu a zadat dotaz na síť vloženou v tomto modelu pro vizualizaci.

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) určuje místo pro hledání jednoho nebo více objektů. Je definovaný ID uzlu prostorového grafu a prostorové meze v systému souřadnic reprezentované ID uzlu prostorového grafu. Sada SDK ukotvení objektů podporuje čtyři typy hranic, konkrétně **pole pole zobrazení**, **ohraničujícího pole**, **koule** a **umístění**.

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) oznamuje **pozorovateli objektu** , jak najít objekty daného modelu. Poskytuje následující parametry přizpůsobitelné, jejichž výchozí hodnoty lze načíst z objektového modelu.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

Vlastnost [MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) Určuje hodnotu, která je považována za zjištěnou instanci.

U každého kandidáta objektu **sleduje pozorovatel** poměr překrývajících se povrchů mezi transformovaným objektovým modelem a scénou a potom hlásí, že je kandidátem na aplikaci pouze v případě, že je poměr pokrytí nad danou prahovou hodnotou.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

Vlastnost [IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) určuje, zda se očekává, že cílový objekt na rovině umělé země stojí.

Základní rovina je nejnižší vodorovná podlaha v oblasti hledání. Poskytuje dobré omezení u možného objektu. Zapnutí tohoto příznaku povede **pozorovatele** k odhadu pozice v omezeném prostoru a může zlepšit přesnost. Tento parametr bude ignorován, pokud model nemá být na rovině základní desky.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

Vlastnost [ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) označuje očekávaný maximální úhel ve stupních mezi směrem instance objektu a závažností.

Tento parametr poskytuje jiné omezení pro směr v případě odhadované pozice. Například pokud je objekt napravo vpravo, tento parametr může být 0. Kotvy objektů neslouží k detekci objektů, které se liší od modelu. Pokud je model napravo vpravo, nezjistí nezjištěnou instanci. Pro souběžné rozložení se použije nový model. Stejné pravidlo platí pro kloub.

#### <a name="maxscalechange"></a>MaxScaleChange

Vlastnost [MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) označuje maximální změnu měřítka objektu (v rozsahu 0 ~ 1) s ohledem na prostorové mapování. Odhadované měřítko se používá pro transformované vrcholy objektů zarovnané na střed na počátku a na osách zarovnané na ose. Odhadovaná měřítka nemusí být skutečnou škálou mezi modelem CAD a jeho fyzickou reprezentací, ale některé hodnoty, které aplikaci umožňují vykreslovat objekt modelu blízko prostorového mapování na fyzickém objektu.

#### <a name="searchareas"></a>SearchAreas

Vlastnost [SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) označuje pole prostorových rozsahů, kde najít objekty.

**Pozorovatel** bude hledat objekty v prostoru sjednocení všech oblastí hledání zadaných v dotazu. V této verzi vrátíme maximálně jeden objekt s nejvyšší jistotou, aby se snížila latence.

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) představuje hypotetickou polohu, kde může být instance daného modelu v systému souřadnic HoloLens. Každá instance obsahuje `SurfaceCoverage` vlastnost, která indikuje, jak dobrá je předpokládaná pozice.

Instance je vytvořena voláním `ObjectObserver.DetectAsync` metody a pak se automaticky aktualizuje na pozadí při připojení. Aplikace může naslouchat události změněné stavu na konkrétní instanci nebo změnit režim sledování pro pozastavení nebo obnovení aktualizace. Instance bude automaticky odebrána z **pozorovatele** , když dojde ke ztrátě sledování.

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) načítá objektové modely, detekuje jejich instance a sestavy 6 – DOF představuje každou instanci v systému souřadnic HoloLens.

I když se z **pozorovatele** vytvoří libovolný objektový model nebo instance, jejich životnost je nezávislá. Aplikace může odstranit pozorovatele a pokračovat v používání objektového modelu nebo instance.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) zaznamenává diagnostiku a zapisuje data do archivu.

Diagnostický archiv zahrnuje Cloud bodu scény, stav pozorovatele a informace o modelech. Tyto informace jsou užitečné k identifikaci možných potíží s modulem runtime. Další informace najdete v [nejčastějších dotazech](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Použití a podrobnosti o běhovém prostředí sady SDK

Tato část by vám měla poskytnout základní informace o použití sady SDK modulu runtime. Měl by vám poskytnout dostatečný kontext pro procházení ukázkových aplikací, abyste viděli, jak se kotvy objektů používají komplexní.

### <a name="initialization"></a>Inicializace

Aplikace musí volat `ObjectObserver.IsSupported()` rozhraní API, aby bylo možné zjistit, zda jsou kotvy objektů v zařízení podporovány, než je použije. Pokud `ObjectObserver.IsSupported()` rozhraní API vrátí `false` , ověřte, že aplikace povolila **spatialPerception** schopnost and\or upgradovat na nejnovější operační systém HoloLens.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

V dalším kroku aplikace vytvoří pozorovatele objektu a nahraje nezbytné modely generované [službou konverze modelu kotev objektu](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

Aplikace vytvoří dotaz pro detekci instancí tohoto modelu v rámci prostoru.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Ve výchozím nastavení se každá zjištěná instance automaticky sleduje **pozorovatelem**. Volitelně můžeme manipulovat s těmito instancemi změnou režimu sledování nebo poslechem události změny stavu.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

V události změněné stav můžeme zadat dotaz na nejnovější stav nebo odstranit instanci, pokud se ztratilo sledování.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Aplikace může také volitelně nahrávat jednu nebo více diagnostických cvičení pro offline ladění.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Nakonec uvolníme prostředky vyřazením všech objektů.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```