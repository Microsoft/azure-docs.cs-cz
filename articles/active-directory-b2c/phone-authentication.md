---
title: Registrace a přihlášení k telefonnímu programu s vlastními zásadami
titleSuffix: Azure AD B2C
description: Odesílat jednorázová hesla (JEDNORÁZOVé heslo) v textových zprávách telefonům uživatelů vaší aplikace s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660263"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Nastavte si registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C

Registrace a přihlášení k telefonnímu programu v Azure Active Directory B2C (Azure AD B2C) umožňuje vašim uživatelům se zaregistrovat a přihlásit k vašim aplikacím pomocí JEDNORÁZOVého hesla, které se v textové zprávě pošle na telefon. Jednorázová hesla můžou přispět k minimalizaci rizika vašich uživatelů forgetting nebo k ohrožení zabezpečení vašich hesel.

Postupujte podle kroků v tomto článku a použijte vlastní zásady, které zákazníkům umožňují registraci a přihlášení k vašim aplikacím pomocí jednorázového hesla odeslaného na telefon.

## <a name="pricing"></a>Ceny

Jednorázová hesla se uživatelům odesílají pomocí textových zpráv SMS a můžete se vám účtovat každou odeslanou zprávu. Informace o cenách naleznete v části **samostatné poplatky** [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Uživatelské prostředí pro registraci a přihlašování telefonem

Když se přihlásíte a přihlásíte telefon, uživatel se může přihlásit k aplikaci pomocí telefonního čísla jako jeho primárního identifikátoru. Činnost koncového uživatele při registraci a přihlašování jsou popsána níže.

> [!NOTE]
> Důrazně doporučujeme, abyste zahrnuli informace o souhlasu do svého přihlašovacího a přihlašovacího prostředí, které je podobné následujícímu ukázkovému textu. Tento ukázkový text slouží pouze k informativním účelům. V tématu Stručná příručka k monitorování kódu na [webu CTIA](https://www.ctia.org/programs) a Projděte si své vlastní specialisty nebo odborníky na dodržování předpisů, které vám poradí s vaším konečným textem a konfigurací funkcí, aby splnily Vaše požadavky na dodržování předpisů:
>
> *Když zadáte telefonní číslo, vyjadřujete tím souhlas s přijetím jednorázového hesla odesílaného pomocí textové zprávy, které vám pomůžou přihlásit se a *&lt; Vložit: &gt; název vaší aplikace*. Mohou platit standardní zprávy a sazby dat.*
>
> *&lt;Vložit: odkaz na vaše prohlášení o zásadách ochrany osobních údajů&gt;*<br/>*&lt;vložení: odkaz na vaše podmínek služby&gt;*

Chcete-li přidat vlastní informace o souhlasu, upravte následující ukázku. Zahrňte ho do části `LocalizedResources` pro ContentDefinition, kterou používá samoobslužná stránka, s ovládacím prvkem zobrazení (soubor *Phone_Email_Base.xml* v [registračním programu a v úvodní][starter-pack-phone]sadě pro přihlášení k telefonu):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Přihlašovací možnosti pro telefon

Pokud uživatel ještě nemá účet pro vaši aplikaci, může ho vytvořit kliknutím na odkaz **zaregistrovat** se. Zobrazí se stránka pro registraci, kde uživatel vybere svou **zemi**, zadá jejich telefonní číslo a vybere **Odeslat kód**.

![Uživatel spustí registraci telefonu.](media/phone-authentication/phone-signup-start.png)

Do telefonního čísla uživatele se pošle jednorázový ověřovací kód. Uživatel zadá **ověřovací kód** na stránce pro registraci a pak vybere příkaz **ověřit kód**. (Pokud uživatel nedokázal načíst kód, může vybrat **Odeslat nový kód**.)

![Uživatel ověřuje kód při registraci telefonu.](media/phone-authentication/phone-signup-verify-code.png)

Uživatel zadá další informace požadované na stránce pro registraci. Například **zobrazované jméno**, **křestní jméno** a **příjmení** (země a telefonní číslo zůstávají naplněny). Pokud chce uživatel použít jiné telefonní číslo, může zvolit možnost **změnit číslo** a restartovat registraci. Po dokončení vybere uživatel **pokračovat**.

![Uživatel poskytuje další informace](media/phone-authentication/phone-signup-additional-info.png)

V dalším kroku se uživateli zobrazí výzva k zadání e-mailu pro obnovení. Uživatel zadá svou e-mailovou adresu a pak vybere **Odeslat ověřovací kód**. Do e-mailové schránky uživatele se pošle kód, který se může načíst a zadat do pole **ověřovací kód** . Pak uživatel vybere **ověřit kód**. 

Po ověření kódu uživatel vybere **vytvořit** a vytvoří svůj účet. Nebo pokud chce uživatel použít jinou e-mailovou adresu, můžou zvolit **změnit e-mail**.

![Uživatel vytvoří účet](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Přihlašovací prostředí pro telefonování

Pokud má uživatel existující účet s telefonním číslem jako svůj identifikátor, uživatel zadá své telefonní číslo a vybere **pokračovat**. Po výběru možnosti **pokračovat** potvrdí zemi a telefonní číslo a na telefonu se pošle jednorázový ověřovací kód. Uživatel zadá ověřovací kód a vybere **pokračovat** pro přihlášení.

![Uživatelské prostředí přihlašování k telefonu](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Odstranění uživatelského účtu

V některých případech může být nutné odstranit uživatele a přidružená data z adresáře Azure AD B2C. Podrobnosti o tom, jak odstranit uživatelský účet prostřednictvím Azure Portal, najdete v [těchto pokynech](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Předpoklady

Před nastavením jednorázového hesla budete potřebovat následující prostředky.

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Webová aplikace zaregistrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Vlastní zásady](custom-policy-get-started.md) odeslané do vašeho tenanta

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Získání registračního a registračního balíčku & přihlášení k telefonu

Začněte tím, že aktualizujete soubory vlastních zásad registrace a přihlášení telefon pro práci s vaším klientem Azure AD B2C.

1. V místním klonu úložiště počátečního balíčku Najděte [vlastní soubory zásad pro registraci a přihlašování telefonem][starter-pack-phone] nebo si je Stáhněte přímo. Soubory zásad XML jsou umístěné v následujícím adresáři:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .

1. Dokončete kroky v části [Přidání ID aplikací do vlastní zásady](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [v tématu Začínáme s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md). V tomto případě aktualizujte `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **ID aplikace (klienta)** dvou aplikací, které jste zaregistrovali při dokončování požadavků, *IdentityExperienceFramework* a *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Nahrání souborů zásad

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do svého tenanta Azure AD B2C.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**.
1. Soubory zásad nahrajte v následujícím pořadí:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Při nahrávání každého souboru Azure přidá předponu `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **vlastní zásady** vyberte **B2C_1A_SignUpOrSignInWithPhone**.
1. V části **Vybrat aplikaci** vyberte aplikaci *WebApp1* , kterou jste zaregistrovali při dokončování požadavků.
1. V **možnosti vybrat adresu URL odpovědi** zvolte `https://jwt.ms` .
1. Vyberte **Spustit nyní** a zaregistrujte se pomocí e-mailové adresy nebo telefonního čísla.
1. Vyberte znovu **Spustit** znovu a přihlaste se pomocí stejného účtu, abyste měli jistotu, že máte správnou konfiguraci.

## <a name="get-user-account-by-phone-number"></a>Získat uživatelský účet podle telefonního čísla

Uživatel, který se zaregistruje pomocí telefonního čísla, se ve vašem Azure AD B2C adresáři zaznamená jeho telefonní číslo jako přihlašovací jméno, a to bez e-mailové adresy pro obnovení. Chcete-li změnit telefonní číslo, vaše oddělení technické podpory nebo tým podpory musí nejprve najít svůj účet a následně aktualizovat jejich telefonní číslo.

Pomocí [Microsoft Graph](microsoft-graph-operations.md)můžete najít uživatele podle jejich telefonního čísla (přihlašovací jméno):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Například:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Další kroky

V GitHubu můžete najít registrační sadu vlastních zásad pro registraci a přihlašování pro vlastní zásady (a další úvodní balíčky) na GitHubu: [Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/scénáře/Phone-number-Password][starter-pack-phone] – soubory zásad sady počátečního ověřování a transformace deklarací v telefonním čísle:
* [Definování technického profilu Multi-Factor Authentication služby Azure AD](multi-factor-auth-technical-profile.md)
* [Definovat transformace deklarací telefonního čísla](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
