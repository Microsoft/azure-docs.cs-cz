---
title: Ukázkový skript Azure CLI – import do úložiště konfigurace aplikace
titleSuffix: Azure App Configuration
description: Poskytuje informace a ukázkové skripty pro import do úložiště konfigurace aplikace Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28c04859f23b3a560166aa62ac903e1204fc5d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413293"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Import do úložiště konfigurace aplikace Azure

Tento ukázkový skript importuje nastavení klíč-hodnota do úložiště konfigurace aplikace Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Nejdřív musíte nainstalovat rozšíření rozhraní příkazového řádku Azure App Configuration, a to spuštěním následujícího příkazu:

        az extension add -n appconfig

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
| [AZ appconfig KV import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Importuje do prostředku úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
