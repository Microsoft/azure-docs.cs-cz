---
title: Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C | Microsoft Docs
description: Poskytněte zákazníkům registraci a přihlášení k účtům QQ ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9679e3216184c0dc5b8dee241c30fc69d5423aeb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963761"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-qq-application"></a>Vytvoření aplikace v QQ

Pokud chcete v Azure Active Directory (Azure AD) B2C použít účet QQ jako zprostředkovatele identity, musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet QQ, můžete ho získat na adrese [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se do programu QQ Developer program

1. Přihlaste se k [portálu pro vývojáře QQ](http://open.qq.com) pomocí přihlašovacích údajů k účtu QQ.
2. Až se přihlásíte, [https://open.qq.com/reg](https://open.qq.com/reg) pokračujte v registraci jako vývojář.
3. Vyberte**个人**(individuální vývojář).
4. Zadejte požadované informace a vyberte**下一步**(další krok).
5. Dokončete proces ověření e-mailu. Až se zaregistrujete jako vývojář, budete muset počkat několik dní. 

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejděte do [ (Nastavení)https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Integrace a služby).
2. Vyberte**应用管理**(Správa aplikací).
5. Vyberte**创建应用**(vytvořit aplikaci) a zadejte požadované informace.
7. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` v**授权回调域**(adresa URL zpětného volání). Pokud máte `tenant_name` například contoso, nastavte adresu URL `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`na.
8. Vyberte**创建应用**(vytvořit aplikaci).
9. Na stránce potvrzení vyberte**应用管理**(Správa aplikací) a vraťte se na stránku správy aplikací.
10. U aplikace, kterou jste vytvořili, vyberte**查看**(Zobrazit).
11. Vyberte**修改**(Upravit).
12. Zkopírujte **ID aplikace** a **klíč App**. Obě tyto hodnoty budete potřebovat k přidání poskytovatele identity k vašemu tenantovi.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurace QQ jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *QQ*.
6. Vyberte **typ zprostředkovatele identity**, vyberte **QQ (Preview)** a klikněte na **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si dříve poznamenali jako **ID klienta** , a zadejte klíč aplikace, který jste si poznamenali jako **tajný klíč klienta** aplikace QQ, kterou jste vytvořili dříve.
8. Klikněte na **OK** a potom kliknutím na **vytvořit** uložte konfiguraci QQ.

