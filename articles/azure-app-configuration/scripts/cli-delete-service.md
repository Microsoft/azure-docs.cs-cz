---
title: Ukázkový skript Azure CLI – odstranění úložiště konfigurace aplikace Azure
titleSuffix: Azure App Configuration
description: Ukázkový skript Azure CLI – odstranění úložiště konfigurace aplikace Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 9f18cb79e4c160271fbb3855b1fc6e05589cf854
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899441"
---
# <a name="delete-an-azure-app-configuration-store"></a>Odstranění úložiště konfigurace aplikace Azure

Tento ukázkový skript odstraní instanci Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Nejdřív musíte nainstalovat rozšíření rozhraní příkazového řádku Azure App Configuration, a to spuštěním následujícího příkazu:

        az extension add -n appconfig

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů odstraní úložiště konfigurace aplikace. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ appconfig DELETE](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-delete) | Odstraní prostředek úložiště konfigurace aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
