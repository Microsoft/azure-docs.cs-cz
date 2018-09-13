---
title: Nastavení registrace a přihlášení s účtem QQ pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty QQ ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7abe86d49ec62460f4bfe039cbd935efe21caba8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716328"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem QQ pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-qq-application"></a>Vytvoření aplikace QQ

Použít účet QQ jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet QQ, získáte ji na [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se na programu pro vývojáře QQ

1. Přihlaste se k [portál pro vývojáře QQ](http://open.qq.com) pomocí svých přihlašovacích údajů účtu QQ.
2. Po přihlášení, přejděte na [ http://open.qq.com/reg ](http://open.qq.com/reg) sami zaregistrovat jako vývojář.
3. Vyberte**个人**(samostatný Vývojář).
4. Zadejte požadované informace a vyberte**下一步**(dál).
5. Abyste prošli procesem ověření e-mailu. Budete muset počkat několik dní, po registraci jako vývojář schválení. 

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejděte do [ (Nastavení)https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Integrace a služby).
2. Vyberte**应用管理**(Správa aplikací).
5. Vyberte**创建应用**(Vytvoření aplikace) a zadejte požadované informace.
7. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` v**授权回调域**(adresu URL zpětného volání). Například pokud vaše `tenant_name` je contoso, nastavte adresu URL bude `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Vyberte**创建应用**(vytvořit aplikaci).
9. Na stránce potvrzení vyberte**应用管理**(Správa aplikací) se vrátíte na stránku řízení aplikací.
10. Vyberte**查看**(Zobrazit) vedle aplikace, které jste vytvořili.
11. Vyberte**修改**(Upravit).
12. Kopírovat **ID aplikace** a **klíče aplikace**. Oba tyto hodnoty pro přidání poskytovatele identity do svého tenanta potřebujete.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurace QQ jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.  

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *QQ*.
6. Vyberte **typ zprostředkovatele identit**vyberte **QQ (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte klíč aplikace, který jste si poznamenali jako **tajný kód klienta** z QQ aplikace, která jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci QQ.

