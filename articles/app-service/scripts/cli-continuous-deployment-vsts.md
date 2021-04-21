---
title: Průběžné nasazování z Azure Repos
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak nastavit CI/CD z Azure Repos.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: cfc9ffff21db92eef4a3a544cb042394078a4e6d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788020"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>Vytvoření aplikace App Service s průběžným nasazováním pomocí Azure CLI

Tento ukázkový skript vytvoří aplikaci v App Service se souvisejícími prostředky a pak nastaví průběžné nasazování z úložiště Azure DevOps. Pro tuto ukázku potřebujete:

* Úložiště Azure DevOps s kódem aplikace, ke kterému máte oprávnění správce.
* [Token PAT](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) pro vaši organizaci Azure DevOps

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Vytvoří aplikaci App Service. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Přidruží aplikaci App Service k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
