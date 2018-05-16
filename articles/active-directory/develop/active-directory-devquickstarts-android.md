---
title: Začínáme se službou Azure AD Android | Microsoft Docs
description: Jak vytvářet aplikace platformy Android, který se integruje s Azure AD pro přihlášení a volání služby Azure AD chráněný pomocí OAuth2.0 rozhraní API.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-android-getting-started"></a>Začínáme se službou Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Pokud vyvíjíte aplikace pro Android, Microsoft je jednoduchá a přímočará pro přihlášení uživatelů Azure Active Directory (Azure AD). Azure AD umožňuje aplikaci přístup k datům uživatele prostřednictvím Microsoft Graph nebo vlastní chráněné webové rozhraní API. 

Knihovna pro Android Azure AD Authentication Library (ADAL) umožňuje aplikaci začít používat [Microsoft Azure Cloud](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) díky podpoře [ Účty Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pomocí oborový standard OAuth2 a OpenID Connect. Tento příklad znázorňuje normální životních, které by neměli mít aplikace, včetně:

* Získání tokenu pro Microsoft Graph
* Aktualizovat token
* Volání Microsoft Graph
* Odhlásit uživatele

Abyste mohli začít, musíte klienta služby Azure AD, kde můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě nemáte klienta, [zjistěte, jak získat](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scénář: Přihlásit uživatele a volání Microsoft Graph

![Topologie](./media/active-directory-android-topology.png)

Tuto aplikaci můžete použít pro všechny účty Azure AD. Podporuje scénáře jednu i více organizace (popsané v krocích). Ukazuje, jak vývojáři mohou vytvářet aplikace pro připojení s uživateli enterprise a přístup k jejich Azure + O365 dat přes Microsoft Graph. Během tok ověřování koncoví uživatelé, bude nutné se přihlásit a souhlas s oprávnění aplikace a v některých případech může vyžadovat správce, aby souhlas k aplikaci. Většina logiku v této ukázce ukazuje, jak pro ověřování koncového uživatele a zkontrolujte základní volat pro Microsoft Graph.

## <a name="example-code"></a>Příklad kódu

Kód úplnou ukázku najdete [na Githubu](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Postup spuštění

### <a name="register-and-configure-your-app"></a>Registrace a konfigurace aplikace 
Budete muset mít nativní klientskou aplikaci zaregistrovat pomocí Microsoft [portál Azure](https://portal.azure.com). 

1. Získávání registrace aplikace
    - Přejděte na [Azure Portal](https://aad.portal.azure.com). 
    - Vyberte ***Azure Active Directory*** > ***registrace aplikace***. 

2. Vytvoření aplikace
    - Vyberte **Registrace nové aplikace**. 
    - Zadejte název aplikace v **název** pole. 
    - V **typ aplikace** vyberte **nativní**. 
    - V **identifikátor URI pro přesměrování**, zadejte `http://localhost`. 

3. Konfigurace Microsoft Graph
    - Vyberte **Nastavení > požadovaná oprávnění**.
    - Vyberte **přidat**uvnitř **vybrat rozhraní API** vyberte ***Microsoft Graph***. 
    - Vyberte oprávnění **přihlášení a čtení profilu uživatele**, poté stiskněte tlačítko **vyberte** uložit. 
        - Toto oprávnění se mapuje `User.Read` oboru. 
    - Volitelné: Uvnitř **požadovaná oprávnění > Windows Azure Active Directory**, odeberte vybrané oprávnění **přihlášení a čtení profilu uživatele**. Tím se vyhnete stránku souhlasu uživatele, který se seznamem oprávnění dvakrát. 

4. Blahopřejeme! Aplikace je úspěšně nakonfigurován. V další části budete potřebovat:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Získání ukázkového kódu

1. Klonování kód.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Otevřete ukázku v Android Studio.
    - Vyberte **otevřít existující projekt Android Studio**.

### <a name="configure-your-code"></a>Konfigurace kódu

Můžete najít všechny konfigurace pro tuto ukázku kódu v ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** souboru. 

1. Nahraďte konstanta `CLIENT_ID` s `ApplicationID`.
2. Nahraďte konstanta `REDIRECT URI` s `Redirect URI` jste nakonfigurovali dříve (`http://localhost`). 

### <a name="run-the-sample"></a>Spuštění ukázky

1. Vyberte **sestavení > Vyčistit projektu**. 
2. Vyberte **spustit > spuštění aplikace**. 
3. Aplikace by měla sestavení a zobrazit některé základní UX Když kliknete `Call Graph API` tlačítko bude vyzvat přihlášení a bezobslužně volání rozhraní Graph API Microsoft s nový token. 

## <a name="important-info"></a>Důležité informace

1. Najdete v článku věnovaném [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) Další informace o mechanismů knihovny a postup konfigurace nové scénáře a možnosti. 
2. V nativní scénáře aplikace bude používat vložené webové zobrazení a nebude nechte aplikaci. `Redirect URI` Může být libovolný. 
3. Vyhledat potíže nebo máte požadavky? Můžete vytvořit problém nebo můžete zveřejnit na Stackoverflow ke značce `azure-active-directory`. 

### <a name="cross-app-sso"></a>Jednotné přihlašování napříč aplikacemi
Další informace [postup povolení jednotného přihlašování napříč aplikacemi v systému Android pomocí ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Telemetrii ověřování
Knihovna ADAL zpřístupní auth telemetrie pomáhající vývojáři aplikací pochopit, jak se chovají své aplikace a lepšího prostředí pro sestavení. To umožňuje zaznamenat přihlášení úspěšné, aktivních uživatelů a několik dalších zajímavých statistiky. Použití telemetrii ověřování vyžadovat vývojáři aplikace k vytvoření služby telemetrie k agregaci a uložení událostí.

Další informace o ověřování telemetrie, najdete v článku věnovaném [ADAL Android telemetrii ověřování](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
