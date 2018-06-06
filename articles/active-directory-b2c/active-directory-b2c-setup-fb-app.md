---
title: Konfigurace sítě Facebook v Azure Active Directory B2C | Microsoft Docs
description: Zadejte registrace a přihlášení k příjemce s účty sítě Facebook v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ceab8df79337796bed314e04bacc37bf2ebe1d00
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712313"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s účty služby Facebook
## <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook
Pokud chcete použít Facebook jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci pro Facebook a přiřaďte mu správné parametry. Je nutné k tomu účtu sítě Facebook. Pokud nemáte, můžete ho na získat [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přejděte na [Facebook pro vývojáře](https://developers.facebook.com/) webu a přihlaste se pomocí vaší sítě Facebook přihlašovací údaje účtu.
2. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář Facebook. Chcete-li to provést, klikněte na tlačítko **zaregistrovat** (v horním pravém horním rohu stránky), přijměte zásady pro Facebook a dokončete registraci.
3. Klikněte na tlačítko **Moje aplikace** a pak klikněte na **přidejte novou aplikaci**. 
4. Ve formuláři, zadejte **zobrazovaný název** platné **e-mailu kontaktujte**.
5. Klikněte na tlačítko **vytvoření ID aplikace**. To může vyžadovat přijměte zásady platformy Facebook a dokončit kontrolu zabezpečení online.
6. V levém sloupci klikněte na tlačítko **nastavení** a pak vyberte **základní** Pokud již není vybrán.
7. Vyberte **kategorie**. 
8. Klikněte na tlačítko **+ přidejte platformu** a vyberte **webu**.
   
    ![Facebook – nastavení](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook – nastavení – Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Zadejte `https://login.microsoftonline.com/` v **adresa URL webu** pole a pak klikněte na **uložit změny** v dolní části stránky.
   
    ![Facebook – adresa URL webu](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Zkopírujte hodnotu **ID aplikace**. Klikněte na tlačítko **zobrazit** a zkopírujte hodnotu **tajný klíč aplikace**. Budete potřebovat obou z nich nakonfigurovat jako zprostředkovatel identity ve vašem klientovi sítě Facebook. **Tajný klíč aplikace** je důležitým bezpečnostním pověřením.
   
    ![Facebook - ID aplikace a tajný klíč aplikace](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Klikněte na tlačítko **+ přidat produkt** na levé straně a pak **nastavit až** tlačítko pro **Facebook přihlášení**.
   
    ![Facebook - Facebook přihlášení](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Klikněte na tlačítko **nastavení** na pravém navigaci v části **Facebook přihlášení**

    ![Facebook – nastavení přihlášení Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI přesměrování platný OAuth** pole **nastavení klienta OAuth** části. Nahraďte **{klient}** s názvem vašeho klienta (například contosob2c.onmicrosoft.com). Klikněte na tlačítko **uložit změny** v dolní části stránky.
    
    ![Facebook – identifikátor URI přesměrování OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Chcete-li aplikace Facebook použitelná pro Azure AD B2C, je třeba, aby veřejně dostupné. To provedete kliknutím na **revize aplikace** na levé straně a vypnutím přepínače v horní části stránky **Ano** a kliknutím na **potvrdit**.
    
    ![Facebook - veřejné aplikace](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurovat jako zprostředkovatel identity ve vašem klientovi sítě Facebook
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Facebook".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Facebook**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte ID aplikace a tajný klíč aplikace (of aplikace Facebook, který jste vytvořili dříve) v **ID klienta** a **tajný klíč klienta** polí v uvedeném pořadí.
7. Klikněte na tlačítko **OK**a potom klikněte na **vytvořit** uložte konfiguraci sítě Facebook.

> [!NOTE]
> Přidání **zprostředkovatele Identity** ke klientovi neupravuje existující zásady. Nezapomeňte aktualizovat zásady zahrnutím zprostředkovatele identity, který jste právě vytvořili.
>