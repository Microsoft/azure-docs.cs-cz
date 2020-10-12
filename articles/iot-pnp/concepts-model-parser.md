---
title: Principy analyzátoru modelu digitálních vláken | Microsoft Docs
description: Jako vývojář se naučíte používat analyzátor DTDL k ověřování modelů.
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352186"
---
# <a name="understand-the-digital-twins-model-parser"></a>Principy analyzátoru modelů digitálních dvojčat

DTDL (Digital autodefinition Definition Language) je popsána ve [specifikaci DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Uživatelé můžou k ověření a dotazování modelu definovaného ve více souborech použít balíček NuGet pro _analyzátor modelů vláken s digitálními_ podmnožinami.

## <a name="install-the-dtdl-model-parser"></a>Instalace analyzátoru modelu DTDL

Analyzátor je k dispozici v NuGet.org s ID: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Chcete-li nainstalovat analyzátor, použijte libovolný kompatibilní správce balíčků NuGet, jako je ten v aplikaci Visual Studio nebo v rozhraní příkazového `dotnet` řádku.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Použití analyzátoru k ověření modelu

Model, který chcete ověřit, se může skládat z jednoho nebo více rozhraní popsaných v souborech JSON. Pomocí analyzátoru můžete načíst všechny soubory v dané složce a použít analyzátor k ověření všech souborů jako celku včetně všech odkazů mezi soubory:

1. Vytvořit `IEnumerable<string>` se seznamem všech modelů obsahu:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Vytvoření instance `ModelParser` volání a `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Kontrola chyb ověřování. Pokud analyzátor najde nějaké chyby, vyvolá `AggregateException` se se seznamem podrobných chybových zpráv:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Zkontrolujte `Model` . V případě úspěšného ověření můžete model zkontrolovat pomocí rozhraní API analyzátoru modelů. Následující fragment kódu ukazuje, jak iterovat všechny modely analyzovány a zobrazuje existující vlastnosti:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Další kroky

Rozhraní API analyzátoru modelů uvedené v tomto článku umožňuje mnoha scénářům automatizovat nebo ověřovat úlohy, které jsou závislé na DTDLch modelech. Můžete například dynamicky vytvořit uživatelské rozhraní z informací v modelu.
