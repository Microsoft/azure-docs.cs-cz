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
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140300"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na **přidat Firebase do aplikace pro Android** stránce, proveďte následující kroky: 
    1. Pro **název balíčku pro Android**, zkopírujte hodnotu vaše **applicationId** ve vaší aplikaci **build.gradle** souboru. V tomto příkladu má `com.fabrikam.fcmtutorial1app`. 

        ![Zadejte název balíčku](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Register app**. 
4. Vyberte **stažení souboru google-services.json**, uložte soubor do **aplikace** složky vašeho projektu a pak vyberte **Další**. 

    ![Stažení souboru google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Ujistěte se, následující **změny konfigurace** do projektu v nástroji Android Studio. 
    1.  Ve vaší **na úrovni projektu build.gradle** souboru (&lt;projektu&gt;/build.gradle), přidejte následující příkaz, **závislosti** části. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Ve vaší **build.gradle úrovni aplikace** souboru (&lt;projektu&gt;/&lt;aplikace modulu&gt;/build.gradle), přidejte následující příkaz,  **závislosti** oddílu. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Přidejte následující řádek na konec objektu **úrovni aplikace build.gradle** souboru za část obsahující závislosti. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Vyberte **synchronizovat nyní*** na panelu nástrojů. 
 
        ![změny konfigurace Build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Vyberte **Další** na stránce. 
7. Vyberte **tento krok přeskočit** na stránce. 

    ![Poslední krok přeskočte](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli **souboru google-services.json** soubor do **aplikace** složky vašeho projektu Android Studio, můžete provést tak dál na této stránce. 
5. Přepněte **Cloud Messaging** kartě v horní části. 
6. Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci centra oznámení.
