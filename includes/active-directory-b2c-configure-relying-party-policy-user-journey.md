---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951514"
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



