---
title: Vytvoření funkce v Azure nasazené z GitHubu | Microsoft Docs
description: Vytvořte aplikaci funkcí a nasaďte kód funkce z úložiště GitHub pomocí služby Azure Functions.
services: functions
ms.service: functions
keywords: ''
ms.devlang: azurecli
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: b44126f6bf191ee57bfea7017a4f4d87a57c01f1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989856"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Vytvoření aplikace funkcí v Azure nasazené z GitHubu

Tento ukázkový skript Azure Functions vytvoří aplikaci funkcí s použitím [plánu Consumption](../functions-scale.md#consumption-plan) a její související prostředky. Skript také nakonfiguruje kód vaší funkce pro průběžné nasazování z úložiště GitHub. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

V této ukázce potřebujete:

* Úložiště GitHub s kódem funkcí, ke kterému máte oprávnění správce.
* [Token PAT](https://help.github.com/articles/creating-an-access-token-for-command-line-use) pro váš účet GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud raději používáte Azure CLI místně, musíte nainstalovat a používat verzi 2.0 nebo novější. Verzi Azure CLI zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a nasadí kód funkce z GitHubu.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště vyžadovaný aplikací funkcí. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu Consumption](../functions-scale.md#consumption-plan) bez serveru a přidruží ji k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
