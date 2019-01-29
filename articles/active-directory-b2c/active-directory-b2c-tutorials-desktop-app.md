---
title: Kurz – Povolení ověřování účtů pomocí Azure Active Directory B2C pro desktopovou aplikaci | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů v desktopové aplikaci .NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a99e141a59be654d6d4285be73b0bea60b1e813b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166965"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Kurz: Povolení aplikace klasické pracovní plochy ověřování účtů pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak použít Azure Active Directory (Azure AD) B2C k registraci a přihlašování uživatelů v desktopové aplikaci WPF (Windows Presentation Foundation). Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích, podnikových účtů a účtů Azure Active Directory s využitím protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrovat ukázkovou desktopovou aplikaci ve svém tenantovi Azure AD B2C.
> * Vytvořit toky uživatelů pro uživatele, registrace a přihlašování, úpravy profilu a resetování hesla.
> * Nakonfigurovat ukázkovou aplikaci pro použití vašeho tenanta Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Vytvořte si vlastního [tenanta Azure AD B2C](active-directory-b2c-get-started.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadami funkcí **Vývoj desktopových aplikací .NET** a **Vývoj pro ASP.NET a web**.

## <a name="register-desktop-app"></a>Registrace desktopové aplikace

Aby aplikace mohly přijímat [přístupové tokeny](../active-directory/develop/developer-glossary.md#access-token) z Azure Active Directory, musí být [zaregistrované](../active-directory/develop/developer-glossary.md#application-registration) ve vašem tenantovi. Registrací se pro aplikaci vytvoří [ID aplikace](../active-directory/develop/developer-glossary.md#application-id-client-id) ve vašem tenantovi. 

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
    | **Vlastní identifikátor URI přesměrování** | `com.onmicrosoft.contoso.appname://redirect/path` | Zadejte `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` toky uživatelů pro tento identifikátor URI odesílají tokeny. |
    
3. Kliknutím na **Vytvořit** svou aplikaci zaregistrujte.

Zaregistrované aplikace se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaši desktopovou aplikaci. Zobrazí se podokno vlastností zaregistrované desktopové aplikace.

![Vlastnosti desktopové aplikace](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Poznamenejte si **ID klienta aplikace**. Toto ID jednoznačně identifikuje aplikaci a je potřeba při konfiguraci aplikace později v tomto kurzu.

## <a name="create-user-flows"></a>Vytvořit toky uživatelů

Tok uživatele Azure AD B2C definuje uživatelské prostředí pro úlohu identity. Například registrace, přihlášení, změna hesla a úpravy profilu jsou běžné toky uživatelů.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Vytvořit tok registrace / přihlášení uživatele

Registrovat uživatele pro přístup k přihlášení do desktopové aplikace, vytvořte **tok registrace / přihlášení uživatele**.

1. Na stránce portálu Azure AD B2C vyberte **toky uživatelů** a klikněte na tlačítko **nový tok uživatele**.
2. Na **doporučená** klikněte na tlačítko **podepsat a přihlašování**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení:

    ![Přidání toku registrace nebo přihlašování uživatelů](media/active-directory-b2c-tutorials-desktop-app/add-susi-user-flow.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiUpIn | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **B2C_1_**. Použijte úplné uživatelské jméno, tok **B2C_1_SiUpIn** ve vzorovém kódu. | 
    | **Zprostředkovatelé identit** | E-mailová registrace | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |

3.  V části **atributy uživatele a deklarace identity**, klikněte na tlačítko **zobrazit více** a vyberte následující nastavení:

    ![Přidání uživatele atributech a deklaracích identit](media/active-directory-b2c-tutorials-desktop-app/add-attributes-and-claims.png)

    | Sloupec      | Navrhované hodnoty  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Shromažďovat atribut** | Zobrazované jméno a PSČ | Vyberte atributy, které se při registraci shromáždí od uživatele. |
    | **Vrátí deklarace identity** | Zobrazované jméno, PSČ, Uživatel je nový, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token). |

4. Klikněte na **OK**.

5. Klikněte na tlačítko **vytvořit** vytvoříte požadovaný tok uživatele. 

### <a name="create-a-profile-editing-user-flow"></a>Vytvořit tok uživatele upravování profilu

Povolit uživatelům resetovat jejich informace profilu uživatele na své vlastní, vytvořte **tok uživatele upravování profilu**.

1. Na stránce portálu Azure AD B2C vyberte **tok uživatele** a klikněte na tlačítko **nový tok uživatele**.
2. Na **doporučená** klikněte na tlačítko **upravování profilu**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiPe | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **B2C_1_**. Použijte úplné uživatelské jméno, tok **B2C_1_SiPe** ve vzorovém kódu. | 
    | **Zprostředkovatelé identit** | Registrace místního účtu | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |

3. V části **atributy uživatele**, klikněte na tlačítko **zobrazit více** a vyberte následující nastavení:

    | Sloupec      | Navrhované hodnoty  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Shromažďovat atribut** | Zobrazované jméno a PSČ | Vyberte atributy, které můžou uživatelé při úpravě profilu změnit. |
    | **Vrátí deklarace identity** | Zobrazované jméno, PSČ, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token) po úspěšné úpravě profilu. |

4. Klikněte na **OK**.
5. Klikněte na tlačítko **vytvořit** vytvoříte požadovaný tok uživatele. 

### <a name="create-a-password-reset-user-flow"></a>Vytvořit tok uživatele resetování hesla

Pokud chcete povolit resetování hesla na aplikaci, je potřeba vytvořit **resetování hesel, tok uživatele**. Tento tok uživatele popisuje uživatelské prostředí při resetování hesla a obsah tokenů, které aplikace obdrží při úspěšném dokončení.

1. Na stránce portálu Azure AD B2C vyberte **toky uživatelů** a klikněte na tlačítko **nový tok uživatele**.
2. Na **doporučená** klikněte na tlačítko **resetování hesla**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SSPR | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **B2C_1_**. Použijte úplné uživatelské jméno, tok **B2C_1_SSPR** ve vzorovém kódu. | 
    | **Zprostředkovatelé identit** | Resetování hesla s použitím e-mailové adresy | Toto je zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |

3. V části **deklarace identit aplikace**, klikněte na tlačítko **zobrazit více** a vyberte následující nastavení:

    | Sloupec      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Vrátí deklarace identity** | ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token) po úspěšném resetování hesla. |

4. Klikněte na **OK**.
5. Klikněte na tlačítko **vytvořit** vytvoříte požadovaný tok uživatele. 

## <a name="update-desktop-app-code"></a>Aktualizace kódu desktopové aplikace

Teď máte zaregistrovanou desktopovou aplikaci a vytvořené toky uživatelů, musíte nakonfigurovat aplikaci, aby používala vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou desktopovou aplikaci. 

[Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [projděte si úložiště](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Ukázková desktopová aplikace WPF předvádí, jak může desktopová aplikace pomocí Azure AD B2C registrovat a přihlašovat uživatele a volat chráněné rozhraní API.

Je třeba změnit aplikaci, aby používala registraci aplikace ve vašem tenantovi a nakonfigurovat toky uživatelů, kterou jste vytvořili. 

Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete řešení `active-directory-b2c-wpf` v sadě Visual Studio.

2. V projektu `active-directory-b2c-wpf` otevřete soubor **App.xaml.cs** a proveďte následující aktualizace:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Aktualizace **PolicySignUpSignIn** proměnnou *tok registrace / přihlášení uživatele* názvu, kterou jste vytvořili v předchozím kroku. Nezapomeňte použít předponu *B2C_1_*.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>Spuštění ukázkové desktopové aplikace

Stisknutím klávesy **F5** sestavte a spusťte desktopovou aplikaci. 

Ukázková aplikace podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje registraci uživatele k používání aplikace pomocí e-mailové adresy. Další scénáře můžete prozkoumat sami.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **Sign In** (Přihlásit se) a zaregistrujte se jako uživatel desktopové aplikace. Tady se používá **B2C_1_SiUpIn** tok uživatele, které jste definovali v předchozím kroku.

2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 

3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat desktopovou aplikaci.

> [!NOTE]
> Pokud klikněte na tlačítko **Call API** (Zavolat rozhraní API), zobrazí se chyba Neautorizováno. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového tenanta. Vzhledem k tomu, že je váš přístupový token platný pouze pro vašeho tenanta Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svého tenanta vytvoříte chráněné webové rozhraní API. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit tenanta Azure AD B2C, vytvořit toky uživatelů a aktualizovat ukázkovou desktopovou aplikaci, aby používala vašeho tenanta Azure AD B2C. V dalším kurzu zjistíte, jak zaregistrovat, nakonfigurovat a volat chráněné webové rozhraní API z desktopové aplikace.

> [!div class="nextstepaction"]
> 
