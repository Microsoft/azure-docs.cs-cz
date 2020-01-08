---
title: Registrace a přihlášení k telefonnímu programu s vlastními zásadami
titleSuffix: Azure AD B2C
description: Naučte se, jak odesílat jednorázová hesla v textových zprávách telefonům uživatelů vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 96bf088e6db29817d1ae4276c254a84723e5d03d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480187"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Nastavte si registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C

Registrace a přihlášení k telefonnímu programu v Azure Active Directory B2C (Azure AD B2C) umožňuje vašim uživatelům se zaregistrovat a přihlásit k vašim aplikacím pomocí JEDNORÁZOVého hesla, které se v textové zprávě pošle na telefon. Jednorázová hesla můžou přispět k minimalizaci rizika vašich uživatelů forgetting nebo k ohrožení zabezpečení vašich hesel.

Postupujte podle kroků v tomto článku a použijte vlastní zásady, které zákazníkům umožňují registraci a přihlášení k vašim aplikacím pomocí jednorázového hesla odeslaného na telefon.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Požadavky

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Webová aplikace zaregistrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Vlastní zásady](active-directory-b2c-get-started-custom.md) odeslané do vašeho tenanta

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Získání registračního a registračního balíčku & přihlášení k telefonu

Začněte tím, že aktualizujete soubory vlastních zásad registrace a přihlášení telefon pro práci s vaším klientem Azure AD B2C.

V následujících krocích se předpokládá, že jste splnili [požadavky](#prerequisites) a již jste naklonoval úložiště [Úvodní sady Custom Policy Pack][starter-pack] do místního počítače.

1. V místním klonu úložiště počátečního balíčku Najděte [vlastní soubory zásad pro registraci a přihlašování telefonem][starter-pack-phone] nebo si je Stáhněte přímo. Soubory zásad XML jsou umístěné v následujícím adresáři:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, budou `contosob2c.onmicrosoft.com`všechny instance `yourtenant.onmicrosoft.com`.

1. Dokončete kroky v části [Přidání ID aplikací do vlastní zásady](active-directory-b2c-get-started-custom.md#add-application-ids-to-the-custom-policy) [v tématu Začínáme s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). V takovém případě aktualizujte `/phone-number-passwordless/` **`Phone_Email_Base.xml`** s **ID aplikace (klienta)** dvou aplikací, které jste zaregistrovali při dokončování požadavků, *IdentityExperienceFramework* a *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Nahrání souborů zásad

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do svého tenanta Azure AD B2C.
1. V části **zásady**vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**.
1. Soubory zásad nahrajte v následujícím pořadí:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Při nahrávání každého souboru přidá Azure předponu `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **vlastní zásady**vyberte **B2C_1A_SignUpOrSignInWithPhone**.
1. V části **Vybrat aplikaci**vyberte aplikaci *WebApp1* , kterou jste zaregistrovali při dokončování požadavků.
1. V **možnosti vybrat adresu URL odpovědi**zvolte `https://jwt.ms`.
1. Vyberte **Spustit nyní** a zaregistrujte se pomocí e-mailové adresy nebo telefonního čísla.
1. Vyberte znovu **Spustit** znovu a přihlaste se pomocí stejného účtu, abyste měli jistotu, že máte správnou konfiguraci.

## <a name="next-steps"></a>Další kroky

Můžete najít registrační sadu vlastních zásad pro registraci a přihlašování pro vlastní zásady (a další úvodní balíčky) na GitHubu:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/scénáře/telefonní číslo – nejenom hesla][starter-pack-phone]

Soubory zásad počátečního sady používají technické profily Multi-Factor Authentication a transformace deklarací v telefonním čísle:

* [Definování technického profilu Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definovat transformace deklarací telefonního čísla](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
