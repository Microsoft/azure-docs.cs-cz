---
title: Ukázka skriptu Azure CLI – import do úložiště konfigurace aplikací
titleSuffix: Azure App Configuration
description: Použití skriptu Nastavení příkazu Azure – import konfigurace do konfigurace aplikací Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523590"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Import do úložiště konfigurace aplikací Azure

Tento ukázkový skript importuje nastavení klíč-hodnota do úložiště Konfigurace aplikace Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud chcete nainstalovat nebo upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k importu do obchodu konfigurace aplikací. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importuje do prostředku úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o azure cli najdete v [dokumentaci k azure cli](/cli/azure).

Další ukázky skriptu příkazového příkazu konfigurace aplikace najdete v [ukázkách příkazového příkazu konfigurace aplikace Azure](../cli-samples.md).
