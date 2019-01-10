---
title: Přihlášení uživatelů a zavolání rozhraní API pro Microsoft Graph z aplikace pro Android | Microsoft Docs
description: Zjistěte, jak přihlásit uživatele a zavolat rozhraní API pro Microsoft Graph z aplikace pro Android.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: d5d284e8f7756e3e9bff5b08d28a8ed911a60572
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158869"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Pokud vyvíjíte aplikaci pro Android, Microsoft vám usnadní práci s přihlášením uživatelů do Azure Active Directory (Azure AD). Azure AD umožňuje vaší aplikaci získat přístup k datům uživatelů prostřednictvím Microsoft Graphu nebo vlastního chráněného webového rozhraní API.

Knihovna pro Android Azure AD Authentication Library (ADAL) dává aplikaci možnost začít používat [cloudu Microsoft Azure](https://cloud.microsoft.com) a [Microsoft Graph API](https://developer.microsoft.com/graph) díky podpoře [Microsoft Azure Účty služby Active Directory](https://azure.microsoft.com/services/active-directory/) pomocí oborový standard OAuth 2.0 a OpenID Connect.

V tomto rychlém startu se naučíte:

* Získat token pro Microsoft Graph
* Aktualizovat token
* Zavolat Microsoft Graph
* Odhlásit uživatele

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ho ještě nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scénář: Přihlašování uživatelů a volání Microsoft Graphu

![Topologie](./media/quickstart-v1-android/active-directory-android-topology.png)

Tuto aplikaci můžete použít pro všechny účty Azure AD. Podporuje jak scénáře s jedním tenantem, tak i scénáře s více tenanty (popsáno v postupech). Ukazuje, jak můžete sestavit aplikace pro připojení podnikových uživatelů a získání přístupu prostřednictvím Microsoft Graphu k jejich datům Azure a O365. Během autorizačního toku se budou muset koncoví uživatelé přihlásit a vyjádřit souhlas s oprávněními aplikace a v některých případech se může v aplikaci vyžadovat i vyjádření souhlasu správce. Většina logiky v tomto příkladu ukazuje, jak ověřit uživatele a provést základní volání Microsoft Graphu.

## <a name="sample-code"></a>Ukázka kódu

Úplnou ukázku kódu lze najít [na Githubu](https://github.com/Azure-Samples/active-directory-android).

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

## <a name="step-1-register-and-configure-your-app"></a>Krok 1: Registrace a konfigurace aplikace

Budete potřebovat nativní klientskou aplikaci zaregistrovanou u Microsoftu prostřednictvím webu [Azure Portal](https://portal.azure.com).

1. Získání zaregistrované aplikace
    - Přejděte na [Azure Portal](https://aad.portal.azure.com).
    - Vyberte ***Azure Active Directory*** > ***Registrace aplikací***.

2. Vytvoření aplikace
    - Vyberte **Registrace nové aplikace**.
    - Do pole **Název** zadejte název aplikace.
    - V části **Typ aplikace** vyberte **Nativní**.
    - U možnosti **Identifikátor URI pro přesměrování** zadejte `http://localhost`.

3. Konfigurace Microsoft Graphu
    - Vyberte **Nastavení > Požadovaná oprávnění**.
    - Vyberte **Přidat** a uvnitř možnosti **Vyberte rozhraní API** vyberte ***Microsoft Graph***.
    - Vyberte oprávnění **Přihlášení a čtení uživatelského profilu** a stisknutím **Vybrat** vše uložte.
        - Toto oprávnění se mapuje do oboru `User.Read`.
    - Volitelné: Uvnitř **požadovaná oprávnění > Windows Azure Active Directory**, odeberte vybrané oprávnění **přihlášení a čtení profilu uživatele**. Stránka se souhlasem uživatele pak nebude uvádět oprávnění dvakrát.

4. Blahopřejeme! Aplikace se úspěšně nakonfigurovala. V další části budete potřebovat:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>Krok 2: Získání ukázkového kódu

1. Naklonujte kód.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Otevřete ukázku v Android Studiu.
    - Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

## <a name="step-3-configure-your-code"></a>Krok 3: Konfigurace kódu

Veškerou konfiguraci tohoto ukázkového kódu najdete v souboru ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***.

1. Konstantu `CLIENT_ID` nahraďte položkou `ApplicationID`.
2. Konstantu `REDIRECT URI` nahraďte identifikátorem URI pro přesměrování, který jste nakonfigurovali dříve (`http://localhost`).

## <a name="step-4-run-the-sample"></a>Krok 4: Spuštění ukázky

1. Vyberte **Build > Clean Project** (Sestavit > Čistý projekt).
2. Vyberte **Run > Run app** (Spustit > Spustit aplikaci).
3. Aplikace by se měla sestavit a zobrazit základní uživatelské prostředí. Když kliknete na tlačítko `Call Graph API`, zobrazí se výzva k přihlášení a potom se pomocí nového tokenu tiše zavolá rozhraní API pro Microsoft Graph.

## <a name="next-steps"></a>Další postup

1. Pokud chcete získat další informace o mechanismech knihovny a způsobu konfigurace nových scénářů a funkcí, projděte si [stránku Wiki knihovny ADAL pro Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki).
2. V nativních scénářích bude aplikace používat vložené webové zobrazení Webview a neopustí aplikaci. Identifikátor URI pro přesměrování může být libovolný.
3. Došlo k problémům nebo máte nějaký požadavek? Můžete vytvořit chybu nebo publikovat na webu Stack Overflow pomocí značky `azure-active-directory`.

### <a name="cross-app-sso"></a>Jednotné přihlašování napříč aplikacemi

Zjistěte, [jak na Androidu pomocí knihovny ADAL povolit jednotné přihlašování napříč aplikacemi](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Telemetrie ověřování

Knihovna ADAL zveřejňuje telemetrii ověřování, aby vývojáři aplikací pochopili, jak se aplikace chovají, a mohli vytvořit lepší prostředí. Díky tomu můžete zachytit úspěšné přihlášení, aktivní uživatele a několik dalších zajímavých přehledů. K použití telemetrie ověřování musí vývojáři aplikací službu telemetrie nastavit, aby události agregovala a ukládala.

Další informace o telemetrii ověřování získáte v tématu o [telemetrii ověřování knihovny ADAL pro Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry).
