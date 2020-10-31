---
title: Digitální dvojčata a graf dvojčat
titleSuffix: Azure Digital Twins
description: Pochopení konceptu digitálního vlákna a způsobu, jakým jejich vztahy vytvářejí graf.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3bf039eb099a5735c3528c1ba5b9c440d7787c43
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097052"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Pochopení digitálních vláken a jejich dvojitých grafů

V řešení digitálních vláken Azure jsou entity ve vašem prostředí reprezentovány pomocí **digitálních vláken** Azure. Digitální vlákna je instance jednoho z vašich vlastních definovaných [modelů](concepts-models.md). Dá se připojit k ostatním digitálním **závislostem prostřednictvím vztahů** , které tvoří **dvojitou graf** : Tento neřízený graf je reprezentace celého prostředí.

> [!TIP]
> "Digitální vlákna Azure" odkazuje na tuto službu Azure jako celek. "Digitální vlákna" nebo pouze "vlákna" odkazují na jednotlivé zdvojené uzly v rámci vaší instance služby.

## <a name="digital-twins"></a>Digitální dvojčata

Než budete moct vytvořit digitální dvojitou hodnotu v instanci digitálních vláken Azure, musíte mít do této služby nahraný *model* . Model popisuje sadu vlastností, zpráv telemetrie a vztahy, které mohou mít konkrétní vlákna, mimo jiné. Typy informací, které jsou definovány v modelu, naleznete v tématu [*Koncepty: vlastní modely*](concepts-models.md).

Po vytvoření a nahrání modelu může klientská aplikace vytvořit instanci typu; Jedná se o digitální dvojitou hodnotu. Například po vytvoření modelu *podlahy* můžete vytvořit jednu nebo několik digitálních vláken, která používají tento typ (například dvojitou hodnotu typu *podlah-Floor* s názvem *GroundFloor* , další s názvem *Floor2* atd.). 

## <a name="relationships-a-graph-of-digital-twins"></a>Relace: graf digitálních vláken

Vlákna se připojují k dvojitým grafům podle jejich vztahů. Vztahy, které mohou být zdvojeny, jsou definovány jako součást modelu.  

Modelová *podlaha* například může *definovat relaci,* která cílí na vlákna typu *Room* . V této definici vám digitální vlákna Azure umožní vytvořit *obsahuje* vztahy z libovolného vlákna na *podlaze* pro všechny místnosti s dvojitou *místností* (včetně vláken, která jsou podtypy *místnosti* ). 

Výsledkem tohoto procesu je sada uzlů (digitální vlákna) propojená přes hrany (jejich vztahy) v grafu.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Vytvoření pomocí rozhraní API

V této části se dozvíte, co vypadá při vytváření digitálních vláken a vztahů z klientské aplikace. Obsahuje příklady kódu .NET, které využívají [rozhraní API DigitalTwins](/rest/api/digital-twins/dataplane/twins), k poskytnutí dalších informací o tom, co se nachází uvnitř každé z těchto konceptů.

### <a name="create-digital-twins"></a>Vytváření digitálních vláken

Níže je fragment kódu klienta, který používá [rozhraní API DigitalTwins](/rest/api/digital-twins/dataplane/twins) k vytvoření instance vlákna typu *pokoj* .

Můžete inicializovat vlastnosti vlákna, když je vytvořen, nebo je nastavit později. Chcete-li vytvořit dvojitě spuštěný s inicializovanými vlastnostmi, vytvořte dokument JSON, který bude poskytovat nezbytné inicializační hodnoty.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

Můžete také použít pomocnou třídu nazvanou `BasicDigitalTwin` pro uložení polí vlastností do "" vlákna "objektu, a to více přímo jako alternativu k použití slovníku. Další informace o pomocné třídě a příkladech jejich použití naleznete v části [*vytvoření digitálního vlákna*](how-to-manage-twin.md#create-a-digital-twin) v tématu *Postupy: Správa digitálních vláken* .

>[!NOTE]
>I když jsou zdvojené vlastnosti považovány za volitelné, takže není nutné je inicializovat, musí být při vytvoření vlákna nastaveny všechny [komponenty](concepts-models.md#elements-of-a-model) **na vlákna.** Můžou to být prázdné objekty, ale samotné komponenty musí existovat.

### <a name="create-relationships"></a>Vytvoření relací

Tady je příklad klientského kódu, který používá [rozhraní API DigitalTwins](/rest/api/digital-twins/dataplane/twins) k vytvoření vztahu mezi digitálním *typem podlahového* typu s názvem *GroundFloor* a digitálním dvojitým názvem typu *místnosti* *Cafe* .

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>Reprezentace JSON prvků grafu

Data z digitálního vlákna a data relace jsou ukládána ve formátu JSON. To znamená, že při [dotazování na](how-to-query-graph.md) vytvářený graf v instanci digitálních vláken Azure bude výsledkem reprezentace digitálních vláken a relací, které jste vytvořili.

### <a name="digital-twin-json-format"></a>Formát digitálního vlákna JSON

Při reprezentaci jako objektu JSON zobrazí digitální vlákna následující pole:

| Název pole | Description |
| --- | --- |
| `$dtId` | Uživatelem zadaný řetězec představující ID digitálního vlákna |
| `$etag` | Standardní pole HTTP přiřazené webovým serverem |
| `$conformance` | Výčet, který obsahuje stav shody tohoto digitálního *vlákna (v* *souladu* s nevyhovujícími, *Neznámý* ) |
| `{propertyName}` | Hodnota vlastnosti ve formátu JSON ( `string` , typ čísla nebo objekt) |
| `$relationships` | Adresa URL cesty ke kolekci vztahů Toto pole chybí, pokud digitální vlákna neobsahuje žádné odchozí okraje vztahu. |
| `$metadata.$model` | Volitelné ID rozhraní modelu, které charakterizuje tuto digitální vlákna |
| `$metadata.{propertyName}.desiredValue` | [Pouze pro zapisovatelné vlastnosti] Požadovaná hodnota zadané vlastnosti |
| `$metadata.{propertyName}.desiredVersion` | [Pouze pro zapisovatelné vlastnosti] Verze požadované hodnoty |
| `$metadata.{propertyName}.ackVersion` | Verze potvrzená aplikací zařízení implementující digitální dvojitou vlákenou |
| `$metadata.{propertyName}.ackCode` | [Pouze pro zapisovatelné vlastnosti] `ack` Kód vrácený aplikací pro zařízení implementující digitální vlákna |
| `$metadata.{propertyName}.ackDescription` | [Pouze pro zapisovatelné vlastnosti] `ack` Popis vrácený aplikací pro zařízení implementující digitální dvojitou vlákenou |
| `{componentName}` | Objekt JSON obsahující hodnoty a metadata vlastnosti komponenty, podobně jako u kořenového objektu. Tento objekt existuje i v případě, že komponenta nemá žádné vlastnosti. |
| `{componentName}.{propertyName}` | Hodnota vlastnosti komponenty v kódu JSON ( `string` , typ čísla nebo objekt) |
| `{componentName}.$metadata` | Informace metadat pro komponentu, podobně jako na úrovni kořenového adresáře `$metadata` |

Tady je příklad digitálního vlákna formátovaného jako objekt JSON:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Formát JSON vztahu

Při reprezentaci jako objektu JSON se v relaci z digitálního vlákna zobrazí následující pole:

| Název pole | Description |
| --- | --- |
| `$relationshipId` | Uživatelem zadaný řetězec představující ID tohoto vztahu. Tento řetězec je jedinečný v kontextu zdrojového digitálního vlákna, což také znamená, že `sourceId`  +  `relationshipId` je jedinečný v kontextu instance digitálního vlákna Azure. |
| `$etag` | Standardní pole HTTP přiřazené webovým serverem |
| `$sourceId` | ID zdrojového digitálního vlákna |
| `$targetId` | ID cílového digitálního vlákna |
| `$relationshipName` | Název vztahu |
| `{propertyName}` | Volitelné Hodnota vlastnosti této relace ve formátu JSON ( `string` , typ čísla nebo objekt) |

Tady je příklad vztahu naformátovaného jako objekt JSON:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak spravovat prvky grafu pomocí rozhraní API digitálních vláken Azure:
* [*Postupy: Správa digitálních vláken*](how-to-manage-twin.md)
* [*Postupy: Správa dvojitých grafů s relacemi*](how-to-manage-graph.md)

Nebo se naučíte dotazovat se na graf s dvojitými podseznamy digitálních vláken Azure pro informace:
* [*Koncepty: dotazovací jazyk*](concepts-query-language.md)