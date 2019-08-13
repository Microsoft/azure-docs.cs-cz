---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949948"
---
## <a name="create-the-local-function-app-project"></a>Vytvoření projektu funkce místní aplikace

Spuštěním následujícího příkazu z příkazového řádku vytvoříte projekt aplikace funkcí ve složce `MyFunctionProj` aktuálního místního adresáře. Úložiště GitHub se také vytvoří v `MyFunctionProj`.

```command
func init MyFunctionProj
```

Po zobrazení výzvy vyberte modul runtime pracovního procesu z následujících možností jazyka:

+ `dotnet`: vytvoří projekt knihovny třídy .NET (.csproj).
+ `node`: vytvoří projekt založený na Node. js. Vyberte buď `javascript` nebo `typescript`. 
+ `python`: v případě projektu v Pythonu prosím, proveďte kompletní [Vytvoření funkce aktivované protokolem HTTP v Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: v projektu PowerShellu prosím [Vytvořte svoji první funkci PowerShellu v Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


Po vytvoření projektu použijte následující příkaz pro přechod do složky nového `MyFunctionProj` projektu.

```command
cd MyFunctionProj
```
