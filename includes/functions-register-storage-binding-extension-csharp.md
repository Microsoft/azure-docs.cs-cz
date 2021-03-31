---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201945"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nyní můžete přidat výstupní vazbu úložiště do projektu.  
::: zone-end  