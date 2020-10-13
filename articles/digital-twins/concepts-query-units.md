---
title: Jednotky dotazů v digitálních prozdvojeních Azure
titleSuffix: Azure Digital Twins
description: Pochopení konceptu fakturačních jednotek dotazů v Azure Digital revláken
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c68cb8cc0ecf759b9af0e313e09663cdbc327917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89067701"
---
# <a name="query-units-in-azure-digital-twins"></a>Jednotky dotazů v digitálních prozdvojeních Azure 

Jednotka pro dotazování digitálních vláken Azure **(qu)** je jednotka výpočtu na vyžádání, která se používá ke spouštění [dotazů digitálních vláken Azure](how-to-query-graph.md) pomocí [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query). 

Vyabstrakcí systémové prostředky, jako jsou CPU, IOPS a paměť, které jsou nutné k provádění operací dotazů podporovaných pomocí digitálních vláken Azure, a umožňuje tak sledovat využití v jednotkách dotazů.

Množství jednotek dotazů spotřebovaných za provedení dotazu je ovlivněno...
* složitost dotazu 
* velikost sady výsledků (takže dotaz vracející 10 výsledků bude spotřebovávat víc QUs než dotaz podobné složitosti, který vrací jenom jeden výsledek).

Tento článek vysvětluje, jak porozumět jednotkám dotazů a sledovat spotřebu jednotek dotazů.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Hledání využití jednotky dotazu v digitálních prozdvojeních Azure 

Když spustíte dotaz pomocí [rozhraní API pro dotazování](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)digitálních vláken Azure, můžete prozkoumat hlavičku odpovědi a sledovat počet QUs, které dotaz spotřeboval. V odpovědi odeslané zpět z digitálních vláken Azure hledejte "dotaz-poplatek". 

Sady [SDK](how-to-use-apis-sdks.md) pro digitální vlákna Azure umožňují extrakci hlavičky poplatků za dotaz z reakce na stránku. V této části se dozvíte, jak zadávat dotazy na digitální vlákna a jak iterovat na základě stránkované reakce za účelem extrakce hlavičky dotazu. 

Následující fragment kódu ukazuje, jak lze extrahovat poplatky za dotaz vzniklé při volání rozhraní API pro dotazy. Při přístupu k hlavičce poplatků za dotaz vychází z odpovědí na stránky odpovědi a potom Iteruje na základě digitálního výsledku výsledků na každé stránce. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Další kroky

Další informace o dotazování na digitální vlákna Azure najdete tady:
* [*Koncepty: dotazovací jazyk*](concepts-query-language.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)
* [Referenční dokumentace k rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

V referenčních informacích o omezeních souvisejících s dotazy na digitální vlákna Azure můžete najít [*: omezení služby ve verzi Public Preview*](reference-service-limits.md).