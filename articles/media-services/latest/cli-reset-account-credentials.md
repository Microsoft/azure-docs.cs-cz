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
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967573"
---
# <a name="cli-example-reset-the-account-credentials"></a>Příklad rozhraní příkazového řádku: Resetování přihlašovacích údajů účtu

Skript Azure CLI v tomto článku znázorňuje, jak resetovat přihlašovací údaje k účtu a získat zpět nastavení app.config.

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>Další postup

* [AZ AMS](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Resetovat přihlašovací údaje](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)
