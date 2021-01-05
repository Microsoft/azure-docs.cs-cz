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
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
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
