---
title: 'Azure CLI ukázkový skript: vytvoření služby SignalR službou App Service'
description: Ukázkový skript Azure CLI – Vytvoření služby SignalR s využitím služby App Service
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 007bf58c06d28f4445b3e57c5d692e3ee19b5b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257739"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Vytvoření služby SignalR s využitím služby App Service

Tento ukázkový skript vytvoří nový prostředek služby Azure SignalR, který slouží k nabízení aktualizací obsahu v reálném čase do klientů. Tento skript také přidá novou webovou aplikaci a plán služby App Service k hostování webové aplikace ASP.NET Core využívající službu SignalR. Webová aplikace je prostřednictvím nastavení aplikace *AzureSignalRConnectionString* nakonfigurovaná tak, aby se připojila k novému prostředku služby SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tento skript používá rozšíření *signalr* pro Azure CLI. Před použitím tohoto ukázkového skriptu nainstalujte rozšíření *signalr* pro Azure CLI spuštěním následujícího příkazu:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Poznamenejte si vygenerovaný název pro novou skupinu prostředků. Zobrazí se ve výstupu. Tento název skupiny prostředků použijete, když budete chtít odstranit všechny prostředky skupiny.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Vytvoří prostředek služby Azure SignalR. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Vypíše klíče, které bude vaše aplikace používat při nabízení aktualizací obsahu v reálném čase pomocí SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Vytvoří plán služby Azure App Service pro hostování webových aplikací. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Vytvoří webovou aplikaci Azure s použitím plánu hostování služby App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Přidá nové nastavení aplikace pro webovou aplikaci. Toto nastavení aplikace slouží k uchovávání připojovacího řetězce služby SignalR. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure SignalR najdete v [dokumentaci ke službě Azure SignalR](../signalr-cli-samples.md).
