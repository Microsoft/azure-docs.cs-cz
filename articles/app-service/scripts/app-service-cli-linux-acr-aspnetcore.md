---
title: Ukázkový skript Azure CLI – Vytvoření webové aplikace ASP.NET Core v kontejneru Dockeru ze služby Azure Container Registry | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření webové aplikace ASP.NET Core v kontejneru Dockeru ze služby Azure Container Registry
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: ec6fa209d9db700bfb525bd56ac11d9799b1f029
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Vytvoření webové aplikace ASP.NET Core v kontejneru Dockeru ze služby Azure Container Registry

Tento ukázkový skript vytvoří skupinu prostředků, plán služby App Service pro Linux a webovou aplikaci. Pak pomocí kontejneru Dockeru nasadí aplikaci ASP.NET Core ze služby Azure Container Registry.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Vytvoří webovou aplikaci Azure. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) | Nastaví kontejner Dockeru pro webovou aplikaci Azure. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../app-service-cli-samples.md).
