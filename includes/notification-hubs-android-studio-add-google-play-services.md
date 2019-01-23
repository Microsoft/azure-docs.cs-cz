---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453083"
---
1. Kliknutím na příslušnou ikonu na panelu nástrojů v Android Studiu nebo kliknutím na možnosti **Tools**(Nástroje)  > **Android** > **SDK Manager** v nabídce otevřete Android SDK Manager. Vyhledejte cílovou verzi sady Android SDK, kterou používá váš projekt, otevřete ji kliknutím na **Show Package Details** (Zobrazit podrobnosti balíčku) a vyberte možnost **Google APIs** (Rozhraní API Google), pokud ještě není nainstalovaná.
2. Klikněte na kartu **SDK Tools** (Nástroje SDK). Pokud jste ještě nenainstalovali služby Google Play, klikněte na položku **Google Play Services**, jak vidíte níže. Potom spusťte instalaci kliknutím na **Apply** (Použít). Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Otevřít `build.gradle` soubor v adresáři aplikace.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Přidejte tento řádek pod `dependencies`:

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Klikněte na ikonu **Sync Project with Gradle Files** (Synchronizovat projekt se soubory Gradle) na panelu nástrojů.
6. Otevřete soubor **AndroidManifest.xml** a ke značce *application* přidejte následující značku.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
