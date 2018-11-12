---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262603"
---
## <a name="test"></a>Test funkce

Na počítačích se systémem Mac nebo Linux otestujete nasazenou funkci pomocí cURL nebo v systému Windows pomocí skriptu Bash. Proveďte následující příkaz cURL, který nahradí zástupný symbol `<app_name>` názvem vaší aplikace Function App. Připojte řetězec dotazu `&name=<yourname>` k adrese URL.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Odpověď funkce se zobrazí v prohlížeči.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Pokud nemáte cURL k dispozici v příkazovém řádku, zadejte stejnou adresu URL do pole adresy ve webovém prohlížeči. Znovu nahraďte zástupný symbol `<app_name>` názvem aplikace Function App, připojte řetězec dotazu `&name=<yourname>` k adrese URL a proveďte požadavek.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Odpověď funkce se zobrazí v prohlížeči.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
