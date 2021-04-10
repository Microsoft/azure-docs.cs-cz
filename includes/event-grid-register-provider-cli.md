---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645407"
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
