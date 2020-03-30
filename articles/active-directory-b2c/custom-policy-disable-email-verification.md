---
title: Zakázání ověření e-mailu při registraci zákazníka pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Přečtěte si, jak zakázat ověřování e-mailů během registrace zákazníků ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136138"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Zakázání ověření e-mailu během registrace zákazníka pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí sociálních a místních účtů.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Přidání metadat do samoobslužného technického profilu

Technický profil **LocalAccountSignUpWithLogonEmail** je [vlastní uplatněný](self-asserted-technical-profile.md), který je vyvolán během toku registrace. Chcete-li zakázat ověření `EnforceEmailVerification` e-mailu, nastavte metadata na false. Přepsat localAccountSignUpUpWithLogonEmail technické profily v souboru rozšíření. 

1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Najděte `ClaimsProviders` prvek. Pokud prvek neexistuje, přidejte jej.
1. Do prvku přidejte `ClaimsProviders` následujícího zprostředkovatele deklarací identity:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
4. Vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **Nahrát vlastní zásady**a potom nahrajte dva soubory zásad, které jste změnili.
2. Vyberte zásadu registrace nebo přihlášení, kterou jste nahráli, a klikněte na tlačítko **Spustit.**
3. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy bez ověření.


## <a name="next-steps"></a>Další kroky

- Další informace o [samoobslužném technickém profilu](self-asserted-technical-profile.md) naleznete v odkazu ief.
