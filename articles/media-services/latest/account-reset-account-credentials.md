---
title: Resetování přihlašovacích údajů k účtu – rozhraní příkazového řádku
description: Pomocí skriptu Azure CLI můžete resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783588"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Příklad rozhraní příkazového řádku Azure: resetování přihlašovacích údajů účtu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skript Azure CLI v tomto článku znázorňuje, jak resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./create-account-howto.md).

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

* [AZ AMS](/cli/azure/ams)
* [Resetování přihlašovacích údajů](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
