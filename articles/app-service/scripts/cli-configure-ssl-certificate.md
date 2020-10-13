---
title: 'CLI: nahrání a vazba certifikátu TLS/SSL do aplikace'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak vytvořit vazby vlastního certifikátu TLS/SSL k aplikaci.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 18dc50302b4a77d8e3e7683306e2aa4d07ef5fe8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962362"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Vytvoření vazby vlastního certifikátu TLS/SSL k aplikaci App Service pomocí rozhraní příkazového řádku

Tento ukázkový skript vytvoří aplikaci v App Service se souvisejícími prostředky a pak naváže certifikát TLS/SSL vlastního názvu domény. Pro tuto ukázku potřebujete:

* Přístup ke konfigurační stránce DNS doménového registrátora.
* Platná. Soubor PFX a jeho heslo pro certifikát TLS/SSL, který chcete nahrát a vytvořit.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří aplikaci App Service. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Namapuje vlastní doménu na App Service aplikaci. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Nahraje certifikát TLS/SSL do aplikace App Service. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Vytvoří připojení nahraného certifikátu TLS/SSL k aplikaci App Service. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).