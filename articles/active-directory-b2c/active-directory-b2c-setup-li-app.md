---
title: Nastavení registrace a přihlášení s účtem LinkedIn pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty LinkedIn ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 84b1ad2ecd2c027c7d8a105579059ceb957f41c6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928994"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem LinkedIn pomocí Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Použít účet LinkedIn jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet LinkedIn, získáte ji na [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Přihlaste se k [web vývojáře LinkedIn](https://www.developer.linkedin.com/) pomocí svých přihlašovacích údajů účtu LinkedIn.
2. Vyberte **Moje aplikace**a potom klikněte na tlačítko **vytvořit aplikaci**.
3. Zadejte **název společnosti**, **název_aplikace**, **popis aplikace**, **Logo aplikace**, **využívání aplikací** , **Adresu URL webu**, **e-mailová adresa**, a **Telefon do zaměstnání**.
4. Souhlas s **LinkedIn API Terms of Use** a klikněte na tlačítko **odeslat**.
5. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Najdete je v části **ověřovací klíče**. Budete potřebovat oba poskytovatelé konfigurace LinkedIn jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.
6. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **oprávnění adresy URL pro přesměrování**. Nahraďte **{klient}** s názvem vašeho tenanta (například contoso.onmicrosoft.com). Vyberte **přidat**a potom klikněte na tlačítko **aktualizace**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Nakonfigurujte účet LinkedIn jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-li-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-li-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *LinkedIn*.
6. Vyberte **typ zprostředkovatele identit**vyberte **LinkedIn**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace účet LinkedIn, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci účtu LinkedIn.

