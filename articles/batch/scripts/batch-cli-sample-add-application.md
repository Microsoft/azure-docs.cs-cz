---
title: Ukázkový skript Azure CLI – Přidání aplikace ve službě Batch | Microsoft Docs
description: Ukázkový skript Azure CLI – Přidání aplikace ve službě Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: a407522e1c5e674dcaee2a4bf019bc858668969a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968726"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Příklad rozhraní příkazového řádku: Přidání aplikace do účtu Azure Batch

Tento skript ukazuje, jak přidat aplikaci pro použití s fondem nebo úlohou Azure Batch. Aplikaci nastavíte pro přidání do účtu Batch tak, že zabalíte spustitelný soubor spolu se všemi závislostmi do souboru zip. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy.
Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Vytvoří aplikaci.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Přidá balíček aplikace do zadané aplikace.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Aktualizuje vlastnosti aplikace.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
