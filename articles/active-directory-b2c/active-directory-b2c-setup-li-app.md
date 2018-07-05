---
title: Konfigurace Linkedinu v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účtem LinkedIn ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7588711bd1c2a02e2e9a100d2ba182f43e7df488
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446066"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení pro uživatele s účtem LinkedIn
## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn
Použití LinkedIn jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci, LinkedIn a zadejte správné parametry. Budete potřebovat účet LinkedIn, chcete-li to provést. Pokud ho nemáte, můžete získat na [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Přejděte [web vývojáře LinkedIn](https://www.developer.linkedin.com/) a přihlaste se pomocí přihlašovacích údajů k účtu LinkedIn.
2. Klikněte na tlačítko **Moje aplikace** v horním řádku nabídek a pak klikněte na tlačítko **vytvořit aplikaci**.
   
    ![LinkedIn – nové aplikace](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. V **vytvořte novou aplikaci** formulářů, zadejte příslušné informace (**název společnosti**, **název**, **popis**, **Adresa URL loga aplikace**, **využívání aplikací**, **adresu URL webu**, **e-mailová adresa** a **Telefondozaměstnání**).
4. Souhlas s **LinkedIn API Terms of Use** a klikněte na tlačítko **odeslat**.
   
    ![LinkedIn – registrace aplikace](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. (Můžete najít v rámci **ověřovací klíče**.) Budete potřebovat oba poskytovatelé konfigurace LinkedIn jako zprostředkovatele identity ve vašem tenantovi.
   
   > [!NOTE]
   > **Tajný kód klienta** je důležitým bezpečnostním pověřením.
   > 
   > 
6. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **oprávnění adresy URL pro přesměrování** pole (v části **OAuth 2.0**). Nahraďte **{klient}** s názvem vašeho tenanta (například contoso.onmicrosoft.com). Klikněte na tlačítko **přidat**a potom klikněte na tlačítko **aktualizace**. **{Klient}** hodnota by měla být malými písmeny.
   
    ![LinkedIn – instalační program aplikace](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurace LinkedIn jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "LI".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **LinkedIn**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta a tajný kód klienta LinkedIn aplikace, kterou jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci LinkedIn.

