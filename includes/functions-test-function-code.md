---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279438"
---
## <a name="test"></a>Ověření funkce v Azure

K ověření nasazené funkce můžete použít webový prohlížeč.  Zkopírujte adresu URL, včetně klávesy funkce, do adresního řádku webového prohlížeče. Před provedením žádosti připojit řetězec dotazu `&name=<yourname>` k adrese URL.

![Volání funkce pomocí webového prohlížeče.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Alternativně můžete pomocí elementu kudrlinkou ověřit nasazenou funkci. Pomocí adresy URL, včetně klíč funkce, který jste zkopírovali z předchozího kroku, přidejte řetězec dotazu `&name=<yourname>` k adrese URL.

![použití funkce kudrlinkou k volání funkce v Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

