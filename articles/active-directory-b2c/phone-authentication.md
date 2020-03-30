---
title: Registrace telefonu a přihlášení pomocí vlastních zásad (náhled)
titleSuffix: Azure AD B2C
description: Posílejte jednorázová hesla (OTP) v textových zprávách do telefonů uživatelů aplikace s vlastními zásadami ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183954"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Nastavení registrace a přihlášení pomocí vlastních zásad ve službě Azure AD B2C (Preview)

Registrace a přihlášení k telefonu ve službě Azure Active Directory B2C (Azure AD B2C) umožňuje uživatelům zaregistrovat se a přihlásit se k vašim aplikacím pomocí jednorázového hesla (OTP) odeslané v textové zprávě do svého telefonu. Jednorázová hesla mohou pomoci minimalizovat riziko, že uživatelé zamýšlejí nebo mají svá hesla ohrožena.

Podle kroků v tomto článku pomocí vlastních zásad, které zákazníkům umožní zaregistrovat se a přihlásit se k aplikacím pomocí jednorázového hesla odeslaného do jejich telefonu.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Ceny

Jednorázová hesla jsou uživatelům odesílána pomocí textových zpráv SMS a za každou odeslanou zprávu vám mohou být účtovány poplatky. Informace o cenách najdete v části **Samostatné poplatky** [v cenách Služby Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Požadavky

Před nastavením otp potřebujete následující prostředky.

* [Klient Azure AD B2C](tutorial-create-tenant.md)
* [Webová aplikace registrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Vlastní zásady](custom-policy-get-started.md) nahrané do vašeho tenanta

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Získejte & startovací balíček pro registraci v telefonu

Začněte aktualizací souborů vlastních zásad pro registraci a přihlášení telefonu tak, aby fungovaly s klientem Azure AD B2C.

Následující kroky předpokládají, že jste dokončili [požadavky](#prerequisites) a již jste naklonovali úložiště [vlastních sad startovacíbalíček][starter-pack] do místního počítače.

1. Najděte [vlastní soubory zásad registrace a přihlášení telefonu][starter-pack-phone] v místním klonu repo startovacísady nebo si je stáhněte přímo. Soubory zásad XML jsou umístěny v následujícím adresáři:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. V každém souboru `yourtenant` nahraďte řetězec názvem vašeho klienta Azure AD B2C. Například pokud název klienta B2C je *contosob2c*, `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`všechny instance become .

1. Dokončete kroky v [části Přidání ID aplikací do](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) oddílu Vlastní zásady [Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md). V takovém případě `/phone-number-passwordless/` **`Phone_Email_Base.xml`** aktualizujte **pomocí ID aplikace (klienta)** dvou aplikací, které jste zaregistrovali při vyplňování požadavků, *IdentityExperienceFramework* a *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Nahrání souborů zásad

1. Přihlaste se k [portálu Azure a](https://portal.azure.com) přejděte na svého klienta Azure AD B2C.
1. V části **Zásady**vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **Nahrát vlastní zásady**.
1. Nahrajte soubory zásad v následujícím pořadí:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneNeboEmail.xml*
    1. *ProfilEditovatPhoneOnly.xml*
    1. *ProfilEditovatPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Při nahrávání jednotlivých souborů azure `B2C_1A_`přidá předponu .

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **Vlastní zásady**vyberte **B2C_1A_SignUpOrSignInWithPhone**.
1. V části **Select application**vyberte aplikaci *webapp1,* kterou jste zaregistrovali při vyplňování požadavků.
1. V **popřípadě Vybrat adresu url odpovědi**zvolte `https://jwt.ms`.
1. Vyberte **Spustit a** zaregistrujte se pomocí e-mailové adresy nebo telefonního čísla.
1. Vyberte Spustit **nyní** znovu a přihlaste se pomocí stejného účtu, abyste potvrdili, že máte správnou konfiguraci.

## <a name="get-user-account-by-phone-number"></a>Získání uživatelského účtu podle telefonního čísla

Uživatel, který se zaregistruje pomocí telefonního čísla, ale neposkytne e-mailovou adresu pro obnovení, se zaznamená do adresáře Azure AD B2C s jejich telefonním číslem jako svým přihlašovacím jménem. Pokud si pak uživatel přeje změnit své telefonní číslo, musí váš tým technické podpory nejprve najít svůj účet a poté aktualizovat své telefonní číslo.

Uživatele můžete najít podle jeho telefonního čísla (přihlašovací jméno) pomocí [aplikace Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Například:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Další kroky

Na GitHubu najdete balíček pro registraci a přihlášení k vlastním zásadám (a další startovací balíčky):

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

Soubory zásad starter pack používají vícefaktorové ověřování technické profily a telefonní číslo nároky transformace:

* [Definování technického profilu Azure S vícefaktorovým ověřováním](multi-factor-auth-technical-profile.md)
* [Definování transformací deklarací telefonního čísla](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
