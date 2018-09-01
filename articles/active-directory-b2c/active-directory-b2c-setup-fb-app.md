---
title: Nastavení registrace a přihlášení pomocí účtu sítě Facebook pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty služby Facebook ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344602"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu sítě Facebook pomocí Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook

Použít účet Facebook jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet na Facebooku, získáte ji na [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přihlaste se k [Facebook pro vývojáře](https://developers.facebook.com/) pomocí svých přihlašovacích údajů účtu sítě Facebook.
2. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář sítě Facebook. Chcete-li to provést, vyberte **zaregistrovat** v pravém horním rohu stránky, přijmout zásady na Facebooku a dokončete registraci.
3. Vyberte **Moje aplikace** a potom klikněte na tlačítko **přidat novou aplikaci**. 
4. Zadejte **zobrazovaný název** a platný **E-mail kontaktu**.
5. Klikněte na tlačítko **vytvoření ID aplikace**. To může vyžadovat přijmout zásady platformy Facebooku a proveďte kontrolu zabezpečení online.
6. Vyberte **nastavení** > **základní**.
7. V dolní části stránky vyberte **přidat platformy**a pak vyberte **webu**.
8. Zadejte `https://{tenantname}.b2clogin.com/` v **URL webu**. Zadejte adresu URL, **adresa URL zásad ochrany osobních údajů**, například `http://www.contoso.com`.
9. Vyberte **uložit změny**.
11. V horní části stránky, zkopírujte hodnotu **ID aplikace**. 
12. Klikněte na tlačítko **zobrazit** a zkopírujte hodnotu **tajný kód aplikace**. Obou z nich použijete ke konfiguraci sítě Facebook jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód aplikace** je důležitým bezpečnostním pověřením.
13. Vyberte **produkty**a pak vyberte **nastavení** pod **přihlášení k Facebooku**.
14. Vyberte **nastavení** pod **přihlášení k Facebooku**.
15. Zadejte `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` v **identifikátory URI pro přesměrování OAuth platný** . Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c). Klikněte na tlačítko **uložit změny** v dolní části stránky.
16. K zajištění dostupnosti sítě Facebook aplikace Azure AD B2C, vyberte **revizi aplikace**, nastavte **veřejného zkontrolujte Moje aplikace?** k **Ano**, zvolit některou kategorii, například `Business and Pages`a potom klikněte na tlačítko **potvrdit**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace účtu sítě Facebook jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Facebook*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Facebook**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód aplikace, které jste si poznamenali jako **tajný kód klienta** z aplikace Facebook, kterou jste vytvořili).
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci sítě Facebook.