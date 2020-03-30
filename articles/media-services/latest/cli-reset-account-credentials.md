---
title: Ukázkový skript Azure CLI – Resetování přihlašovacích údajů k účtu | Microsoft Docs
description: Pomocí skriptu Azure CLI můžete resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.
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
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382983"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Příklad nastavení příkazového příkazu k Azure: Obnovení přihlašovacích údajů účtu

Skript Azure CLI v tomto článku znázorňuje, jak resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet mediálních služeb](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Další kroky

* [az ams](/cli/azure/ams)
* [Resetování přihlašovacích údajů](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
