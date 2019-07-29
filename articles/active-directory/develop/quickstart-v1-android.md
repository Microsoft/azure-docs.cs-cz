---
title: Přihlášení uživatelů a zavolání rozhraní API pro Microsoft Graph z aplikace pro Android | Microsoft Docs
description: Zjistěte, jak přihlásit uživatele a zavolat rozhraní API pro Microsoft Graph z aplikace pro Android.
services: active-directory
documentationcenter: android
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1ebfce2b96da51b9c33646c1626495d280d7b8a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592248"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlaste se uživatelům a zavolejte rozhraní API pro Microsoft Graph z aplikace pro Android.

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Pokud vyvíjíte aplikaci pro Android, Microsoft vám usnadní práci s přihlášením uživatelů do Azure Active Directory (Azure AD). Azure AD umožňuje vaší aplikaci získat přístup k datům uživatelů prostřednictvím Microsoft Graphu nebo vlastního chráněného webového rozhraní API.

Knihovna Androidu pro Azure AD Authentication Library (ADAL) umožňuje, aby vaše aplikace mohla začít používat [Microsoft Azure Cloud](https://azure.microsoft.com/free/cloud-services/) a [rozhraní API Microsoft Graph](https://developer.microsoft.com/graph) tím, že podporuje [Microsoft Azure Active Directory účty](https://azure.microsoft.com/services/active-directory/) pomocí standardu v oboru. OAuth 2,0 a OpenID Connect.

V tomto rychlém startu se naučíte:

* Získat token pro Microsoft Graph
* Aktualizovat token
* Zavolat Microsoft Graph
* Odhlásit uživatele

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ho ještě nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scénář: Přihlaste se uživatelům a zavolejte Microsoft Graph

![Zobrazuje topologii Azure AD a Androidu.](./media/quickstart-v1-android/active-directory-android-topology.png)

Tuto aplikaci můžete použít pro všechny účty Azure AD. Podporuje jak scénáře s jedním tenantem, tak i scénáře s více tenanty (popsáno v postupech). Ukazuje, jak můžete sestavit aplikace pro připojení podnikových uživatelů a získání přístupu prostřednictvím Microsoft Graphu k jejich datům Azure a O365. Během autorizačního toku se budou muset koncoví uživatelé přihlásit a vyjádřit souhlas s oprávněními aplikace a v některých případech se může v aplikaci vyžadovat i vyjádření souhlasu správce. Většina logiky v tomto příkladu ukazuje, jak ověřit uživatele a provést základní volání Microsoft Graphu.

## <a name="sample-code"></a>Ukázka kódu

Úplný ukázkový kód můžete najít [na GitHubu](https://github.com/Azure-Samples/active-directory-android).

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
    - Vyberte **Nová registrace**.
    - Do pole **Název** zadejte název aplikace.
    - V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
    - V části **identifikátor URI přesměrování**vyberte z rozevíracího seznamu možnost **veřejný klient (mobilní zařízení a plocha)** a zadejte `http://localhost`.
    - Klikněte na **zaregistrovat**.

3. Konfigurace Microsoft Graphu
    - Vyberte **oprávnění k rozhraní API**.
    - Vyberte **Přidat oprávnění**a uvnitř **Vyberte rozhraní API** vyberte ***Microsoft Graph***.
    - V části **delegovaná oprávnění**vyberte oprávnění **uživatel. číst**a pak klikněte na tlačítko **Přidat** k uložení.        
    
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
2. Konstantu `REDIRECT URI` nahraďte identifikátorem `Redirect URI` pro přesměrování, který jste nakonfigurovali dříve (`http://localhost`).

## <a name="step-4-run-the-sample"></a>Krok 4: Spuštění ukázky

1. Vyberte **Build > Clean Project** (Sestavit > Čistý projekt).
2. Vyberte **Run > Run app** (Spustit > Spustit aplikaci).
3. Aplikace by se měla sestavit a zobrazit základní uživatelské prostředí. Když kliknete na tlačítko `Call Graph API`, zobrazí se výzva k přihlášení a potom se pomocí nového tokenu tiše zavolá rozhraní API pro Microsoft Graph.

## <a name="next-steps"></a>Další postup

1. Pokud chcete získat další informace o mechanismech knihovny a způsobu konfigurace nových scénářů a funkcí, projděte si [stránku Wiki knihovny ADAL pro Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki).
2. V nativních scénářích bude aplikace používat vložené webové zobrazení Webview a neopustí aplikaci. Identifikátor URI pro přesměrování může být libovolný.
3. Došlo k problémům nebo máte nějaký požadavek? Můžete vytvořit problém nebo odeslat Stack Overflow se značkou `azure-active-directory`.

### <a name="cross-app-sso"></a>Jednotné přihlašování napříč aplikacemi

Zjistěte, [jak na Androidu pomocí knihovny ADAL povolit jednotné přihlašování napříč aplikacemi](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Telemetrie ověřování

Knihovna ADAL zveřejňuje telemetrii ověřování, aby vývojáři aplikací pochopili, jak se aplikace chovají, a mohli vytvořit lepší prostředí. Díky tomu můžete zachytit úspěšné přihlášení, aktivní uživatele a několik dalších zajímavých přehledů. K použití telemetrie ověřování musí vývojáři aplikací službu telemetrie nastavit, aby události agregovala a ukládala.

Další informace o telemetrii ověřování získáte v tématu o [telemetrii ověřování knihovny ADAL pro Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry).
