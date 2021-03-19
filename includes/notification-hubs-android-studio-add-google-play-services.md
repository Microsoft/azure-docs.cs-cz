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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67509109"
---
1. V Android Studio v nabídce vyberte **nástroje** a pak vyberte **správce SDK**. 
2. Vyberte cílovou verzi Android SDK, která se používá ve vašem projektu. Pak vyberte **Zobrazit podrobnosti balíčku**. 

    ![Android SDK Manager – výběr cílové verze](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Vyberte **Google API**, pokud ještě není nainstalovaná.

    ![Správce Android SDK – vybraná rozhraní API pro Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Přepněte na kartu **SDK Tools** . Pokud jste ještě nenainstalovali Služby Google Play, vyberte **služby Google Play** , jak je znázorněno na následujícím obrázku. Pak vyberte **použít** k instalaci. Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![Android SDK Manager – Služby Google Play vybráno](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Pokud se zobrazí dialogové okno **Potvrdit změnu** , vyberte **OK**. Instalační program součásti nainstaluje požadované součásti. Po instalaci součástí vyberte **Dokončit** .
4. Kliknutím na **tlačítko OK** zavřete dialogové okno **nastavení pro nové projekty** .  
5. Otevřete soubor Build. Gradle v adresáři **aplikace** a přidejte následující řádek do části `dependencies` . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Na panelu nástrojů vyberte ikonu **synchronizovat nyní** .

    ![Synchronizovat s Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otevřete soubor AndroidManifest.xml a přidejte následující značku do značky *aplikace* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
