---
title: 'Azure Active Directory B2C: Konfigurace LinkedIn | Microsoft Docs'
description: Zadejte registrace a přihlášení k příjemce s účty LinkedIn v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 91970b3ec41c645cd1cd3e203cc96ad655dafd7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s účty LinkedIn
## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn
Pokud chcete použít LinkedIn jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci LinkedIn a zadat se správné parametry. Potřebujete účet LinkedIn k tomu. Pokud nemáte, můžete ho na získat [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Přejděte na [LinkedIn vývojáři webu](https://www.developer.linkedin.com/) a přihlaste se pomocí přihlašovacích údajů účtu LinkedIn.
2. Klikněte na tlačítko **Moje aplikace** v horní nabídce panelu a pak klikněte na tlačítko **vytvořit aplikaci**.
   
    ![LinkedIn - nové aplikace](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. V **vytvořte novou aplikaci** formuláři, zadejte příslušné informace (**název společnosti**, **název**, **popis**, **Adresa URL aplikace Logo**, **využívání aplikací**, **adresu URL webu**, **e-mailová adresa** a **Telefon do zaměstnání**).
4. Souhlas s **LinkedIn API podmínky použití** a klikněte na tlačítko **odeslání**.
   
    ![LinkedIn – registrace aplikace](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Zkopírujte hodnoty z **ID klienta** a **tajný klíč klienta**. (Je najdete v části **ověřovací klíče**.) Budete potřebovat oba dva konfigurace LinkedIn jako zprostředkovatele identity ve vašem klientovi.
   
   > [!NOTE]
   > **Tajný klíč klienta** je důležitým bezpečnostním pověřením.
   > 
   > 
6. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **oprávnění adres URL pro přesměrování** pole (v části **OAuth 2.0**). Nahraďte **{klient}** s názvem vašeho klienta (například contoso.onmicrosoft.com). Klikněte na tlačítko **přidat**a potom klikněte na **aktualizace**. **{Klient}** hodnota je malá a velká písmena.
   
    ![LinkedIn – instalační program aplikace](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurace LinkedIn jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "LI".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **LinkedIn**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte ID klienta a tajný klíč klienta LinkedIn aplikace, kterou jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci LinkedIn.

