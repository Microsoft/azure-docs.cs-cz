---
title: Ukázkový skript Azure CLI – práce s klíčovými hodnotami v App Configuration Storu
titleSuffix: Azure App Configuration
description: Poskytuje informace o práci s klíčovými hodnotami v úložišti konfigurace aplikace Azure.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 931c2f738556404e2d58381f7152acb0613a057b
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468146"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Práce s klíčovými hodnotami v úložišti konfigurace aplikace Azure

Tento ukázkový skript vytvoří novou klíčovou hodnotu v úložišti konfigurace aplikace Azure, vypíše všechny existující hodnoty klíčů, aktualizuje hodnotu nově vytvořeného klíče a poslední odstraní.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

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

Tento skript používá následující příkazy pro práci s klíčovými hodnotami v úložišti konfigurace aplikace. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky: |
|---|---|
| [AZ appconfig KV set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Vytvoří nebo aktualizuje klíčovou hodnotu. |
| [AZ appconfig KV list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Uvádí klíčové hodnoty v úložišti konfigurace aplikace. |
| [AZ appconfig KV DELETE](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Odstraní klíčovou hodnotu. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
