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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70935087"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na stránce **Přidat Firebase do aplikace pro Android postupujte** takto: 
    1. Pro **název balíčku Android**zkopírujte hodnotu **applicationId** v souboru build.gradle vaší aplikace. V tomto příkladu `com.fabrikam.fcmtutorial1app`je . 

        ![Zadejte název balíčku](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Registrovat aplikaci**. 
4. Vyberte **Stáhnout soubor google-services.json**, uložte soubor do složky **aplikace** projektu a pak vyberte **Další**. 

    ![Stáhnout google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Proveďte následující **změny konfigurace** projektu v Android Studio. 
    1.  V souboru build.gradle na&lt;&gt;úrovni projektu ( project /build.gradle) přidejte do oddílu **závislostí** následující příkaz. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. V souboru build.gradle na&lt;&gt;/&lt;úrovni aplikace&gt;(project app-module /build.gradle) přidejte do oddílu závislostí následující **příkazy.** 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Přidejte následující řádek na konec souboru build.gradle na úrovni aplikace za oddíl závislosti. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Na panelu nástrojů **vyberte Synchronizovat.** 
 
        ![build.gradle změny konfigurace](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Vyberte **další**. 
7. Vyberte **Přeskočit tento krok**. 

    ![Přeskočit poslední krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli soubor google-services.json do složky **aplikace** projektu Android Studio, můžete tak učinit na této stránce. 
5. Přepněte na kartu **Cloud Messaging** v horní části. 
6. Zkopírujte a uložte **klíč server** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci rozbočovače.
