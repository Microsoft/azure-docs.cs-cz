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
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 45e8668ce0a7eb2edd79271096f58b56ca1af5f0
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205565"
---
## <a name="set-up-your-project"></a>Nastavení projektu

Opravdu chcete stáhnout tento ukázkový projekt Android Studio místo? [Stažení projektu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)a pokračujte [krok konfigurace](#register-your-application) ke konfiguraci ukázka kódu, než ho provést.

### <a name="create-a-new-project"></a>Vytvoření nového projektu 
1.  Otevřete Android Studio a pak vyberte **soubor** > **nový** > **nový projekt**.
2.  Název aplikace a potom vyberte **Další**.
3.  Vyberte **21 rozhraní API nebo novější (Android 5.0)** a potom vyberte **Další**.
4.  Nechte **prázdná aktivita** je, vyberte **Další**a potom vyberte **Dokončit**.


### <a name="add-msal-to-your-project"></a>Do projektu přidejte MSAL
1.  V nástroji Android Studio vyberte **Gradle skripty** > **build.gradle (modul: aplikace)**.
2.  V části **závislosti**, vložte následující kód:

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>O tomto balíčku

Balíček v předchozí kód nainstaluje knihovny pro ověřování Microsoft. MSAL zpracovává získávání, ukládání do mezipaměti a aktualizaci uživatele tokeny, které se používají pro přístup k rozhraní API, které jsou chráněné službou Azure Active Directory koncový bod v2.
<!--end-collapse-->

## <a name="create-the-application-ui"></a>Vytvoření aplikace uživatelského rozhraní

1. Přejděte na **res** > **rozložení**a pak otevřete **activity_main.xml**. 
2. Změna rozložení aktivity z `android.support.constraint.ConstraintLayout` či jiné na `LinearLayout`.
3. Přidat `android:orientation="vertical"` vlastnost, která má `LinearLayout` uzlu.
4. Vložte následující kód do `LinearLayout` uzlu, nahraďte aktuální obsahu:

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
