---
title: Nastavení registrace a přihlášení pomocí účtu Google pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Google ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: af8a727bf9733b744b1ed429420cd8fde6e3e6f1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176127"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Google pomocí Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace služby Google

Použít účet Google jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Google můžete získat na [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Přihlaste se k [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů účtu Google.
2. Vyberte **vytvořit projekt**a potom klikněte na tlačítko **vytvořit**. Pokud jste vytvořili projekty před, vyberte seznam projektů a pak vyberte **nový projekt**.
3. Zadejte **název projektu**, klikněte na tlačítko **vytvořit**a zkontrolujte, že používáte nový projekt.
3. Vyberte **pověření** v nabídce vlevo a pak vyberte **Vytvořte přihlašovací údaje** > **ID klienta Oauth**.
4. Vyberte **obrazovkami pro vyjádření souhlasu konfigurovat**.
5. Vyberte nebo zadejte platný **e-mailová adresa**, zadejte **název produktu, který se uživatelům zobrazí**, přidejte `b2clogin.com` k **oprávnění domény**a klikněte na tlačítko **uložit** .
6. V části **typ aplikace**vyberte **webovou aplikaci**.
7. Zadejte **název** pro vaši aplikaci, zadejte `https://your-tenant-name.b2clogin.com` v **oprávnění JavaScript zdroje**, a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **identifikátory URI pro přesměrování autorizovaní**. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C.
8. Klikněte na možnost **Vytvořit**.
9. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Budete potřebovat oba poskytovatelé konfigurace Google jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurace Google účtu jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Google*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Google**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace Google, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Google.

