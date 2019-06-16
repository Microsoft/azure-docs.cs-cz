---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67132945"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Pokud váš účet umožňuje přístup k více než jednoho tenanta, vyberte účet v pravém horním rohu a pak nastavte portálu relace do tenanta služby Azure AD, kterou chcete použít.
1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
1. V části **identifikátor URI pro přesměrování** části, v rozevíracím seznamu vyberte **webové** platforem a pak nastavte hodnotu na adresu URL aplikace, která je založená na webovém serveru. 

   Informace o nastavení a získání adresy URL pro přesměrování ve Visual Studiu a Node.js najdete v následujících dvou částech.

1. Vyberte **Zaregistrovat**.
1. V aplikaci **přehled** stránce si poznamenejte **ID aplikace (klient)** hodnoty pro pozdější použití.
1. Tento rychlý start vyžaduje [implicitní tok poskytování](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) povolit. V levém podokně zaregistrovanou aplikaci, vyberte **ověřování**.
1. V **upřesňující nastavení**v části **implicitní grant**, vyberte **tokeny typu ID** a **přístupové tokeny** zaškrtávací políčka. Tokeny typu ID a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Nastavení adresy URL pro přesměrování pro Node.js
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale můžete použít další dostupný port. 
>
> Chcete-li nastavit adresu URL pro přesměrování v informace o registraci aplikace, přepněte zpět **registrace aplikace** podokně, a proveďte jednu z následujících akcí:
>
> - Nastavte *`http://localhost:30662/`* jako **přesměrování URL adresy**.
> - Pokud používáte vlastní port TCP, použijte *`http://localhost:<port>/`* (kde  *\<port >* je vlastní číslo portu TCP).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Nastavení adresy URL přesměrování pro aplikaci Visual Studio
> K získání adresy URL pro přesměrování pro aplikaci Visual Studio, postupujte takto:
> 1. V **Průzkumníka řešení**, vyberte projekt.
>
>    **Vlastnosti** otevře se okno. Pokud se neotevře, stiskněte klávesu **F4**.
>
>    ![V okně Vlastnosti projektu JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Kopírovat **URL** hodnotu.
 
> 1. Přepněte zpět **registrace aplikace** podokně a vložte zkopírovaný hodnotu jako **adresy URL pro přesměrování**.

#### <a name="configure-your-javascript-spa"></a>Konfigurace vaší aplikace SPA v JavaScriptu

1. V *index.html* soubor, který jste vytvořili při nastavení projektu, přidejte informace o registraci aplikace. V horní části souboru v rámci `<script></script>` značky, přidejte následující kód:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Kde:
    - *\<Enter_the_Application_Id_here >* je **ID aplikace (klient)** pro aplikace, které jste zaregistrovali.
    - *\<Enter_the_Tenant_info_here >* nastavena na jednu z následujících možností:
       - Pokud vaše aplikace podporuje *účty v tomto adresáři organizace*, nahraďte tuto hodnotu **ID Tenanta** nebo **název Tenanta** (například  *contoso.microsoft.com*).
       - Pokud vaše aplikace podporuje *účty v libovolném adresáři organizace*, nahradí tato hodnota se **organizace**.
       - Pokud vaše aplikace podporuje *účty v jakékoli organizaci adresáři a osobní účty Microsoft*, nahradí tato hodnota se **běžné**. K omezení podpory *Microsoft osobní účty pouze*, nahradí tato hodnota se **příjemci**.
