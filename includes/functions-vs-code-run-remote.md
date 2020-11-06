---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425003"
---
## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce** , klikněte pravým tlačítkem myši (Windows) nebo <kbd>CTRL-</kbd> Click (MacOS) na **HttpExample** a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Vložte tuto adresu URL pro požadavek HTTP do adresního řádku prohlížeče, přidejte `name` řetězec dotazu `?name=Functions` na konec této adresy URL a potom spusťte požadavek. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    Následující příklad ukazuje odpověď v prohlížeči na požadavek Remote GET vracený funkcí:

    ![Odezva funkce v prohlížeči](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
