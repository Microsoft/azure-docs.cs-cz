---
title: Ukázka skriptu příkazového příkazu Azure – práce s hodnotami klíčů v Úložišti konfigurace aplikací
titleSuffix: Azure App Configuration
description: Použití skriptu Azure CLI k vytváření, zobrazení, aktualizaci a odstraňování hodnot klíčů z úložiště konfigurace aplikací
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 1a4edabe666a554ccd01d110f0f71226221dfc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523641"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Práce s hodnotami klíčů v úložišti Azure App Configuration Store

Tento ukázkový skript ukazuje, jak:
* Vytvoření nového páru klíč-hodnota
* Seznam všech existujících párů klíč-hodnota
* Aktualizace hodnoty nově vytvořeného klíče
* Odstranění nového páru klíč-hodnota

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Tento článek vyžaduje Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

V této tabulce jsou uvedeny příkazy použité v našem ukázkovém skriptu. 

| Příkaz | Poznámky |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Vytvořte nebo aktualizujte dvojici klíč-hodnota. |
| [az appconfig kv seznam](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Seznam párů klíč hodnota v úložišti konfigurace aplikace. |
| [az appconfig kv odstranit](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Odstraňte dvojici klíč-hodnota. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptu příkazového příkazu konfigurace aplikace najdete v [ukázkách příkazového příkazu konfigurace aplikace Azure](../cli-samples.md).
