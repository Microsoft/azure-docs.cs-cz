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
ms.openlocfilehash: e6b949824ec5da60c5e2485be830e61d156a11ff
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55830657"
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

    3. Přidejte následující řádek na konec objektu **úrovni aplikace build.gradle** po části dependenices soubor. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
 
        ![změny konfigurace Build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Vyberte **Další** na stránce. 
7. Vyberte **tento krok přeskočit** na stránce. 

    ![Poslední krok přeskočte](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli **souboru google-services.json** soubor do **aplikace** složky vašeho projektu Android Studio, můžete provést tak dál na této stránce. 
5. Přepněte **Cloud Messaging** kartě v horní části. 
6. Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci centra oznámení.
