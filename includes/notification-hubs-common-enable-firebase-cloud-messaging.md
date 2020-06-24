---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081378"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Vytvoření projektu Firebase a povolení Firebaseého cloudového zasílání zpráv pro Android

1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, který zadává jako **název projektu** **PushDemo** .

    > [!NOTE]
    > Pro vás bude vygenerován jedinečný název. Ve výchozím nastavení se tato hodnota skládá z malých variant zadaného názvu a generovaného čísla odděleného pomlčkou. Tuto možnost můžete změnit, pokud chcete, aby byla stále globálně jedinečná.

1. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android).

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Na stránce **Přidat Firebase na svou aplikaci pro Android** proveďte následující kroky.
    1. Jako **název balíčku pro Android**zadejte název svého balíčku. Příklad: `com.<organization_identifier>.<package_name>`.

        ![Zadejte název balíčku.](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Vyberte **Registrovat aplikaci**.  
    1. Vyberte **stáhnout google-services.jsna**. Pak soubor uložte do místní složky pro pozdější použití a vyberte **Další**.  

        ![Stáhnout google-services.js](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Vyberte **Další**.
    1. Vybrat **pokračovat do konzoly**

        > [!NOTE]
        > Pokud není tlačítko **pokračovat pro konzolu** povolené, proveďte operaci *ověřit instalaci* a pak zvolte **Přeskočit tento krok**.

1. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Pokud jste nestáhli **google-services.jsna** soubor, můžete si ho stáhnout na této stránce.

1. V horní části přepněte na kartu **Cloud Messaging** . Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci centra oznámení.

    ![Kopírovat klíč serveru](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
