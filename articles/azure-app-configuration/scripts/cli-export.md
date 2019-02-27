---
title: Ukázkový skript Azure CLI – Export ze Azure App Configuration Store | Dokumentace Microsoftu
description: Poskytuje informace a ukázky skriptů pro export z úložiště konfigurace aplikace pro Azure
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
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884782"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Export z úložiště konfigurace aplikace pro Azure

Tento ukázkový skript exportuje hodnoty klíče z konfigurace aplikace pro Azure storu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Je třeba nejprve nainstalovat rozšíření Azure App konfigurace rozhraní příkazového řádku spuštěním následujícího příkazu:

        az extension add -n appconfig

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k exportu konfigurace app storu. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | Exporty z konfigurace aplikace ukládání prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku konfiguraci aplikace najdete v [dokumentaci Azure aplikace konfigurační](../cli-samples.md).
