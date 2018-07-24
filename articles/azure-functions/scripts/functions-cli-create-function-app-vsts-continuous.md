---
title: Vytvoření funkce v Azure nasazené z Visual Studio Team Services | Microsoft Docs
description: Vytvoření aplikace funkcí a nasazení kódu funkce z Visual Studio Team Services
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988948"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Vytvoření aplikace funkcí a nasazení kódu funkce z Visual Studio Team Services

Toto téma ukazuje použití služby Azure Functions k vytvoření aplikace funkcí [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) s použitím [plánu Consumption](../functions-scale.md#consumption-plan). Aplikace funkcí, což je kontejner pro vaše funkce, se průběžně nasazuje z úložiště Visual Studio Team Services (VSTS). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

K dokončení tohoto tématu potřebujete:

* Úložiště VSTS obsahující váš projekt aplikace funkcí, ke kterému máte oprávnění správce.
* [Token PAT](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) pro přístup k vašemu úložišti VSTS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud raději používáte Azure CLI místně, musíte nainstalovat a používat verzi 2.0 nebo novější. Verzi Azure CLI zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a nasadí kód funkce z Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, účtu úložiště, aplikace funkcí a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště vyžadovaný aplikací funkcí. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu Consumption](../functions-scale.md#consumption-plan) bez serveru. |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Přidruží aplikaci funkcí k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
