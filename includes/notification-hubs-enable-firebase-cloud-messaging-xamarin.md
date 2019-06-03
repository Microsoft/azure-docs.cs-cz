---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4ceff7d59443fe78fb8cf7164e5f31cf1acc189a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420807"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na **přidat Firebase do aplikace pro Android** stránce, proveďte následující kroky: 
    1. Pro **název balíčku pro Android**, zadejte název vašeho balíčku. Například: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Zadejte název balíčku](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Vyberte **Register app**. 
4. Vyberte **stažení souboru google-services.json**, uložte soubor do **aplikace** složky vašeho projektu a pak vyberte **Další**. 

    ![Stažení souboru google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Vyberte **Další** na stránce. 
7. Vyberte **tento krok přeskočit** na stránce. 

    ![Poslední krok přeskočte](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Pokud jste nestáhli **souboru google-services.json** soubor, můžete provést tak dál na této stránce. 
5. Přepněte **Cloud Messaging** kartě v horní části. 
6. Zkopírujte a uložte **klíč serveru starší verze** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci centra oznámení.
