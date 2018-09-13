---
title: Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účtů GitHub ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 92ae3e0697c2ecd345c8e75690d40a81ef473ba4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713778"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

Pokud chcete použít účet Github jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, je potřeba vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Github, můžete získat na [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace OAuth Githubu

1. Přihlaste se k [GitHub pro vývojáře](https://github.com/settings/developers) pomocí svých přihlašovacích údajů Githubu.
2. Vyberte **aplikace OAuth** a pak vyberte **nové aplikace OAuth**.
3. Zadejte **název_aplikace** a **adresa URL domovské stránky**.
4. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **adresu URL zpětného volání autorizace**. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C.
5. Klikněte na tlačítko **zaregistrovat aplikaci**.
6. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Jak přidat zprostředkovatele identity do svého tenanta potřebujete.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Nakonfigurujte účet GitHub jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.  

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Githubu*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Githubu (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace účtu Githubu, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci účtu Github.