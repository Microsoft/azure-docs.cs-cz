---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814692"
---
1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). Postupujte podle zobrazených pokynů. Stáhněte si soubor **google-services.json**. 

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. V nastavení projektu vyberte kartu **General** (Obecné). Potom si stáhněte soubor **google-services.json**, který obsahuje klíč rozhraní Server API a ID klienta.
5. Klikněte na kartu **Cloud Messaging** v nastavení projektu a potom zkopírujte hodnotu **Legacy server key** (Klíč serveru starší verze). Tuto hodnotu použijete ke konfiguraci zásad přístupu centra oznámení.
