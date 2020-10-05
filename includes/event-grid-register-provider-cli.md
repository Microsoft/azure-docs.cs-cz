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
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87830115"
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