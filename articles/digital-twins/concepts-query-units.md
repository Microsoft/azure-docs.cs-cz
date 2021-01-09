---
title: Jednotky dotazů v digitálních prozdvojeních Azure
titleSuffix: Azure Digital Twins
description: Pochopení konceptu fakturačních jednotek dotazů v Azure Digital revláken
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 86f2abb8bfb95d5b9e72936ca3e9464747c00b1c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049299"
---
# <a name="query-units-in-azure-digital-twins"></a>Jednotky dotazů v digitálních prozdvojeních Azure 

Jednotka pro dotazování digitálních vláken Azure **(qu)** je jednotka výpočtu na vyžádání, která se používá ke spouštění [dotazů digitálních vláken Azure](how-to-query-graph.md) pomocí [rozhraní API pro dotazy](/rest/api/digital-twins/dataplane/query). 

Vyabstrakcí systémové prostředky, jako jsou CPU, IOPS a paměť, které jsou nutné k provádění operací dotazů podporovaných pomocí digitálních vláken Azure, a umožňuje tak sledovat využití v jednotkách dotazů.

Množství jednotek dotazů spotřebovaných za provedení dotazu je ovlivněno...

* složitost dotazu
* velikost sady výsledků (takže dotaz vracející 10 výsledků bude spotřebovávat víc QUs než dotaz podobné složitosti, který vrací jenom jeden výsledek).

Tento článek vysvětluje, jak porozumět jednotkám dotazů a sledovat spotřebu jednotek dotazů.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Hledání využití jednotky dotazu v digitálních prozdvojeních Azure

Když spustíte dotaz pomocí [rozhraní API pro dotazování](/rest/api/digital-twins/dataplane/query)digitálních vláken Azure, můžete prozkoumat hlavičku odpovědi a sledovat počet QUs, které dotaz spotřeboval. V odpovědi odeslané zpět z digitálních vláken Azure hledejte "dotaz-poplatek".

Sady [SDK](how-to-use-apis-sdks.md) pro digitální vlákna Azure umožňují extrakci hlavičky poplatků za dotaz z reakce na stránku. V této části se dozvíte, jak zadávat dotazy na digitální vlákna a jak iterovat na základě stránkované reakce za účelem extrakce hlavičky dotazu. 

Následující fragment kódu ukazuje, jak lze extrahovat poplatky za dotaz vzniklé při volání rozhraní API pro dotazy. Při přístupu k hlavičce poplatků za dotaz vychází z odpovědí na stránky odpovědi a potom Iteruje na základě digitálního výsledku výsledků na každé stránce. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Další kroky

Další informace o dotazování na digitální vlákna Azure najdete tady:

* [*Koncepty: dotazovací jazyk*](concepts-query-language.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)
* [Referenční dokumentace k rozhraní API pro dotazy](/rest/api/digital-twins/dataplane/query/querytwins)

V [*referenčních informacích*](reference-service-limits.md)o omezeních pro služby můžete najít digitální vlákna Azure – omezení související s dotazy: omezení služby.