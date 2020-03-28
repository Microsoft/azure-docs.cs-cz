---
title: Ukázka skriptu příkazového příkazu Azure – export z konfiguračního obchodu s aplikacemi Azure
titleSuffix: Azure App Configuration
description: Použití skriptu Azure CLI k exportu konfigurace z konfigurace aplikace Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 3e014504fc1e94c3b323a49b408fa992de00d14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523615"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Export z obchodu konfigurace aplikací Azure

Tento ukázkový skript exportuje hodnoty klíčů z úložiště Konfigurace aplikace Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro export z obchodu konfigurace aplikací. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az appconfig kv export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exportuje z prostředku úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptu příkazového příkazu konfigurace aplikace najdete v [ukázkách příkazového příkazu konfigurace aplikace Azure](../cli-samples.md).
