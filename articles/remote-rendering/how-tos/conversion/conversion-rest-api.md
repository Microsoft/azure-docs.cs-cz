---
title: REST API převodu assetu
description: Popisuje, jak převést Asset prostřednictvím REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: f33e5717cd5556e72d996e7e943867c16805e71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705173"
---
# <a name="use-the-model-conversion-rest-api"></a>Použití rozhraní REST API pro převod modelů

Služba [převodu modelů](model-conversion.md) je řízena prostřednictvím [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). Toto rozhraní API je možné použít k vytvoření převodů, získání vlastností převodu a výpisu existujících převodů.

## <a name="rest-api-reference"></a>REST API – referenční informace

Informace o vzdáleném vykreslování REST API najdete [tady](/rest/api/mixedreality/2021-01-01preview/remoterendering)a definice Swagger [tady](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Ve složce *Scripts* poskytujeme skript prostředí PowerShell *Conversion.ps1*, který ukazuje použití naší služby v [úložišti ukázek ARR](https://github.com/Azure/azure-remote-rendering) . Skript a jeho konfigurace jsou popsané tady: [Příklady skriptů PowerShellu](../../samples/powershell-example-scripts.md). Poskytujeme také sady SDK pro [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java a Python.

## <a name="next-steps"></a>Další kroky

- [Použití služby Azure Blob Storage pro převod modelů](blob-storage.md)
- [Převod modelu](model-conversion.md)