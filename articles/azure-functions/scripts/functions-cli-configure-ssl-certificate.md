---
title: Ukázkový skript Azure CLI – Vazba vlastního certifikátu SSL na aplikaci funkcí | Microsoft Docs
description: Ukázkový skript Azure CLI – Vazba vlastního certifikátu SSL na aplikaci funkcí v Azure
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 689764543f5d927273f92deecbfd43e282fc028c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961361"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Vazba vlastního certifikátu SSL na aplikaci funkcí

Tento ukázkový skript vytvoří ve službě App Service aplikaci funkcí se souvisejícími prostředky a pak k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. Pro tuto ukázku potřebujete:

* Přístup ke konfigurační stránce DNS doménového registrátora.
* Platný soubor .PFX a heslo pro certifikát SSL, který chcete nahrát a svázat.
* Mít nakonfigurovaný záznam A ve vlastní doméně, která odkazuje na výchozí název domény vaší webové aplikace. Další informace najdete v tématu [Mapování existujícího vlastního názvu DNS na Azure Web Apps](https://aka.ms/appservicecustomdns).

Pokud chcete vytvořit vazbu certifikátu SSL, vaše aplikace funkcí musí být vytvořená v plánu služby App Service, a ne v plánu Consumption.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště, který vyžaduje aplikace funkcí. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Vytvoří plán služby App Service, který se vyžaduje k vytvoření vazby certifikátů SSL. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Vytvoří v plánu služby App Service aplikaci funkcí. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Namapuje na aplikaci funkcí vlastní doménu. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Nahraje do aplikace funkcí certifikát SSL. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Vytvoří vazbu nahraného certifikátu SSL na aplikaci funkcí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../functions-cli-samples.md).
