---
title: 'Azure CLI ukázkový skript: vytvoření služby SignalR'
description: Ukázkový skript Azure CLI – Vytvoření služby SignalR
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 93674574bceb24b75b9af36708ddfe7e77ebf0fe
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565839"
---
# <a name="create-a-signalr-service"></a>Vytvoření služby SignalR 

Tento ukázkový skript vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tento skript používá rozšíření *signalr* pro Azure CLI. Před použitím tohoto ukázkového skriptu nainstalujte rozšíření *signalr* pro Azure CLI spuštěním následujícího příkazu:

```azurecli-interactive
az extension add -n signalr
```

Tento skript vytvoří nový prostředek služby SignalR a novou skupinu prostředků. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Poznamenejte si vygenerovaný název pro novou skupinu prostředků. Tento název skupiny prostředků použijete, když budete chtít odstranit všechny prostředky skupiny.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Každý příkaz v tabulce odkazuje na příslušnou část dokumentace. Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Vytvoří prostředek služby Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Vypíše klíče, které bude vaše aplikace používat při nabízení aktualizací obsahu v reálném čase pomocí SignalR. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure SignalR najdete v [dokumentaci ke službě Azure SignalR](../signalr-reference-cli.md).
