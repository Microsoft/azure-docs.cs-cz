---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: 44969063765099d350e21abfbd07792891443911
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673122"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Vytvoření a aktivace virtuálního prostředí

Ve vhodné složce spusťte následující příkazy a `.venv`vytvořte a aktivujte virtuální prostředí s názvem . Nezapomeňte použít Python 3.8, 3.7 nebo 3.6, které jsou podporované funkcemi Azure.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do vaší distribuce Linuxu, spusťte následující příkaz:

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

Spuštění všech následných příkazů v tomto aktivovaném virtuálním prostředí. (Chcete-li virtuální prostředí `deactivate`ukončit, spusťte .)
::: zone-end