---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201945"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet add package](/dotnet/core/tools/dotnet-add-package) v okně Terminálu a přidejte balíček rozšíření úložiště do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nyní můžete přidat vazby výstupu úložiště do projektu.  
::: zone-end  