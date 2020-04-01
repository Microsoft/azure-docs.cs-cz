---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728826"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na stránce **Přidat Firebase do aplikace pro Android postupujte** takto: 
    1. Pro **název balíčku Android**zadejte název balíčku. Například: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Zadejte název balíčku](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Registrovat aplikaci**.  
    1. Vyberte **stáhnout google-services.json**. Potom soubor uložte do složky projektu a vyberte **další**. Pokud jste ještě nevytvořili projekt sady Visual Studio, můžete tento krok provést po vytvoření projektu. 

        ![Stáhnout google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Vyberte **další**. 
    7. Vyberte **Přeskočit tento krok**. 

        ![Přeskočit poslední krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste soubor **google-services.json** nestáhli, můžete si jej stáhnout na této stránce. 

    ![Stažení souboru google-services.json z karty Obecné](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Přepněte na kartu **Cloud Messaging** v horní části. Zkopírujte a uložte **klíč server** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci centra oznámení.

    ![Kopírovat klíč serveru](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
