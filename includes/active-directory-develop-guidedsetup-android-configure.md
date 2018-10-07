---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 6e20ce083c415bced22231835cc616ede8f0dd04
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842928"
---
## <a name="register-your-application"></a>Registrace vaší aplikace
Můžete zaregistrovat aplikaci v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="option-1-express"></a>Možnost 1: Express
1. Přejděte na portál [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  V **název_aplikace**, zadejte název pro vaši aplikaci.

3. Ujistěte se, že **instalační program s asistencí** zaškrtávací políčko je vybrané a pak vyberte **vytvořit**.

4. Postupujte podle pokynů pro získání ID aplikace a vložte ho do svého kódu.

### <a name="option-2-advanced"></a>Možnost 2: Upřesnit 
1. Přejděte na portál [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app).
2. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace. 

3. Zkontrolujte, že není zaškrtnuté políčko **Guided Setup** (Instalační program s asistencí) a vyberte **Create** (Vytvořit).

4. Vyberte **Add Platform** (Přidat platformu), **Native Application** (Nativní aplikace) a pak **Save** (Uložit).

5. V části **aplikace** > **java** > **{hostitele}. { obor názvů}**, otevřete `MainActivity`. 

6.  Nahraďte *[Zadejte sem aplikace Id]* s vaší aplikací / ID klienta:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. V části **aplikace** > **manifesty**, otevřete *AndroidManifest.xml* souboru.

8. V `manifest\application`, přidejte následující aktivitu. `BrowserTabActivity` Aktivitu, která umožňuje společnosti Microsoft pro zpětné volání do aplikace po dokončení ověřování:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. V `BrowserTabActivity`, nahraďte `[Enter the application Id here]` s aplikací / ID klienta.
