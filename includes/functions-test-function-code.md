---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132167"
---
## <a name="test"></a>Testování funkce v Azure

Otestujete nasazenou funkci pomocí cURL. Pomocí adresy URL, kterou jste zkopírovali v předchozím kroku, připojte řetězec dotazu `&name=<yourname>` na adresu URL, jako v následujícím příkladu:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![pomocí příkazu cURL k volání funkce v Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Můžete také vložit zkopírovanou adresu URL na adresu ve webovém prohlížeči. Znovu připojte řetězec dotazu `&name=<yourname>` na adresu URL předtím, než provedete požadavek.

![Pomocí webového prohlížeče pro volání funkce.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
