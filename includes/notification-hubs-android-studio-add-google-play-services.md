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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67509109"
---
1. V Android Studiu vyberte v nabídce **Nástroje** a pak vyberte **Správce sady SDK**. 
2. Vyberte cílovou verzi sady Android SDK, která se používá v projektu. Pak vyberte **Zobrazit podrobnosti balíčku**. 

    ![Android SDK Manager - vyberte cílovou verzi](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Pokud ještě není nainstalovaná , vyberte **vyberte google api**.

    ![Android SDK Manager - Google API vybrána](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Přepněte na kartu **Nástroje sady SDK.** Pokud jste služby Google Play ještě nenainstalovali, vyberte **služby Google Play,** jak je znázorněno na následujícím obrázku. Pak vyberte **Použít** k instalaci. Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![Android SDK Manager - Google Play služby vybrané](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Pokud se zobrazí dialogové okno **Potvrdit změnu,** vyberte **OK**. Instalační služba součásti nainstaluje požadované součásti. Po instalaci součástí vyberte **Dokončit.**
4. Výběrem **možnosti OK** zavřete dialogové okno **Nastavení pro nové projekty.**  
5. Otevřete soubor build.gradle v adresáři **aplikace** a `dependencies`pod položku přidejte následující řádek . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Na panelu nástrojů vyberte **ikonu Synchronizovat.**

    ![Synchronizace s Gradlem](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otevřete soubor AndroidManifest.xml a přidejte do značky *aplikace* následující značku.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
