---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "72329622"
---
Azure Functions umožňuje připojit služby Azure a další prostředky k funkcím bez nutnosti psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb má funkce k dispozici jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](../articles/azure-functions/functions-triggers-bindings.md).