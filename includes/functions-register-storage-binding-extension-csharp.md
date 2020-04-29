---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
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