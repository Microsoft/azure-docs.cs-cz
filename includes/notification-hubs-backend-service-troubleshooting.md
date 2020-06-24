---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081371"
---
#### <a name="no-response-from-the-backend-service"></a>Žádná odpověď ze služby back-endu

Při místním testování zajistěte, aby byla služba back-end spuštěná a používala správný port.

Při testování proti **aplikaci rozhraní API Azure**ověřte, že je služba spuštěná a nasazená a se spustila bez chyby.

Nezapomeňte zkontrolovat, jestli jste základní adresu zadali správně v části **[post](https://www.postman.com/downloads)** nebo v konfiguraci mobilní aplikace při testování přes klienta. Základní adresa by měla být orientační `https://<api_name>.azurewebsites.net/` nebo `https://localhost:5001/` při místním testování.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Nepříjem oznámení na Androidu po spuštění nebo zastavení relace ladění

Ujistěte se, že se znovu zaregistrujete po spuštění nebo zastavení relace ladění. Ladicí program způsobí vygenerování nového tokenu **Firebase** . Je potřeba aktualizovat taky instalaci centra oznámení.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Příjem kódu stavu 401 ze služby back-endu

Ověřte, že jste nastavili hlavičku požadavku **apikey** a tato hodnota odpovídá hodnotě, kterou jste nakonfigurovali pro back-end službu.

Pokud se tato chyba zobrazí při místním testování, zajistěte, aby hodnota klíče, kterou jste definovali v konfiguraci klienta, odpovídala hodnotě **ověřování: ApiKey** User-nastavení, kterou používá [rozhraní API](#create-the-api-app).

Pokud testujete pomocí **aplikace API**, zajistěte, aby hodnota klíče v konfiguračním souboru klienta odpovídala nastavení **ověřování: ApiKey** aplikace, které používáte v [aplikaci API](#create-the-api-app).

> [!NOTE]
> Pokud jste toto nastavení vytvořili nebo změnili po nasazení back-end služby, musíte službu restartovat, aby se projevila.

Pokud se rozhodnete nedokončit [ověřování klientů pomocí klíče rozhraní API](#authenticate-clients-using-an-api-key-optional) , ujistěte se, že jste nepoužili atribut **autorizovat** na třídu **NotificationsController** .

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Příjem kódu stavu 404 ze služby back-endu

Ověřte, zda je metoda koncového bodu a požadavku HTTP správná. Například koncové body by měly být orientační:

- **[PUT]**`https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[Odstranit]**`https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[Post]**`https://<api_name>.azurewebsites.net/api/notifications/requests`

Nebo při testování místně:

- **[PUT]**`https://localhost:5001/api/notifications/installations`
- **[Odstranit]**`https://localhost:5001/api/notifications/installations/<installation_id>`
- **[Post]**`https://localhost:5001/api/notifications/requests`

Při zadávání základní adresy v klientské aplikaci se ujistěte, že končí na `/` . Základní adresa by měla být orientační `https://<api_name>.azurewebsites.net/` nebo `https://localhost:5001/` při místním testování.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Nejde zaregistrovat a zobrazí se chybová zpráva centra oznámení.

Ověřte, zda má testovací zařízení síťové připojení. Pak určete stavový kód odpovědi HTTP nastavením zarážky pro kontrolu hodnoty vlastnosti **StatusCode** v **HttpResponse**.

Projděte si předchozí návrhy řešení potíží, pokud je to možné, na základě stavového kódu.

Nastavte zarážku na řádcích, které vracejí tyto specifické stavové kódy pro příslušné rozhraní API. Pak zkuste zavolat back-end službu při ladění místně.

Ověřte, že služba back-end pracuje podle očekávání prostřednictvím služby **[post](https://www.postman.com/downloads)** pomocí příslušné datové části. Použijte vlastní datovou část vytvořenou klientským kódem pro příslušnou platformu.

Zkontrolujte oddíly konfigurace specifické pro platformu a ujistěte se, že nebyly vynechány žádné kroky. Ověřte, zda jsou vyhodnocovány vhodné hodnoty pro `installation id` a `token` proměnné pro příslušnou platformu.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Nepovedlo se přeložit ID pro chybovou zprávu zařízení.

Zkontrolujte oddíly konfigurace specifické pro platformu a ujistěte se, že nebyly vynechány žádné kroky.
