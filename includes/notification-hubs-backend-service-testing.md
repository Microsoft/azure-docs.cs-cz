---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146461"
---
### <a name="send-a-test-notification"></a>Odešlete zkušební oznámení

1. Otevře novou kartu v [příspěvku](https://www.postman.com/downloads/).

1. Nastavte požadavek na **post**a zadejte následující adresu:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Pokud se rozhodnete dokončit [ověřování klientů pomocí klíče rozhraní API](#authenticate-clients-using-an-api-key-optional) , nezapomeňte nakonfigurovat hlavičky požadavků tak, aby zahrnovaly hodnotu **apikey** .

   | Klíč                            | Hodnota                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Zvolte pro **tělo**možnost **nezpracované** , pak v seznamu možností formátu zvolte **JSON** a pak zahrňte nějaký zástupný obsah **JSON** :

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Vyberte tlačítko **kód** , které je pod tlačítkem **Uložit** v pravém horním rohu okna. Požadavek by měl vypadat podobně jako v následujícím příkladu, pokud je zobrazený pro **HTML** (v závislosti na tom, jestli jste zahrnuli hlavičku **apikey** ):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Spusťte aplikaci **PushDemo** na jedné nebo obou cílových platformách (**Android** a **iOS**).

    > [!NOTE]
    > Pokud testujete na **Androidu** , ujistěte se, že nespouštíte v **ladění**, nebo když aplikaci nasadíte spuštěním aplikace, vynutí aplikaci zavřít a znovu ji spusťte ze spouštěče.

1. V aplikaci **PushDemo** klepněte na tlačítko **Registrovat** .

1. Zpět v části **[po](https://www.postman.com/downloads)** ukončení zavřete okno **Generovat fragmenty kódu** (Pokud jste to ještě neudělali) a pak klikněte na tlačítko **Odeslat** .

1. Ověřte, že v poli **[post](https://www.postman.com/downloads)** máte odpověď **200 OK** a v aplikaci se zobrazí výstraha, která zobrazuje akci **Přijmout akci**.  

1. Zavřete aplikaci **PushDemo** a potom znovu klikněte na tlačítko **Odeslat** v poli **[post](https://www.postman.com/downloads)**.

1. Ověřte, že se znovu zobrazí odpověď **200 OK** v **[příspěvku](https://www.postman.com/downloads)** . Ověřte, že se v oznamovací oblasti aplikace **PushDemo** zobrazuje oznámení se správnou zprávou.

1. Klepnutím na oznámení potvrďte, že aplikace otevře a zobrazí výstrahu akce **přijmout** akci.

1. V části **[post](https://www.postman.com/downloads)**(odesílatel) upravte předchozí text žádosti o odeslání tichého oznámení určujícího *action_b* místo *action_a* pro hodnotu **Akce** .

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. V případě, že je aplikace stále otevřená, klikněte na tlačítko **Odeslat** v poli **[post](https://www.postman.com/downloads)**.

1. Ověřte, že jste v poli pro **[odeslání](https://www.postman.com/downloads)** odpověděli na **200 OK** a že se výstraha zobrazí v aplikaci ukazující, že **přijatá akce ActionB** namísto akce **Action**.

1. Zavřete aplikaci **PushDemo** a potom znovu klikněte na tlačítko **Odeslat** v poli **[post](https://www.postman.com/downloads)**.

1. Ověřte, že jste v poli pro **[odeslání](https://www.postman.com/downloads)** odpověděli na **200 OK** a že se v oznamovací oblasti nezobrazí tiché oznámení.
