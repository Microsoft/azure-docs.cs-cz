---
title: Vytvoření účtu služby Azure Media Services – Azure CLI | Microsoft Docs
description: Použijte skript Azure CLI k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: a0ca9d8b51bc3cd5ed46b56a4d7fedb45211b136
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613486"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Ukázka CLI: Vytvoření účtu Azure Media Services

Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit účet služby Azure Media Services.

## <a name="prerequisites"></a>Požadavky 

Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Vytvoří účet služby Media Services. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Vytvoří instanční objekt s heslem a nakonfiguruje jeho přístup k účtu služby Azure Media Services. 
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |


## <a name="next-steps"></a>Další postup

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
