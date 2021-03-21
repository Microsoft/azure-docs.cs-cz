---
title: Vytvoření aplikace Function App s nasazením DevOps – Azure CLI
description: Vytvoření aplikace funkcí a nasazení kódu funkce z Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f89da9fc146d753442f2a8c8aa38861e66c9a3d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934369"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Vytvoření funkce v Azure nasazené z Azure DevOps

V tomto tématu se dozvíte, jak pomocí Azure Functions vytvořit aplikaci s funkcemi bez [serveru](https://azure.microsoft.com/solutions/serverless/) s využitím [plánu spotřeby](../consumption-plan.md). Aplikace Function App, která je kontejnerem pro vaše funkce, je průběžně nasazena z úložiště Azure DevOps. 

K dokončení tohoto tématu potřebujete:

* Úložiště Azure DevOps obsahující váš projekt aplikace funkcí, ke kterému máte oprávnění správce
* [Token PAT](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) pro přístup k vašemu úložišti Azure DevOps

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a nasadí kód funkce z Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, účtu úložiště, aplikace funkcí a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště vyžadovaný aplikací funkcí. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu spotřeby](../consumption-plan.md)bez serveru. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Přidruží aplikaci funkcí k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
