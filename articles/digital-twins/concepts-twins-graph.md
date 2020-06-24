---
title: Digitální dvojčata a graf dvojčat
titleSuffix: Azure Digital Twins
description: Pochopení konceptu digitálního vlákna a způsobu, jakým jejich vztahy vytvářejí graf.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 4e2b223337bbe5d46f314a8d2126ab562b937445
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84726077"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Pochopení digitálních vláken a jejich dvojitých grafů

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

V řešení digitálních vláken Azure jsou entity ve vašem prostředí reprezentovány pomocí **digitálních vláken**Azure. Digitální vlákna je instance jednoho z vašich vlastních definovaných [modelů](concepts-models.md). Dá se připojit k ostatním digitálním **závislostem prostřednictvím vztahů** , které tvoří **dvojitou graf**: Tento neřízený graf je reprezentace celého prostředí.

> [!TIP]
> "Digitální vlákna Azure" odkazuje na tuto službu Azure jako celek. "Digitální vlákna" nebo pouze "vlákna" odkazují na jednotlivé zdvojené uzly v rámci vaší instance služby.

## <a name="creating-digital-twins"></a>Vytváření digitálních vláken

Než budete moct vytvořit digitální dvojitou hodnotu v instanci digitálních vláken Azure, musíte mít do této služby nahraný *model* . Model popisuje sadu vlastností, zpráv telemetrie a vztahy, které mohou mít konkrétní vlákna, mimo jiné. Typy informací, které jsou definovány v modelu, naleznete v tématu [Koncepty: vlastní modely](concepts-models.md).

Po vytvoření a nahrání modelu může klientská aplikace vytvořit instanci typu; Jedná se o digitální dvojitou hodnotu. Například po vytvoření modelu *podlahy*můžete vytvořit jednu nebo několik digitálních vláken, která používají tento typ (například dvojitou hodnotu typu *podlah-Floor*s názvem *GroundFloor*, další s názvem *Floor2*atd.). 

Níže je fragment kódu klienta, který používá [rozhraní API DigitalTwins](how-to-use-apis-sdks.md) k vytvoření instance vlákna typu *pokoj*.

V aktuální verzi Preview digitálních vláken Azure musí být všechny vlastnosti vlákna inicializované předtím, než může být vytvořena dvojitá vlákna. To se provádí vytvořením dokumentu JSON, který poskytuje nezbytné inicializační hodnoty.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

## <a name="relationships-creating-a-graph-of-digital-twins"></a>Vztahy: vytváření grafu digitálních vláken

Vlákna se připojují k dvojitým grafům podle jejich vztahů. Vztahy, které mohou být zdvojeny, jsou definovány jako součást modelu.  

Modelová *podlaha* například může *definovat relaci,* která cílí na vlákna typu *Room*. V této definici vám digitální vlákna Azure umožní vytvořit *obsahuje* vztahy z libovolného vlákna na *podlaze* pro všechny místnosti s dvojitou *místností* (včetně vláken, která jsou podtypy *místnosti* ). 

Tady je příklad klientského kódu, který používá [rozhraní API DigitalTwins](how-to-use-apis-sdks.md) k vytvoření vztahu mezi digitálním *typem podlahového*typu s názvem *GroundFloor* a digitálním dvojitým názvem typu *místnosti* *Cafe*.

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

Výsledkem tohoto procesu je sada uzlů (digitální vlákna) propojená přes hrany (jejich vztahy) v grafu.

## <a name="json-representations-of-graph-elements"></a>Reprezentace JSON prvků grafu

Data z digitálního vlákna a data relace jsou ukládána ve formátu JSON. To znamená, že při [dotazování na](how-to-query-graph.md) vytvářený graf v instanci digitálních vláken Azure bude výsledkem reprezentace digitálních vláken a relací, které jste vytvořili.

### <a name="digital-twin-json-format"></a>Formát digitálního vlákna JSON

Při reprezentaci jako objektu JSON zobrazí digitální vlákna následující pole:

| Název pole | Description |
| --- | --- |
| `$dtId` | Uživatelem zadaný řetězec představující ID digitálního vlákna |
| `$conformance` | Výčet, který obsahuje stav shody tohoto digitálního*vlákna (v* *souladu*s nevyhovujícími, *Neznámý*) |
| `{propertyName}` | Hodnota vlastnosti ve formátu JSON ( `string` , typ čísla nebo objekt) |
| `$relationships` | Adresa URL cesty ke kolekci vztahů Toto pole chybí, pokud digitální vlákna neobsahuje žádné odchozí okraje vztahu. |
| `$metadata.$model` | Volitelné ID rozhraní modelu, které charakterizuje tuto digitální vlákna |
| `$metadata.{propertyName}.desiredValue` | [Pouze pro zapisovatelné vlastnosti] Požadovaná hodnota zadané vlastnosti |
| `$metadata.{propertyName}.desiredVersion` | [Pouze pro zapisovatelné vlastnosti] Verze požadované hodnoty |
| `$metadata.{propertyName}.ackVersion` | Verze potvrzená aplikací zařízení implementující digitální dvojitou vlákenou |
| `$metadata.{propertyName}.ackCode` | [Pouze pro zapisovatelné vlastnosti] `ack`Kód vrácený aplikací pro zařízení implementující digitální vlákna |
| `$metadata.{propertyName}.ackDescription` | [Pouze pro zapisovatelné vlastnosti] `ack`Popis vrácený aplikací pro zařízení implementující digitální dvojitou vlákenou |
| `{componentName}` | Objekt JSON obsahující hodnoty a metadata vlastnosti komponenty, podobně jako u kořenového objektu. Tento objekt existuje i v případě, že komponenta nemá žádné vlastnosti. |
| `{componentName}.{propertyName}` | Hodnota vlastnosti komponenty v kódu JSON ( `string` , typ čísla nebo objekt) |
| `{componentName}.$metadata` | Informace metadat pro komponentu, podobně jako na úrovni kořenového adresáře`$metadata` |

Tady je příklad digitálního vlákna formátovaného jako objekt JSON:

```json
{
  "$dtId": "Cafe",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "$model": "dtmi:com:contoso:Table;1",
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
| `$edgeId` | Uživatelem zadaný řetězec představující ID této hraniční relace. Tento řetězec je jedinečný v kontextu zdrojového digitálního vlákna, což také znamená, že `sourceId`  +  `edgeId` je jedinečný v kontextu instance digitálního vlákna Azure. |
| `$sourceId` | ID zdrojového digitálního vlákna |
| `$targetId` | ID cílového digitálního vlákna |
| `$relationshipName` | Název vztahu |
| `{propertyName}` | Volitelné Hodnota vlastnosti této relace ve formátu JSON ( `string` , typ čísla nebo objekt) |

Tady je příklad vztahu naformátovaného jako objekt JSON:

```json
{
  "$edgeId": "Edge-01",
  "$sourceId": "GroundFloor",
  "$relationship": "contains",
  "$targetId": "Cafe",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak spravovat prvky grafu pomocí rozhraní API digitálních vláken Azure:
* [Postupy: Správa digitálního vlákna](how-to-manage-twin.md)
* [Postupy: Správa zdvojeného grafu s relacemi](how-to-manage-graph.md)

Nebo se naučíte dotazovat se na graf s dvojitými podseznamy digitálních vláken Azure pro informace:
* [Koncepty: dotazovací jazyk](concepts-query-language.md)