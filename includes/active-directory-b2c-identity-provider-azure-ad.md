---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678050"
---
## <a name="register-an-azure-ad-app"></a>Registrace aplikace Azure AD

Chcete-li povolit přihlášení pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v rámci klienta Azure AD organizace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD organizace (například contoso.com). V horní nabídce vyberte **filtr Directory + subscription** a pak zvolte adresář, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace. Například, `Azure AD B2C App`.
1. Přijměte výchozí výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování**přijměte hodnotu **webu**a zadejte následující `your-B2C-tenant-name` adresu URL ve všech malých písmenech, kde je nahrazen názvem klienta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Vyberte **Zaregistrovat**. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.
1. Vyberte **certifikáty & tajných kódů**a potom vyberte **Nový tajný klíč klienta**.
1. Zadejte **popis** tajného klíče, vyberte vypršení platnosti a pak vyberte **Přidat**. Zaznamenejte **hodnotu** tajného klíče pro pozdější krok.

### <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

Pokud chcete získat `family_name` deklarace identity `given_name` z Azure AD, můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci v unovém rozhraní portálu Azure nebo manifestu aplikace. Další informace najdete v tématu [Jak poskytovat volitelné deklarace identity do aplikace Azure AD](/active-directory/develop/active-directory-optional-claims.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.
1. V části **Spravovat** vyberte **konfigurace tokenu**.
1. Vyberte **Přidat volitelnou deklaraci .**
1. Pro **typ tokenu**vyberte **ID**.
1. Vyberte volitelné deklarace `family_name` `given_name`identity, které chcete přidat, a .
1. Klikněte na tlačítko **Add** (Přidat).