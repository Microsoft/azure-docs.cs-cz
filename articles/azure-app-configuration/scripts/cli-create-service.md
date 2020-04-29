---
title: Ukázkový skript Azure CLI – vytvoření úložiště konfigurace aplikace Azure
titleSuffix: Azure App Configuration
description: Vytvoření úložiště konfigurace aplikace Azure pomocí skriptu Azure CLI
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: lcozzens
ms.openlocfilehash: 667cf9545d87bd9277c59d066933710339b6c079
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77623088"
---
# <a name="create-an-azure-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace Azure

Tento ukázkový skript vytvoří novou instanci konfigurace aplikace Azure v nové skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Poznamenejte si vygenerovaný název pro novou skupinu prostředků. Tento název skupiny prostředků použijete, když budete chtít odstranit všechny prostředky skupiny.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření nové skupiny prostředků a úložiště konfigurace aplikace. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ appconfig Create](/cli/azure/appconfig#az-appconfig-create) | Vytvoří prostředek úložiště konfigurace aplikace. |
| [AZ appconfig Credential list](/cli/azure/appconfig/credential#az-appconfig-credential-list) | Vypíše přístupové klíče pro úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
