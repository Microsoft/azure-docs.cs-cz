---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645390"
---
## <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid

Pokud jste ve vašem předplatném Azure ještě službu Event Grid nepoužívali, budete možná muset zaregistrovat poskytovatele prostředků služby Event Grid. Spusťte následující příkaz:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Dokončení registrace může chvíli trvat. Pokud chcete zkontrolovat stav, spusťte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Jakmile `RegistrationStatus` je `Registered`, můžete pokračovat.
