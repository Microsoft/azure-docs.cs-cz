---
title: Vytvoření prostředí Azure Time Series Insights Gen2 pomocí Azure CLI – Azure Time Series Insights Gen2 | Microsoft Docs
description: Naučte se, jak nastavit prostředí v Azure Time Series Insights Gen2 pomocí Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: ed185413cff155610b2b088b1791169e33f6ce7a
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464433"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Vytvoření prostředí Azure Time Series Insights Gen2 pomocí Azure CLI

Tento dokument vás provede vytvořením nového prostředí Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

* Vytvořte účet úložiště Azure pro [chladírenský sklad](./concepts-storage.md#cold-store)vašeho prostředí. Tento účet je určený pro dlouhodobé uchovávání a analýzu historických dat.

> [!NOTE]
> V kódu nahraďte `mytsicoldstore` jedinečným názvem pro účet studeného úložiště.

Nejdřív vytvořte účet úložiště:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Vytváření prostředí

Teď, když je účet úložiště vytvořený a jeho název a klíč pro správu se přiřazují k proměnným, spusťte následující příkaz a vytvořte Azure Time Series Insights prostředí:

> [!NOTE]
> Ve svém kódu nahraďte následující jedinečnými názvy pro váš scénář:
>
> * `my-tsi-env` s názvem vašeho prostředí.
> * `my-ts-id-prop` názvem vaší vlastnosti Time Series ID.

> [!IMPORTANT]
> ID časové řady vašeho prostředí je jako klíč databázového oddílu. ID časové řady slouží také jako primární klíč pro model časových řad.
>
> Další informace najdete v tématu [osvědčené postupy pro výběr ID časové řady.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Odebrání prostředí Azure Time Series Insights

Pomocí Azure CLI můžete odstranit jednotlivý prostředek, například Time Series Insights prostředí, nebo odstranit skupinu prostředků a všechny její prostředky, včetně všech Time Series Insights prostředí.

Pokud chcete [odstranit Time Series Insights prostředí](/cli/azure/ext/timeseriesinsights/tsi/environment?view=azure-cli-latest#ext_timeseriesinsights_az_tsi_environment_delete), spusťte následující příkaz:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Pokud chcete [Odstranit účet úložiště](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete), spusťte následující příkaz:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Pokud chcete [Odstranit skupinu prostředků](/cli/azure/group#az-group-delete) a všechny její prostředky, spusťte následující příkaz:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zdrojích událostí příjmu streamování](./concepts-streaming-ingestion-event-sources.md) pro prostředí Azure Time Series Insights Gen2.
* Informace o tom, jak se připojit k [IoT Hub](./how-to-ingest-data-iot-hub.md)
