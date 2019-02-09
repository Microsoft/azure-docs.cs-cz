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
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965983"
---
1. V **Android Studio**vyberte **nástroje** v nabídce a vyberte **správce sady SDK**. 
2. Vyberte cílovou verzi sady SDK pro Android, který se používá v projektu a vyberte **zobrazit podrobnosti balíčku**. 

    ![Správce sady Android SDK – vyberte cílovou verzi](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Vyberte **rozhraní Google API**, pokud ještě není nainstalovaná.

    ![Správce sady Android SDK – vybrané rozhraní API Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Přepněte **SDK Tools** kartu. Pokud jste ještě nenainstalovali služby Google Play, vyberte **služby Google Play** jak je znázorněno na následujícím obrázku. Potom spusťte instalaci kliknutím na **Apply** (Použít). Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![Správce sady Android SDK - služby Google Play vybrané](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Pokud se zobrazí **Potvrdit změnu** dialogu **OK**. Součást instalační program nainstaluje požadované součásti. Vyberte **Dokončit** po instalaci komponenty.
4. Vyberte **OK** zavřete **nastavení pro nové projekty** dialogové okno.  
5. Otevřít `build.gradle` soubor **aplikace** adresáře a přidejte tento řádek pod `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Vyberte **synchronizovat nyní** ikonu na panelu nástrojů.

    ![Synchronizaci s Gradlem](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otevřete soubor **AndroidManifest.xml** a ke značce *application* přidejte následující značku.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
