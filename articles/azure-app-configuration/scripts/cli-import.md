---
title: Ukázkový skript Azure CLI – import do úložiště konfigurace aplikace
titleSuffix: Azure App Configuration
description: Použití skriptu Azure CLI – import konfigurace do Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77523590"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Import do úložiště konfigurace aplikace Azure

Tento ukázkový skript importuje nastavení klíč-hodnota do úložiště konfigurace aplikace Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro import do úložiště konfigurace aplikace. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ appconfig KV import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importuje do prostředku úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
