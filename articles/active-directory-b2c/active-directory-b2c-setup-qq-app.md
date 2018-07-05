---
title: Konfigurace QQ v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty QQ ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9a0f56158f08dd3b22078f111c9ec6911b726c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444425"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: Poskytněte uživatelům s účty QQ registrace a přihlášení

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-qq-application"></a>Vytvoření aplikace QQ

Použití QQ jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci QQ a zadejte správné parametry. Budete potřebovat účet QQ provedete to tak. Pokud ho nemáte, můžete jej získat na [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se na programu pro vývojáře QQ

1. Přejděte [portál pro vývojáře QQ](http://open.qq.com) a přihlaste se pomocí přihlašovacích údajů k účtu QQ.
2. Po přihlášení, přejděte na [ http://open.qq.com/reg ](http://open.qq.com/reg) sami zaregistrovat jako vývojář.
3. V nabídce vyberte**个人**(samostatný Vývojář).
4. Zadejte požadované informace do formuláře a klikněte na tlačítko**下一步**(dál).
5. Abyste prošli procesem ověření e-mailu.

> [!NOTE]
> Budete muset počkat několik dní, po registraci jako vývojář schválení. 

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejděte do [ (Nastavení)https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Integrace a služby).
2. Klikněte na**应用管理**(Správa aplikací).
3. Klikněte na**创建应用**(vytvořit aplikaci).
4. Zadejte informace potřebné aplikace.
5. Klikněte na**创建应用**(vytvořit aplikaci).
6. Zadejte požadované informace.
7. Pro**授权回调域**(adresu URL zpětného volání) zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL bude `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Klikněte na**创建应用**(vytvořit aplikaci).
9. Na potvrzovací stránce klikněte na**应用管理**(Správa aplikací) se vrátíte na stránku řízení aplikací.
10. Klikněte na**查看**(Zobrazit) vedle aplikace, které jste právě vytvořili.
11. Klikněte na**修改**(Upravit).
12. V horní části stránky, zkopírujte **ID aplikace** a **klíče aplikace**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Konfigurace QQ jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "QQ".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **QQ**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Zadejte **klíče aplikace** , který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný kód aplikace** , který jste zkopírovali dříve, jako **tajný kód klienta**.
9. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci QQ.

