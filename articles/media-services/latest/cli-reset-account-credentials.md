---
title: Ukázkový skript Azure CLI – resetování přihlašovacích údajů k účtu
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
ms.openlocfilehash: 2b9b95af79b8aac11f56fe576f860d719b5fb50e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897675"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Příklad rozhraní příkazového řádku Azure: resetování přihlašovacích údajů účtu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skript Azure CLI v tomto článku znázorňuje, jak resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./create-account-howto.md).

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

* [AZ AMS](/cli/azure/ams)
* [Resetování přihlašovacích údajů](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
