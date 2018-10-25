---
title: Kurz – Povolení ověřování účtů pomocí Azure Active Directory B2C pro jednostránkovou aplikaci | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů v jednostránkové aplikaci (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 0f2fa2bb8e20ce4cc187fe6f061d2d8c251c4673
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945208"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování účtů pomocí Azure Active Directory B2C pro jednostránkovou aplikaci

V tomto kurzu se dozvíte, jak použít Azure Active Directory (Azure AD) B2C k registraci a přihlašování uživatelů v jednostránkové aplikaci (SPA). Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích, podnikových účtů a účtů Azure Active Directory s využitím protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrovat ukázkovou jednostránkovou aplikaci ve vašem adresáři Azure AD B2C
> * Vytvořit zásady pro registraci a přihlašování uživatelů, úpravy profilu a resetování hesla.
> * Nakonfigurovat ukázkovou aplikaci pro použití vašeho adresáře Azure AD B2C

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Vytvořte si vlastní [adresář Azure AD B2C](active-directory-b2c-get-started.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadou funkcí **Vývoj pro ASP.NET a web**.
* Sada [.NET Core SDK 2.0.0](https://www.microsoft.com/net/core) nebo novější
* Instalovat [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Registrace jednostránkové aplikace

Aby aplikace mohly přijímat [přístupové tokeny](../active-directory/develop/developer-glossary.md#access-token) z Azure Active Directory, musí být [zaregistrované](../active-directory/develop/developer-glossary.md#application-registration) ve vašem adresáři. Registrací se pro aplikaci vytvoří [ID aplikace](../active-directory/develop/developer-glossary.md#application-id-client-id) ve vašem adresáři. 

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho adresáře Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Ze seznamu služeb na webu Azure Portal vyberte **Azure AD B2C**. 

2. V nastavení B2C klikněte na **Aplikace** a pak klikněte na **Přidat**. 

    K registraci ukázkové webové aplikace ve vašem adresáři použijte tato nastavení:
    
    ![Přidání nové aplikace](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | My sample single page app | Zadejte **Název**, který popíše vaši aplikaci pro zákazníky. | 
    | **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano | V případě jednostránkové aplikace vyberte **Ano**. |
    | **Povolit implicitní tok** | Ano | Vyberte **Ano**, protože aplikace používá [Přihlášení OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adresa URL odpovědi** | `http://localhost:6420` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně (localhost) a naslouchá na portu 6420. |
    | **Zahrnout nativního klienta** | Ne | Vzhledem k tomu, že se jedná o jednostránkovou aplikaci, a ne nativního klienta, vyberte Ne. |
    
3. Kliknutím na **Vytvořit** svou aplikaci zaregistrujte.

Zaregistrované aplikace se zobrazí v seznamu aplikací pro příslušný adresář Azure AD B2C. Vyberte ze seznamu vaši jednostránkovou aplikaci. Zobrazí se podokno vlastností zaregistrované jednostránkové aplikace.

![Vlastnosti jednostránkové aplikace](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Poznamenejte si **ID klienta aplikace**. Toto ID jednoznačně identifikuje aplikaci a je potřeba při konfiguraci aplikace později v tomto kurzu.

## <a name="create-policies"></a>Vytvoření zásad

Zásada Azure AD B2C definuje pracovní postupy uživatelů. Běžnými pracovními postupy jsou například registrace, přihlášení, změna hesla a úpravy profilu.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Vytvoření zásady registrace nebo přihlašování

Pro registraci uživatelů, která jim umožní přístup k přihlášení k webové aplikaci, vytvořte **zásadu registrace nebo přihlašování**.

1. Na stránce portálu Azure AD B2C vyberte **Zásady registrace nebo přihlašování** a klikněte na **Přidat**.

    Ke konfiguraci zásady použijte následující nastavení:

    ![Přidání zásady registrace nebo přihlašování](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiUpIn | Zadejte **Název** zásady. K názvu zásady se přidá předpona **B2C_1_**. Úplný název zásady **B2C_1_SiUpIn** použijete ve vzorovém kódu. | 
    | **Zprostředkovatel identity** | E-mailová registrace | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |
    | **Atributy registrace** | Zobrazované jméno a PSČ | Vyberte atributy, které se při registraci shromáždí od uživatele. |
    | **Deklarace identity aplikace** | Zobrazované jméno, PSČ, Uživatel je nový, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token). |

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
    | **Deklarace identity aplikace** | Zobrazované jméno, PSČ, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token) po úspěšné úpravě profilu. |

2. Kliknutím na **Vytvořit** vytvořte zásadu. 

### <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

Pokud chcete ve své aplikaci povolit resetování hesla, musíte vytvořit **zásadu resetování hesla**. Tato zásada popisuje uživatelské prostředí pro resetování hesla a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

1. Na stránce portálu Azure AD B2C vyberte **Zásady resetování hesla** a klikněte na **Přidat**.

    Ke konfiguraci zásady použijte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SSPR | Zadejte **Název** zásady. K názvu zásady se přidá předpona **B2C_1_**. Úplný název zásady **B2C_1_SSPR** použijete ve vzorovém kódu. | 
    | **Zprostředkovatel identity** | Resetování hesla s použitím e-mailové adresy | Toto je zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |
    | **Deklarace identity aplikace** | ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token) po úspěšném resetování hesla. |

2. Kliknutím na **Vytvořit** vytvořte zásadu. 

## <a name="update-single-page-app-code"></a>Aktualizace kódu jednostránkové aplikace

Když teď máte zaregistrovanou aplikaci a vytvořené zásady, musíte svou aplikaci nakonfigurovat tak, aby používala váš adresář Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou jednostránkovou aplikaci v JavaScriptu, kterou si můžete stáhnout z GitHubu. 

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
Ukázková aplikace předvádí, jak může jednostránková aplikace pomocí Azure AD B2C registrovat a přihlašovat uživatele a volat chráněné rozhraní API. Aplikaci musíte změnit tak, aby používala registraci aplikace ve vašem adresáři, a nakonfigurovat zásady, které jste vytvořili. 

Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete soubor `index.html` v ukázce jednostránkové aplikace Node.js.
2. Nakonfigurujte ukázku s použitím informací o registraci adresáře Azure AD B2C. Změňte následující řádky kódu (nezapomeňte hodnoty nahradit názvy vašeho adresáře a rozhraní API):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    Název zásady použitý v tomto kurzu je **B2C_1_SiUpIn**. Pokud používáte jiný název zásady, použijte v hodnotě `authority` vlastní název zásady.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Spusťte následující příkazy:
    ```
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu, na kterém je aplikace hostovaná.
    
    ```
    Listening on port 6420...
    ```

4. V prohlížeči přejděte na adresu `http://localhost:6420` a zobrazte aplikaci.

Ukázková aplikace podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje registraci uživatele k používání aplikace pomocí e-mailové adresy. Další scénáře můžete prozkoumat sami.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Kliknutím na **Login** (Přihlášení) se zaregistrujte jako uživatel jednostránkové aplikace. Tady se používá zásada **B2C_1_SiUpIn**, kterou jste definovali v předchozím kroku.

2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 

3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v zásadě.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v adresáři Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat jednostránkovou aplikaci.

> [!NOTE]
> Po přihlášení aplikace zobrazí chybu Nedostatečná oprávnění. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového adresáře. Vzhledem k tomu, že je váš přístupový token platný jenom pro váš adresář Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svůj adresář vytvoříte chráněné webové rozhraní API. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svůj adresář Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svůj adresář Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit adresář Azure AD B2C, vytvořit zásady a aktualizovat ukázkovou jednostránkovou aplikaci tak, aby používala váš adresář Azure AD B2C. V dalším kurzu zjistíte, jak zaregistrovat, nakonfigurovat a volat chráněné webové rozhraní API z desktopové aplikace.

> [!div class="nextstepaction"]
> [Ukázky kódu pro Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
