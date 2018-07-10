---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0946b703b45f69ede409167213f2629a796f18b5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869623"
---
## <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid

Pokud jste ve vašem předplatném Azure ještě službu Event Grid nepoužívali, budete možná muset zaregistrovat poskytovatele prostředků služby Event Grid. Spusťte následující příkaz:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Dokončení registrace může chvíli trvat. Pokud chcete zkontrolovat stav, spusťte:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Jakmile `RegistrationStatus` je `Registered`, můžete pokračovat.