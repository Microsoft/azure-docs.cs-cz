---
title: Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účtů GitHub ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11f3f190c0f55e45c549a8bd1de35f78eb7b752d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337425"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

Pokud chcete použít účet Github jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, je potřeba vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Github, můžete získat na [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace OAuth Githubu

1. Přihlaste se k [GitHub pro vývojáře](https://github.com/settings/developers) pomocí svých přihlašovacích údajů Githubu.
2. Vyberte **aplikace OAuth** a pak vyberte **registrace nové aplikace**.
3. Zadejte **název_aplikace** a **adresa URL domovské stránky**.
4. Zadejte `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` v **adresu URL zpětného volání autorizace**. Nahraďte **{klient}** s názvem vašeho tenanta Azure AD B2C (například contosob2c).
5. Klikněte na tlačítko **zaregistrovat aplikaci**.
6. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Jak přidat zprostředkovatele identity do svého tenanta potřebujete.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Nakonfigurujte účet GitHub jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Githubu*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Githubu (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace účtu Githubu, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci účtu Github.