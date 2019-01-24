---
title: Kurz – Povolení ověřování účtů pomocí Azure Active Directory B2C pro webovou aplikaci | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů ve webové aplikaci ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 30a94cb5de2d618938f17c4e5733821ac7247785
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851517"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování účtů pomocí Azure Active Directory B2C webové aplikace

V tomto kurzu se dozvíte, jak použít Azure Active Directory (Azure AD) B2C k registraci a přihlašování uživatelů ve webové aplikaci ASP.NET. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích, podnikových účtů a účtů Azure Active Directory s využitím protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrovat ukázkovou webovou aplikaci ASP.NET ve svém tenantovi Azure AD B2C.
> * Vytvořit toky uživatelů pro uživatele, registrace a přihlašování, úpravy profilu a resetování hesla.
> * Nakonfigurovat ukázkovou webovou aplikaci pro použití vašeho tenanta Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Vytvořte si vlastního [tenanta Azure AD B2C](active-directory-b2c-get-started.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadou funkcí **Vývoj pro ASP.NET a web**.

## <a name="register-web-app"></a>Registrace webové aplikace

Aby aplikace mohly přijímat [přístupové tokeny](../active-directory/develop/developer-glossary.md#access-token) z Azure Active Directory, musí být [zaregistrované](../active-directory/develop/developer-glossary.md#application-registration) ve vašem tenantovi. Registrací se pro aplikaci vytvoří [ID aplikace](../active-directory/develop/developer-glossary.md#application-id-client-id) ve vašem tenantovi. 

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**. Teď byste měli používat tenanta, kterého jste vytvořili v předchozím kurzu. 

2. V nastavení B2C klikněte na **Aplikace** a pak klikněte na **Přidat**. 

    K registraci ukázkové webové aplikace ve vašem tenantovi použijte následující nastavení:

    ![Přidání nové aplikace](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | My Sample Web App | Zadejte **Název**, který popíše vaši aplikaci pro zákazníky. | 
    | **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano | Pro webovou aplikaci vyberte **Ano**. |
    | **Povolit implicitní tok** | Ano | Vyberte **Ano**, protože aplikace používá [Přihlášení OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adresa URL odpovědi** | `https://localhost:44316` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně (localhost) a naslouchá na portu 44316. |
    | **Zahrnout nativního klienta** | Ne | Vzhledem k tomu, že se jedná o webovou aplikaci, a ne nativního klienta, vyberte Ne. |
    
3. Kliknutím na **Vytvořit** svou aplikaci zaregistrujte.

Zaregistrované aplikace se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaši webovou aplikaci. Zobrazí se podokno vlastností webové aplikace.

![Vlastnosti webové aplikace](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Poznamenejte si **ID aplikace**. Toto ID jednoznačně identifikuje aplikaci a je potřeba při konfiguraci aplikace později v tomto kurzu.

### <a name="create-a-client-password"></a>Vytvoření hesla klienta

Azure AD B2C pro [klientské aplikace](../active-directory/develop/developer-glossary.md#client-application) používá autorizaci OAuth2. Webové aplikace jsou [důvěrní klienti](../active-directory/develop/developer-glossary.md#web-client) a vyžadují ID klienta nebo ID aplikace a tajný klíč klienta, heslo klienta nebo klíč aplikace.

1. Vyberte stránku Klíče pro zaregistrovanou webovou aplikaci a klikněte na **Vygenerovat klíč**.

2. Kliknutím na **Uložit** zobrazte klíč aplikace.

    ![stránka aplikace pro generování klíčů](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

Klíč se na portálu zobrazí pouze jednou. Je důležité hodnotu klíče zkopírovat a uložit. Tuto hodnotu budete potřebovat ke konfiguraci vaší aplikace. Uložte klíč na bezpečném místě. Klíč veřejně nesdílejte.

## <a name="create-user-flows"></a>Vytvořit toky uživatelů

Tok uživatele Azure AD B2C definuje uživatelské prostředí pro úlohu identity. Například registrace, přihlášení, změna hesla a úpravy profilu jsou běžné toky uživatelů.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Vytvořit tok registrace / přihlášení uživatele

Registrovat uživatele pro přístup k přihlášení do webové aplikace, vytvořte **tok registrace / přihlášení uživatele**.

1. Na stránce portálu Azure AD B2C vyberte **toky uživatelů** a klikněte na tlačítko **nový tok uživatele**.
2. Na **doporučená** klikněte na tlačítko **podepsat a přihlašování**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení:

    ![Přidání toku registrace nebo přihlašování uživatelů](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiUpIn | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **b2c_1_**. Použijte úplné uživatelské jméno, tok **b2c_1_SiUpIn** ve vzorovém kódu. | 
    | **Zprostředkovatelé identit** | E-mailová registrace | Zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |

3. V části **atributy uživatele a deklarace identity**, klikněte na tlačítko **zobrazit více** a vyberte následující nastavení:

    ![Přidání toku registrace nebo přihlašování uživatelů](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

    | Sloupec      | Navrhované hodnoty  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Shromažďovat atribut** | Zobrazované jméno a PSČ | Vyberte atributy, které se při registraci shromáždí od uživatele. |
    | **Vrátí deklarace identity** | Zobrazované jméno, PSČ, Uživatel je nový, ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token). |

4. Klikněte na **OK**.
5. Klikněte na tlačítko **vytvořit** vytvoříte požadovaný tok uživatele. 

### <a name="create-a-profile-editing-user-flow"></a>Vytvořit tok uživatele upravování profilu

Povolit uživatelům resetovat jejich informace profilu uživatele na své vlastní, vytvořte **tok uživatele upravování profilu**.

1. Na stránce portálu Azure AD B2C vyberte **toky uživatelů** a klikněte na tlačítko **nový tok uživatele**.
2. Na **doporučená** klikněte na tlačítko **upravování profilu**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SiPe | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **b2c_1_**. Použijte úplné uživatelské jméno, tok **b2c_1_SiPe** ve vzorovém kódu. | 
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

1. Na stránce portálu Azure AD B2C vyberte **Zásady resetování hesla** a klikněte na **Přidat**.
2. Na **doporučená** klikněte na tlačítko **resetování hesla**.

    Ke konfiguraci vašeho toku uživatele, použijte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | SSPR | Zadejte **název** pro tok uživatele. Název toku uživatele se s předponou **b2c_1_**. Použijte úplné uživatelské jméno, tok **b2c_1_SSPR** ve vzorovém kódu. | 
    | **Zprostředkovatelé identit** | Resetování hesla s použitím e-mailové adresy | Toto je zprostředkovatel identity sloužící k jednoznačné identifikaci uživatele. |

3. V části **deklarace identit aplikace**, klikněte na tlačítko **zobrazit více** a vyberte následující nastavení:
    | Sloupec      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Vrátí deklarace identity** | ID objektu uživatele | Vyberte [deklarace identity](../active-directory/develop/developer-glossary.md#claim), které chcete zahrnout do [přístupového tokenu](../active-directory/develop/developer-glossary.md#access-token) po úspěšném resetování hesla. |

4. Klikněte na **OK**.
5. Klikněte na tlačítko **vytvořit** vytvoříte požadovaný tok uživatele. 

## <a name="update-web-app-code"></a>Aktualizace kódu webové aplikace

Teď máte zaregistrovanou webovou aplikaci a vytvořené toky uživatelů, musíte nakonfigurovat aplikaci, aby používala vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci, kterou si můžete stáhnout z GitHubu. 

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu. Ujistěte se, že cesta ke složce, do které extrahujete ukázkový soubor, obsahuje méně než 260 znaků.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Ukázková webová aplikace ASP.NET je jednoduchá aplikace seznamu úkolů umožňující vytvoření a aktualizaci seznamu úkolů. Aplikace umožňuje uživatelům zaregistrovat se k používání aplikace ve vašem tenantovi Azure AD B2C s využitím [komponent middlewaru Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). Dosáhnete toho vytvořením toku uživatele Azure AD B2C, můžete uživatele použít účet na sociální síti nebo vytvořit účet, který chcete použít jako svou identitu pro přístup k aplikaci. 

Ukázkové řešení obsahuje dva projekty:

**Ukázková webová aplikace (TaskWebApp):** Webové aplikace můžete vytvářet a upravovat seznam úkolu. Tato webová aplikace používá **registrace nebo přihlašování** tok uživatele k registraci nebo přihlašování uživatelů.

**Ukázková aplikace webového rozhraní API (TaskService):** Webové rozhraní API, které podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Toto webové rozhraní API je chráněné službou Azure AD B2C a volané webovou aplikací.

Aplikaci je potřeba změnit tak, aby používala registraci aplikace ve vašem tenantovi, což zahrnuje ID aplikace a klíč, které jste si poznamenali dříve. Musíte také nakonfigurovat toky uživatelů, kterou jste vytvořili. Ukázková webová aplikace definuje hodnoty konfigurace jako nastavení aplikace v souboru Web.config. Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.

2. V projektu webové aplikace **TaskWebApp** otevřete soubor **Web.config**. Nahraďte hodnotu `ida:Tenant` názvem tenanta, kterého jste vytvořili. Nahraďte hodnotu `ida:ClientId` za ID aplikace, které jste si poznamenali. Nahraďte hodnotu `ida:ClientSecret` klíčem, který jste si poznamenali.

3. V souboru **Web.config** nahraďte hodnotu `ida:SignUpSignInPolicyId` za `b2c_1_SiUpIn`. Nahraďte hodnotu `ida:EditProfilePolicyId` za `b2c_1_SiPe`. Nahraďte hodnotu `ida:ResetPasswordPolicyId` za `b2c_1_SSPR`.

## <a name="run-the-sample-web-app"></a>Spuštění ukázkové webové aplikace

V Průzkumníku řešení klikněte pravým tlačítkem na projekt **TaskWebApp** a klikněte na **Nastavit jako spouštěný projekt**.

Stisknutím klávesy **F5** spusťte webovou aplikaci. Spustí se výchozí prohlížeč a otevře se adresa místního webu `https://localhost:44316/`. 

Ukázková aplikace podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje registraci uživatele k používání aplikace pomocí e-mailové adresy. Další scénáře můžete prozkoumat sami.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Kliknutím na odkaz **Sign up / Sign in** (Registrace / Přihlášení) v horním banneru se zaregistrujte jako uživatel webové aplikace. Tady se používá **b2c_1_SiUpIn** tok uživatele, které jste definovali v předchozím kroku.

2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 

3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat webovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit tenanta Azure AD B2C, vytvořit toky uživatelů a aktualizovat ukázkovou webovou aplikaci, aby používala vašeho tenanta Azure AD B2C. V dalším kurzu zjistíte, jak zaregistrovat, nakonfigurovat a volat webové rozhraní API ASP.NET chráněné vaším tenantem Azure AD B2C.

> [!div class="nextstepaction"]
> [Kurz: Použití Azure Active Directory B2C k ochraně webového rozhraní API ASP.NET](active-directory-b2c-tutorials-web-api.md)
