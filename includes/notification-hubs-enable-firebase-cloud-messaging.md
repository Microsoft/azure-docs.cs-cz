---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509079"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na **přidat Firebase do aplikace pro Android** stránce, proveďte následující kroky: 
    1. Pro **název balíčku pro Android**, zkopírujte hodnotu vaše **applicationId** v souboru build.gradle vaší aplikace. V tomto příkladu má `com.fabrikam.fcmtutorial1app`. 

        ![Zadejte název balíčku](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Register app**. 
4. Vyberte **stažení souboru google-services.json**, uložte soubor do **aplikace** složky vašeho projektu a pak vyberte **Další**. 

    ![Stažení souboru google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Ujistěte se, následující **změny konfigurace** do projektu v nástroji Android Studio. 
    1.  V souboru build.gradle na úrovni projektu (&lt;projektu&gt;/build.gradle), přidejte následující příkaz, kterým **závislosti** oddílu. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. V souboru build.gradle úrovni aplikace (&lt;projektu&gt;/&lt;aplikace modulu&gt;/build.gradle), přidejte následující příkaz, kterým **závislosti** části. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Přidejte následující řádek na konec souboru build.gradle úrovni aplikace za část obsahující závislosti. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Vyberte **synchronizovat nyní** na panelu nástrojů. 
 
        ![změny konfigurace Build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Vyberte **Další**. 
7. Vyberte **tento krok přeskočit**. 

    ![Poslední krok přeskočte](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli soubor souboru google-services.json do **aplikace** složky vašeho projektu Android Studio, můžete provést tak dál na této stránce. 
5. Přepněte **Cloud Messaging** kartě v horní části. 
6. Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci rozbočovače.
