---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175513"
---
1. V zobrazení řešení (nebo **Průzkumníkřešení** v sadě Visual Studio) klikněte pravým tlačítkem myši na složku **Součásti,** klikněte na **příkaz Získat další součásti...**, vyhledejte součást **Klienta služby Google Cloud Messaging** a přidejte ji do projektu.
2. Otevřete soubor projektu ToDoActivity.cs a přidejte do třídy následující příkaz using:

    ```csharp
    using Gcm.Client;
    ```

3. Ve třídě **ToDoActivity** přidejte následující nový kód: 

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

    To umožňuje přístup k instanci mobilního klienta z procesu služby push handler.
4. Po vytvoření **klienta MobileServiceClient** přidejte do metody **OnCreate** následující kód:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Vaše **ToDoActivity** je nyní připraven pro přidání nabízených oznámení.
