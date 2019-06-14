---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140271"
---
1. V zobrazení řešení (nebo **Průzkumníka řešení** v sadě Visual Studio), klikněte pravým tlačítkem na **součásti** složky, klikněte na tlačítko **získat více komponent...** , vyhledejte **klienta zasílání zpráv cloudu Google** součástí a přidejte ho do projektu.
2. Otevřete soubor projektu ToDoActivity.cs a přidejte následující příkaz pro třídu:

    ```csharp
    using Gcm.Client;
    ```

3. V **ToDoActivity** třídy, přidejte následující kód, nové: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Umožňuje získat přístup k instanci mobilního klienta z procesu obslužné rutiny služby nabízených oznámení.
4. Přidejte následující kód, který **OnCreate** metoda, poté, co **MobileServiceClient** se vytvoří:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Vaše **ToDoActivity** je nyní připravený k přidání nabízených oznámení.
