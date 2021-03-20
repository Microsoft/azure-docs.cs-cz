---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: f89383c1b7d0ccce82f35d91acbe1ff8c902db29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84448902"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Vytvoření a aktivace virtuálního prostředí

Ve vhodné složce spusťte následující příkazy, abyste vytvořili a aktivovali virtuální prostředí s názvem `.venv` . Ujistěte se, že používáte Python 3,8, 3,7 nebo 3,6, které podporuje Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do distribuce systému Linux, spusťte následující příkaz:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

V tomto aktivovaném virtuálním prostředí spustíte všechny následné příkazy.   
::: zone-end
