---
title: 'Azure Active Directory B2C: Google + konfigurace | Microsoft Docs'
description: Zadejte registrace a přihlášení k příjemce s účty Google + v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
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
ms.openlocfilehash: db3044754e0c1316b49eda0600d41e3009c1ba16
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s Google + účty
## <a name="create-a-google-application"></a>Vytvoření aplikace Google +
Pokud chcete používat Google + jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci Google + a zadat se správné parametry. Potřebujete účet Google + k tomu. Pokud nemáte, můžete ho na získat [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Přejděte na [konzole pro vývojáře Google](https://console.developers.google.com/) a přihlaste se pomocí přihlašovací údaje účtu Google +.
2. Klikněte na tlačítko **vytvořit projekt**, zadejte **název projektu**a potom klikněte na **vytvořit**.
   
    ![Google + – Začínáme](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + – nový projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Klikněte na tlačítko **rozhraní API Správce** a pak klikněte na **pověření** v levém navigačním panelu.
4. Klikněte **obrazovky souhlas OAuth** v horní části.
   
    ![Google + - přihlašovací údaje](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Vyberte nebo zadejte platný **e-mailová adresa**, poskytovat **název produktu**a klikněte na tlačítko **Uložit**.
   
    ![Google + - obrazovky souhlas OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Klikněte na tlačítko **nové přihlašovací údaje** a potom zvolte **ID klienta OAuth**.
   
    ![Google + - obrazovky souhlas OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. V části **typ aplikace**, vyberte **webové aplikace**.
   
    ![Google + - obrazovky souhlas OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Zadejte **název** pro vaši aplikaci, zadejte `https://login.microsoftonline.com` v **zdroje oprávnění JavaScript** pole, a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **autorizováno přesměrování identifikátory URI** pole. Nahraďte **{klient}** s názvem vašeho klienta (například contosob2c.onmicrosoft.com). **{Klient}** hodnota je malá a velká písmena. Klikněte na možnost **Vytvořit**.
   
    ![Google + - vytvořit ID klienta](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Zkopírujte hodnoty z **ID klienta** a **tajný klíč klienta**. Budete potřebovat oba dva konfigurace Google + jako zprostředkovatele identity ve vašem klientovi. **Tajný klíč klienta** je důležitým bezpečnostním pověřením.
   
    ![Google + - tajný klíč klienta](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurace Google + jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "G +".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Google**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte ID klienta a tajný klíč klienta aplikace Google +, který jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci Google +.

