---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674221"
---
## <a name="configure-the-relying-party-policy"></a>Konfigurace zásady předávající strany

Zásady předávající strany, například [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), určují cestu uživatele, která Azure AD B2C spustí. Najde element **DefaultUserJourney** v rámci [předávající strany](../articles/active-directory-b2c/relyingparty.md). Aktualizujte  **ReferenceId** tak, aby odpovídalo ID cesty uživatele, do kterého jste přidali poskytovatele identity. 

V následujícím příkladu `CustomSignUpOrSignIn` je pro cestu uživatele **ReferenceId** nastavena na `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Nahrajte vlastní zásadu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu** a pak nahrajte dva soubory zásad, které jste změnili v následujícím pořadí: zásady rozšíření, například `TrustFrameworkExtensions.xml` zásady předávající strany, například `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte zásady předávající strany, například `B2C_1A_signup_signin`
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

