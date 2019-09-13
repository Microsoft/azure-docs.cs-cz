---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935087"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na stránce **Přidat Firebase na svou aplikaci pro Android** proveďte následující kroky: 
    1. V případě **názvu balíčku Android**Zkopírujte hodnotu vaší hodnoty **ApplicationId** do souboru Build. Gradle vaší aplikace. V tomto příkladu `com.fabrikam.fcmtutorial1app`je to. 

        ![Zadejte název balíčku.](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Registrovat aplikaci**. 
4. Vyberte **Stáhnout Google-Services. JSON**, uložte soubor do složky **aplikace** vašeho projektu a pak vyberte **Další**. 

    ![Stáhnout Google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Proveďte následující **změny konfigurace** projektu v Android Studio. 
    1.  V souboru Build. Gradle sestavení na úrovni projektu (&lt;/Build.Gradle&gt;projektu) přidejte následující příkaz do oddílu **závislosti** . 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. V souboru&lt;Build. Gradle na úrovni aplikace (projekt/&lt;&gt;App-Module&gt;/Build.Gradle) přidejte následující příkazy do oddílu **závislosti** . 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Přidejte následující řádek na konec souboru Build. Gradle na úrovni aplikace za oddíl závislosti. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Vyberte **synchronizovat hned** na panelu nástrojů. 
 
        ![sestavování změn konfigurace sestavení. Gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Vyberte **Další**. 
7. Vyberte **Přeskočit tento krok**. 

    ![Přeskočit poslední krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli soubor Google-Services. JSON do složky **aplikace** Android Studio projektu, můžete to udělat na této stránce. 
5. V horní části přepněte na kartu **Cloud Messaging** . 
6. Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci svého rozbočovače.
