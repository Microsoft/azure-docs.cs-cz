---
title: Ukázkový skript Azure CLI – Mapování vlastní domény na aplikaci funkcí | Microsoft Docs
description: Ukázkový skript Azure CLI – Mapování vlastní domény na aplikaci funkcí v Azure
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 790d095cfb1b59aed1b9014fc474f9ad6e1b3328
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131307"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapování vlastní domény na aplikaci funkcí

Tento ukázkový skript vytvoří v plánu služby App Service aplikaci funkcí a potom ji namapuje na vámi poskytnutou vlastní doménu. Když je aplikaci funkcí hostované v [plán Premium](../functions-scale.md#premium-plan-public-preview) nebo [plán služby App Service](../functions-scale.md#app-service-plan), můžete namapovat vlastní doménu pomocí záznamu A nebo záznam CNAME. Pro aplikace funkcí v [plánu Consumption](../functions-scale.md#consumption-plan) se podporuje pouze možnost CNAME. Tento příklad vytvoří plán služby App Service a k namapování domény vyžaduje záznam A. 

Pokud chcete tento ukázkový skript spustit, musíte už mít nakonfigurovaný záznam A ve vlastní doméně, která odkazuje na výchozí název domény vaší webové aplikace. Další informace najdete v tématu [Mapování existujícího vlastního názvu DNS na Azure Web Apps](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště, který vyžaduje aplikace funkcí. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Vytvoří plán služby App Service, který se vyžaduje k namapování vlastní domény. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Vytvoří v plánu služby App Service aplikaci funkcí. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Namapuje na aplikaci funkcí vlastní doménu. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
