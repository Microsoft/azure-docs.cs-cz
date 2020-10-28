---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736844"
---
## <a name="enable-the-event-grid-resource-provider"></a>Povolit poskytovatele prostředků Event Grid

Pokud jste se dřív nepoužívali Event Grid ve vašem předplatném Azure, možná budete muset zaregistrovat poskytovatele prostředků Event Grid. Spuštěním následujícího příkazu zaregistrujte poskytovatele:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Dokončení registrace může chvíli trvat. Pokud chcete zkontrolovat stav, spusťte:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Jakmile `registrationState` je `Registered`, můžete pokračovat.
