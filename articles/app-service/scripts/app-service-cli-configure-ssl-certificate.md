---
title: Ukázkový skript Azure CLI – Vazba vlastního certifikátu SSL na webovou aplikaci | Microsoft Docs
description: Ukázkový skript Azure CLI – Vazba vlastního certifikátu SSL na webovou aplikaci
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f806b523543657e4241ec5bfbb4b84b85ebd9355
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
ms.locfileid: "30281381"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Vazba vlastního certifikátu SSL na webovou aplikaci

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. Pro tuto ukázku potřebujete:

* Přístup ke konfigurační stránce DNS doménového registrátora.
* Platný soubor .PFX a heslo pro certifikát SSL, který chcete nahrát a svázat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří webovou aplikaci Azure. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Namapuje na webovou aplikaci vlastní doménu. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Nahraje do webové aplikace certifikát SSL. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Vytvoří vazbu nahraného certifikátu SSL na webovou aplikaci. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../app-service-cli-samples.md).
