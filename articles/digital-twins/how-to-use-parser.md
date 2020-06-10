---
title: Analyzovat a ověřit modely
titleSuffix: Azure Digital Twins
description: Naučte se používat knihovnu analyzátorů k analýze DTDL modelů.
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6346c1d2adca697649ebe879b97766672a23ae29
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613065"
---
# <a name="dtdl-client-side-parser-library"></a>Knihovna analyzátorů DTDL na straně klienta

[Modely](concepts-models.md) v digitálních prostředníkech Azure jsou definované pomocí jazyka DTDL (Digital nenáročné definice) založené na formátu JSON. V případech, kdy je užitečné analyzovat vaše modely, je v NuGet.org k dispozici knihovna analýzy DTDL jako knihovna na straně klienta: [Microsoft. Azure. DigitalTwins. Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/).

Tato knihovna poskytuje model přístup k definicím DTDL, který v podstatě funguje jako ekvivalent jazyka C# pro DTDL. Tato knihovna se dá použít nezávisle na jakékoli [sadě SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md), zejména pro ověřování DTDL ve vizuálním nebo textovém editoru. Je vhodné zajistit, aby byly soubory definice modelu platné, než se pokusíte je odeslat do služby.

Chcete-li použít knihovnu analyzátoru, zadejte ji se sadou dokumentů DTDL. Obvykle byste tyto modelové dokumenty načetli ze služby, ale můžete je také zpřístupnit místně, pokud byl váš klient zodpovědný za jejich nahrání do služby na prvním místě. 

Tady je obecný pracovní postup pro použití analyzátoru:
1. Načte některé nebo všechny dokumenty DTDL ze služby.
2. Předejte vracené dokumenty DTDL v paměti do analyzátoru.
3. Analyzátor ověří sadu předaných dokumentů a vrátí podrobné informace o chybě. Tato možnost je užitečná ve scénářích editoru.
4. Použijte rozhraní API analyzátoru pro pokračování v analýze modelů obsažených v sadě dokumentů. 

Mezi schopnosti analyzátoru patří:
* Získat všechna implementovaná rozhraní modelu (obsah `extends` oddílu rozhraní).
* Získá všechny vlastnosti, telemetrie, příkazy, komponenty a vztahy deklarované v modelu. Tento příkaz také získá všechna metadata zahrnutá v těchto definicích a bere v `extends` úvahu dědičnost (oddíly).
* Získat všechny definice komplexního modelu
* Určete, zda lze model přiřadit z jiného modelu.

> [!NOTE]
> Zařízení [IoT technologie Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) používají k popisu jejich funkce malou variantu syntaxe. Tato varianta syntaxe je sémanticky kompatibilní podmnožinou DTDL, která se používá v digitálních Vlákenách Azure. Při použití knihovny analyzátoru nemusíte znát, která varianta syntaxe se použila k vytvoření DTDL pro digitální vlákna. Analyzátor bude ve výchozím nastavení vždycky vracet stejný model pro syntaxi digitálního vlákna PnP a Azure.

## <a name="use-the-dtdl-validator-sample"></a>Použití ukázky validátoru DTDL

K dispozici je ukázkový kód, který může ověřit modelové dokumenty a ujistit se, že je DTDL platný. Je postavená na knihovně analyzátorů DTDL a je to Language-nezávislá. Najdete ho tady: [Ukázka validátoru DTDL](https://github.com/Azure-Samples/DTDL-Validator).

Ukázku validátoru lze použít jako nástroj příkazového řádku k ověření stromu adresářů souborů DTDL. Poskytuje také interaktivní režim. Zdrojový kód ukazuje příklady použití knihovny analyzátoru.

Informace o tom, jak balíček ukázek do samostatného spustitelného souboru zabalit, najdete ve složce DTDL validátoru pro ověřování v souboru *Readme.MD* .

Po vytvoření samostatného balíčku a přidání spustitelného souboru do vaší cesty můžete validátor spustit pomocí tohoto příkazu v konzole na počítači:

```cmd/sh
DTDLValidator
```

V případě výchozích možností bude ukázka hledat `*.json` soubory v aktuálním adresáři a všech podadresářích. Následující možnost můžete také přidat, chcete-li ukázkové hledání v označeném adresáři a všech podadresářích pro soubory s příponou *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Můžete přidat `-i` možnost pro ukázku zadání interaktivního režimu:

```cmd/sh
DTDLValidator -i
```

Další informace o této ukázce naleznete v tématu zdrojový kód nebo spustit `DTDLValidator --help` .

## <a name="use-the-parser-library-in-code"></a>Použití knihovny analyzátorů v kódu

Knihovnu analyzátoru můžete také použít přímo k ověření modelů sami.

Pro podporu níže uvedeného příkladu kódu analyzátoru zvažte několik modelů definovaných v instanci digitálních vláken Azure:

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker`Model používá syntaxi *modelu schopností* , což znamená, že byl nainstalován ve službě připojením zařízení PnP, které tento model zveřejňuje.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

Následující kód ukazuje příklad použití knihovny analyzátoru pro reflektování těchto definic v jazyce C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

Až dokončíte psaní modelů, přečtěte si téma jak je nahrát (a provést jiné operace správy) pomocí rozhraní DigitalTwinsModels API:
* [Postupy: Správa zdvojeného modelu](how-to-manage-model.md)