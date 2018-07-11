---
title: Nastavení registrace a přihlášení pomocí účtu Google pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Google ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915635"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Google pomocí Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace služby Google

Použít účet Google jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Google můžete získat na [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Přihlaste se k [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů účtu Google.
2. Vyberte **vytvořit projekt**a potom klikněte na tlačítko **vytvořit**. Pokud jste vytvořili projekty před, vyberte seznam projektů a pak vyberte **nový projekt**.
3. Zadejte **název projektu**a potom klikněte na tlačítko **vytvořit**.
3. Vyberte **pověření** v nabídce vlevo a pak vyberte **Vytvořte přihlašovací údaje** > **ID klienta Oauth**.
4. Vyberte **obrazovkami pro vyjádření souhlasu konfigurovat**.
5. Vyberte nebo zadejte platný **e-mailová adresa**, zadejte **název produktu, který se uživatelům zobrazí**a klikněte na tlačítko **Uložit**.
6. V části **typ aplikace**vyberte **webovou aplikaci**.
7. Zadejte **název** pro vaši aplikaci, zadejte `https://login.microsoftonline.com` v **oprávnění JavaScript zdroje**, a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI pro přesměrování autorizovaní**. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com).
8. Klikněte na možnost **Vytvořit**.
9. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Budete potřebovat oba poskytovatelé konfigurace Google jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurace Google účtu jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Google*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Google**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace Google, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Google.

