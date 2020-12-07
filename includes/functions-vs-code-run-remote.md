---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748243"
---
## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem myši (Windows) nebo <kbd>CTRL-</kbd> Click (MacOS) na **HttpExample** a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Vložte tuto adresu URL pro požadavek HTTP do adresního řádku prohlížeče, přidejte `name` řetězec dotazu `?name=Functions` na konec této adresy URL a potom spusťte požadavek. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    Následující příklad ukazuje odpověď v prohlížeči na požadavek Remote GET vracený funkcí:

    ![Odezva funkce v prohlížeči](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
