---
title: Vytvoření aplikace funkcí pomocí nasazení GitHubu – Azure CLI
description: Vytvořte aplikaci funkcí a nasaďte kód funkce z úložiště GitHub pomocí služby Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 752d8a729c56fc2e0e110fcedc263eab4ba7a96b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565303"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Vytvoření aplikace funkcí v Azure nasazené z GitHubu

Tento Azure Functions ukázkový skript vytvoří aplikaci funkcí pomocí [plánu spotřeby](../functions-scale.md#consumption-plan)společně se souvisejícími prostředky. Skript také nakonfiguruje kód vaší funkce pro průběžné nasazování z úložiště GitHub. 

V této ukázce potřebujete:

* Úložiště GitHub s kódem funkcí, ke kterému máte oprávnění správce.
* [Token PAT](https://help.github.com/articles/creating-an-access-token-for-command-line-use) pro váš účet GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a nasadí kód funkce z GitHubu.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště vyžadovaný aplikací funkcí. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu spotřeby](../functions-scale.md#consumption-plan) bez serveru a přidruží ji k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
