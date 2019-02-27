---
title: Ukázkový skript Azure CLI – importovat do Azure App Configuration Store | Dokumentace Microsoftu
description: Poskytuje informace a ukázky skriptů pro import do úložiště Azure konfigurace aplikace
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
ms.openlocfilehash: 65fa09b779f656654411a1d0c3602598655f50c9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884774"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importovat do úložiště Azure konfigurace aplikace

Tento ukázkový skript importuje hodnoty klíče k úložišti konfigurace aplikace pro Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Je třeba nejprve nainstalovat rozšíření Azure App konfigurace rozhraní příkazového řádku spuštěním následujícího příkazu:

        az extension add -n appconfig

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --file ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro import konfigurace app storu. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ appconfig import](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-import) | Importy do konfigurace aplikace ukládání prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku konfiguraci aplikace najdete v [dokumentaci Azure aplikace konfigurační](../cli-samples.md).
