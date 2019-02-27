---
title: Ukázkový skript Azure CLI – práce s hodnotami klíče do Azure App Configuration Store | Dokumentace Microsoftu
description: Poskytuje informace o práci s hodnotami klíče v úložišti konfigurace aplikace pro Azure
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
ms.openlocfilehash: e69e2ca5ccd8e8edc2f55d74a0cca03eaabc9f49
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884738"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Práce s hodnoty klíče v úložišti konfigurace aplikace pro Azure

Tento ukázkový skript vytvoří nový klíč hodnota v úložišti konfigurace aplikace pro Azure, jsou uvedeny všechny existující hodnoty klíče, aktualizuje hodnotu na nově vytvořený klíč a nakonec odstraní.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Je třeba nejprve nainstalovat rozšíření Azure App konfigurace rozhraní příkazového řádku spuštěním následujícího příkazu:

        az extension add -n appconfig

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pracovat na klíč hodnota v obchodě s aplikacemi konfigurace. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Sada kv appconfig az](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-set) | Vytvoří nebo aktualizuje páru klíč hodnota. |
| [seznam kv appconfig az](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-list) | Uvádí klíč hodnota v obchodě s aplikacemi konfigurace. |
| [AZ appconfig kv delete](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-delete) | Odstraní páru klíč hodnota. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku konfiguraci aplikace najdete v [dokumentaci Azure aplikace konfigurační](../cli-samples.md).
