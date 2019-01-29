---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148060"
---
## <a name="test"></a>Test funkce

Používáme nástroj cURL k otestujete nasazenou funkci na počítači Mac nebo Linux nebo pomocí prostředí Powershell na Windows. Proveďte následující příkaz cURL, který nahradí zástupný symbol `<app_name>` názvem vaší aplikace Function App. Připojte řetězec dotazu `&name=<yourname>` k adrese URL.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Odpověď funkce se zobrazí v prohlížeči.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Pokud nemáte `cURL`nebo `Invoke-WebRequest` k dispozici v příkazovém řádku, zadejte stejnou adresu URL do pole adresy ve webovém prohlížeči. Znovu nahraďte zástupný symbol `<app_name>` názvem aplikace Function App, připojte řetězec dotazu `&name=<yourname>` k adrese URL a proveďte požadavek.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Odpověď funkce se zobrazí v prohlížeči.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
