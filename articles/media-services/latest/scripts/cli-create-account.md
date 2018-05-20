---
title: Ukázkový skript Azure CLI – Vytvoření účtu služby Azure Media Services| Microsoft Docs
description: Použijte skript Azure CLI k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 783dc0fd37f98a12d9240ad4b3ee72aa98212eff
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Ukázka CLI: Vytvoření účtu Azure Media Services

Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit účet služby Azure Media Services.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Vytvoří účet úložiště. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Vytvoří účet služby Media Services. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Vytvoří instanční objekt s heslem a nakonfiguruje jeho přístup k účtu služby Azure Media Services. 
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |


## <a name="next-steps"></a>Další kroky

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
