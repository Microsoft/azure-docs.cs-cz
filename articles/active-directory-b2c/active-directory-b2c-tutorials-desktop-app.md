---
title: Kurz – Povolení ověřování účtů pomocí Azure Active Directory B2C pro desktopovou aplikaci | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů v desktopové aplikaci .NET.
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: parja
ms.date: 2/28/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 86d8b105828bdb2a83eac24aebf227b9ae7615e2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Kurz – Povolení ověřování účtů pomocí Azure Active Directory B2C pro desktopovou aplikaci

V tomto kurzu se dozvíte, jak použít Azure Active Directory (Azure AD) B2C k registraci a přihlašování uživatelů v desktopové aplikaci WPF (Windows Presentation Foundation). Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích, podnikových účtů a účtů Azure Active Directory s využitím protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrovat ukázkovou desktopovou aplikaci ve svém tenantovi Azure AD B2C.
> * Vytvořit zásady pro registraci a přihlašování uživatelů, úpravy profilu a resetování hesla.
> * Nakonfigurovat ukázkovou aplikaci pro použití vašeho tenanta Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Vytvořte si vlastního [tenanta Azure AD B2C](active-directory-b2c-get-started.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadami funkcí **Vývoj desktopových aplikací .NET** a **Vývoj pro ASP.NET a web**.

## <a name="register-desktop-app"></a>Registrace desktopové aplikace

Aby aplikace mohly přijímat [přístupové tokeny](../active-directory/develop/active-directory-dev-glossary.md#access-token) z Azure Active Directory, musí být [zaregistrované](../active-directory/develop/active-directory-dev-glossary.md#application-registration) ve vašem tenantovi. Registrací se pro aplikaci vytvoří [ID aplikace](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) ve vašem tenantovi. 

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Ze seznamu služeb na webu Azure Portal vyberte **Azure AD B2C**. 

2. V nastavení B2C klikněte na **Aplikace** a pak klikněte na **Přidat**. 

    K registraci ukázkové webové aplikace ve vašem tenantovi použijte následující nastavení:
    
    ![Přidání nové aplikace](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | My Sample WPF App | Zadejte **Název**, který popíše vaši aplikaci pro zákazníky. | 
    | **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ne | V případě desktopové aplikace vyberte **Ne**. |
    | **Zahrnout nativního klienta** | Ano | Vzhledem k tomu, že se jedná o desktopovou aplikaci, považuje se za nativního klienta. |
    | **Identifikátor URI přesměrování** | Výchozí hodnoty | Jedinečný identifikátor, na který Azure AD B2C přesměruje uživatelského agenta v odpovědi OAuth 2.0. |
    | **Vlastní identifikátor URI přesměrování** | `com.onmicrosoft.contoso.appname://redirect/path` | Zadejte `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path`. Zásady na tento identifikátor URI odesílají tokeny. |
    
3. Kliknutím na **Vytvořit** svou aplikaci zaregistrujte.

Zaregistrované aplikace se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaši desktopovou aplikaci. Zobrazí se podokno vlastností zaregistrované desktopové aplikace.

![Vlastnosti desktopové aplikace](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Poznamenejte si **ID klienta aplikace**. Toto ID jednoznačně identifikuje aplikaci a je potřeba při konfiguraci aplikace později v tomto kurzu.

## <a name="create-policies"></a>Vytvoření zásad

Zásada Azure AD B2C definuje pracovní postupy uživatelů. Běžnými pracovními postupy jsou například registrace, přihlášení, změna hesla a úpravy profilu.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Vytvoření zásady registrace nebo přihlašování

Pro registraci uživatelů, která jim umožní přístup k přihlášení k desktopové aplikaci, vytvořte **zásadu registrace nebo přihlašování**.

1. Na stránce portálu Azure AD B2C vyberte **Zásady registrace nebo přihlašování** a klikněte na **Přidat**.

    Ke konfiguraci zásady použijte následující nastavení:

    ![Přidání zásady registrace nebo přihlašování](media/active-directory-b2c-tutorials-desktop-app/add-susi-policy.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiUpIn | Zadejte **Název** zásady. K názvu zásady se přidá předpona **B2C_1_**. Úplný název zásady **B2C_1_SiUpIn** použijete ve vzorovém kódu. | 
    | **Zprostředkovatel identity** | E-mailová registrace | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |
    | **Atributy registrace** | Zobrazované jméno a PSČ | Vyberte atributy, které se při registraci shromáždí od uživatele. |
    | **Deklarace identity aplikace** | Zobrazované jméno, PSČ, Uživatel je nový, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/active-directory-dev-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Kliknutím na **Vytvořit** vytvořte zásadu. 

### <a name="create-a-profile-editing-policy"></a>Vytvoření zásady upravování profilu

Pokud chcete uživatelům umožnit resetovat informace o svém profilu uživatele, vytvořte **zásadu upravování profilu**.

1. Na stránce portálu Azure AD B2C vyberte **Zásady upravování profilu** a klikněte na **Přidat**.

    Ke konfiguraci zásady použijte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiPe | Zadejte **Název** zásady. K názvu zásady se přidá předpona **B2C_1_**. Úplný název zásady **B2C_1_SiPe** použijete ve vzorovém kódu. | 
    | **Zprostředkovatel identity** | Registrace místního účtu | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |
    | **Atributy profilu** | Zobrazované jméno a PSČ | Vyberte atributy, které můžou uživatelé při úpravě profilu změnit. |
    | **Deklarace identity aplikace** | Zobrazované jméno, PSČ, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/active-directory-dev-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/active-directory-dev-glossary.md#access-token) po úspěšné úpravě profilu. |

2. Kliknutím na **Vytvořit** vytvořte zásadu. 

### <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

Pokud chcete ve své aplikaci povolit resetování hesla, musíte vytvořit **zásadu resetování hesla**. Tato zásada popisuje uživatelské prostředí pro resetování hesla a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

1. Na stránce portálu Azure AD B2C vyberte **Zásady resetování hesla** a klikněte na **Přidat**.

    Ke konfiguraci zásady použijte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SSPR | Zadejte **Název** zásady. K názvu zásady se přidá předpona **B2C_1_**. Úplný název zásady **B2C_1_SSPR** použijete ve vzorovém kódu. | 
    | **Zprostředkovatel identity** | Resetování hesla s použitím e-mailové adresy | Toto je zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |
    | **Deklarace identity aplikace** | ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/active-directory-dev-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/active-directory-dev-glossary.md#access-token) po úspěšném resetování hesla. |

2. Kliknutím na **Vytvořit** vytvořte zásadu. 

## <a name="update-desktop-app-code"></a>Aktualizace kódu desktopové aplikace

Když teď máte zaregistrovanou desktopovou aplikaci a vytvořené zásady, musíte svou aplikaci nakonfigurovat tak, aby používala vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou desktopovou aplikaci. 

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Ukázková desktopová aplikace WPF předvádí, jak může desktopová aplikace pomocí Azure AD B2C registrovat a přihlašovat uživatele a volat chráněné rozhraní API.

Aplikaci musíte změnit tak, aby používala registraci aplikace ve vašem tenantovi, a nakonfigurovat zásady, které jste vytvořili. 

Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete řešení `active-directory-b2c-wpf` v sadě Visual Studio.

2. V projektu `active-directory-b2c-wpf` otevřete soubor **App.xaml.cs** a proveďte následující aktualizace:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Aktualizujte proměnnou **PolicySignUpSignIn** na název *zásady registrace nebo přihlašování*, kterou jste vytvořili v předchozím kroku. Nezapomeňte použít předponu *B2C_1_*.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>Spuštění ukázkové desktopové aplikace

Stisknutím klávesy **F5** sestavte a spusťte desktopovou aplikaci. 

Ukázková aplikace podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje registraci uživatele k používání aplikace pomocí e-mailové adresy. Další scénáře můžete prozkoumat sami.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **Sign In** (Přihlásit se) a zaregistrujte se jako uživatel desktopové aplikace. Tady se používá zásada **B2C_1_SiUpIn**, kterou jste definovali v předchozím kroku.

2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 

3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v zásadě.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat desktopovou aplikaci.

> [!NOTE]
> Pokud klikněte na tlačítko **Call API** (Zavolat rozhraní API), zobrazí se chyba Neautorizováno. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového tenanta. Vzhledem k tomu, že je váš přístupový token platný pouze pro vašeho tenanta Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svého tenanta vytvoříte chráněné webové rozhraní API. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit tenanta Azure AD B2C, vytvořit zásady a aktualizovat ukázkovou desktopovou aplikaci tak, aby používala vašeho tenanta Azure AD B2C. V dalším kurzu zjistíte, jak zaregistrovat, nakonfigurovat a volat chráněné webové rozhraní API z desktopové aplikace.

> [!div class="nextstepaction"]
> 