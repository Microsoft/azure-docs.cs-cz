---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 2e23a15a6bff81f0b48b703e516de8a1a1820972
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293701"
---
## <a name="set-up-your-project"></a>Nastavení projektu

Chcete místo toho stáhněte si tento ukázkový projekt Android Studio? [Stáhnete projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)a pokračujte [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před spuštěním.

### <a name="create-a-new-project"></a>Vytvoření nového projektu 
1.  Otevřete Android Studio a pak vyberte **souboru** > **nový** > **nový projekt**.
2.  Pojmenujte svoji aplikaci a pak vyberte **Další**.
3.  Vyberte **API 21 nebo novější (Android 5.0)** a pak vyberte **Další**.
4.  Ponechte **prázdná aktivita** jak vypadá, vyberte **Další**a pak vyberte **Dokončit**.


### <a name="add-msal-to-your-project"></a>Do projektu přidejte MSAL
1.  V nástroji Android Studio vyberte **skriptů Gradle** > **build.gradle (modul: aplikace)**.
2.  V části **závislosti**, vložte následující kód:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>O tomto balíčku

Knihovna Microsoft Authentication Library se nainstaluje balíček v předchozím kódu. Knihovna MSAL zpracovává všechny token operace, včetně získání, ukládání do mezipaměti, aktualizace a odstranění.  Tokeny jsou potřeba pro přístup k rozhraní API služby chráněný platformou identit společnosti Microsoft.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Vytvoření uživatelského rozhraní aplikace

1. Přejděte na **res** > **rozložení**a pak otevřete **activity_main.xml**. 
2. Změna rozložení aktivitu z `android.support.constraint.ConstraintLayout` nebo jiné `LinearLayout`.
3. Přidat `android:orientation="vertical"` vlastnost `LinearLayout` uzlu.
4. Vložte následující kód do `LinearLayout` uzlu, nahraďte aktuálním obsahu:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
