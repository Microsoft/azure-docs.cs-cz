---
title: Konfigurace sítě Facebook v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty služby Facebook ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 316e44ea92a25ab804c8cc499f91c45e4a66ef02
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445496"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení pro uživatele s účty služby Facebook
## <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook
Použití sítě Facebook jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci pro Facebook a zadejte správné parametry. Účet služby Facebook k tomu potřebujete. Pokud ho nemáte, můžete získat na [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přejděte [Facebook pro vývojáře](https://developers.facebook.com/) přihlašovací údaje účtu pro web a přihlaste se pomocí vaší sítě Facebook.
2. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář sítě Facebook. Chcete-li to provést, klikněte na tlačítko **zaregistrovat** (na pravém horním rohu stránky), přijměte zásady na Facebooku a dokončete registraci.
3. Klikněte na tlačítko **Moje aplikace** a potom klikněte na tlačítko **přidejte novou aplikaci**. 
4. Ve formuláři, zadejte **zobrazovaný název** a platný **E-mail kontaktu**.
5. Klikněte na tlačítko **vytvoření ID aplikace**. To může vyžadovat přijmout zásady platformy Facebooku a proveďte kontrolu zabezpečení online.
6. V levém sloupci klikněte na tlačítko **nastavení** a pak vyberte **základní** Pokud již není vybrána.
7. Vyberte **kategorie**. 
8. Klikněte na tlačítko **+ přidat platformu** a vyberte **webu**.
   
    ![Facebook – nastavení](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Settings - webu](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Zadejte `https://login.microsoftonline.com/` v **adresa URL webu** pole a potom klikněte na tlačítko **uložit změny** v dolní části stránky.
   
    ![Facebook – adresa URL webu](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Zkopírujte hodnotu **ID aplikace**. Klikněte na tlačítko **zobrazit** a zkopírujte hodnotu **tajný kód aplikace**. Budete potřebovat oba poskytovatelé konfigurace sítě Facebook jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód aplikace** je důležitým bezpečnostním pověřením.
   
    ![Facebook – ID aplikace a tajný kód aplikace](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Klikněte na tlačítko **+ přidat produkt** na levé straně a pak **Set Up** tlačítko pro **přihlášení k Facebooku**.
   
    ![Facebook – přihlášení k Facebooku](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Klikněte na tlačítko **nastavení** na pravé navigace v rámci **přihlášení k Facebooku**

    ![Facebook – nastavení přihlášení k Facebooku](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI pro přesměrování OAuth platný** pole **nastavení klienta OAuth** oddílu. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com). Klikněte na tlačítko **uložit změny** v dolní části stránky.
    
    ![Facebook – identifikátor URI přesměrování OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Chcete-li aplikace Facebook použitelný pro Azure AD B2C, musíte je zpřístupnit veřejnosti. To můžete provést kliknutím **revizi aplikace** v levém navigačním panelu a to zapnutím přepínače v horní části stránky a **Ano** a kliknete na **potvrdit**.
    
    ![Facebook – veřejné aplikace](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurace sítě Facebook jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Facebook".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Facebook**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace a tajný kód aplikace (aplikace Facebook, kterou jste vytvořili dříve) v **ID klienta** a **tajný kód klienta** pole v uvedeném pořadí.
7. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci sítě Facebook.

> [!NOTE]
> Přidání **zprostředkovatele Identity** do svého tenanta neprovede žádné změny existujících zásad. Nezapomeňte aktualizovat zásady zahrnutím zprostředkovatele identity, které jste právě vytvořili.
>