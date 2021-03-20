---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97826636"
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
