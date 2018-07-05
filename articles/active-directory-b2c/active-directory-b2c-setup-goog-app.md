---
title: Konfigurace Google +. v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty Google + ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe5722e8d5a0a8a5bf172577777ccb899fb7b94d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443422"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení pro uživatele s účty Google +.
## <a name="create-a-google-application"></a>Vytvoření aplikace služby Google +
Pokud chcete používat Google + jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvoření aplikace služby Google + a zadejte správné parametry. Potřebujete účet Google +. Provedete to tak. Pokud ho nemáte, můžete získat na [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Přejděte [konzole pro vývojáře Google](https://console.developers.google.com/) a přihlaste se pomocí pověření účtu Google +.
2. Klikněte na tlačítko **vytvořit projekt**, zadejte **název projektu**a potom klikněte na tlačítko **vytvořit**.
   
    ![Google + – Začínáme](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + – nový projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Klikněte na tlačítko **správce rozhraní API** a potom klikněte na tlačítko **pověření** v levém navigačním panelu.
4. Klikněte na tlačítko **obrazovku se souhlasem OAuth** kartě v horní části.
   
    ![Google + - přihlašovacích údajů](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Vyberte nebo zadejte platný **e-mailová adresa**, zadejte **název produktu**a klikněte na tlačítko **Uložit**.
   
    ![Google + - obrazovku se souhlasem OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Klikněte na tlačítko **nové přihlašovací údaje** a klikněte na tlačítko **ID klienta OAuth**.
   
    ![Google + - obrazovku se souhlasem OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. V části **typ aplikace**vyberte **webovou aplikaci**.
   
    ![Google + - obrazovku se souhlasem OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Zadejte **název** pro vaši aplikaci, zadejte `https://login.microsoftonline.com` v **oprávnění JavaScript zdroje** pole, a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI pro přesměrování autorizovaní** pole. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com). **{Klient}** hodnota je velká a malá písmena. Klikněte na možnost **Vytvořit**.
   
    ![Google + - vytvořit ID klienta](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Budete potřebovat oba poskytovatelé konfigurace Google + jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.
   
    ![Google + - tajný klíč klienta](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurace Google + jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "G +".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Google**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta a tajný kód klienta aplikace služby Google +, který jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Google +.

