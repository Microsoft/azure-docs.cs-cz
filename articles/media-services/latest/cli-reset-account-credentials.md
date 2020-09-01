---
title: Ukázkový skript Azure CLI – Resetování přihlašovacích údajů k účtu | Microsoft Docs
description: Pomocí skriptu Azure CLI můžete resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90ce5ec3a53e2ca707512e6f534d6b242f513d3b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269739"
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
