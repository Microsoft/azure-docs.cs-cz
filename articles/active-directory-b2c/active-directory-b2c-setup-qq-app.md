---
title: Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům QQ ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 55e4b04814daaaff5bc217a561e9045d313d9675
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811421"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Vytvoření aplikace v QQ

Pokud chcete v Azure Active Directory (Azure AD) B2C použít účet QQ jako zprostředkovatele identity, musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet QQ, můžete se zaregistrovat [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se do programu QQ Developer program

1. Přihlaste se k [portálu pro vývojáře QQ](http://open.qq.com) pomocí přihlašovacích údajů k účtu QQ.
1. Až se přihlásíte, [https://open.qq.com/reg](https://open.qq.com/reg) pokračujte v registraci jako vývojář.
1. Vyberte**个人**(individuální vývojář).
1. Zadejte požadované informace a vyberte**下一步**(další krok).
1. Dokončete proces ověření e-mailu. Až se zaregistrujete jako vývojář, budete muset počkat několik dní.

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejděte do [ (Nastavení)https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Integrace a služby).
1. Vyberte**应用管理**(Správa aplikací).
1. Vyberte**创建应用**(vytvořit aplikaci) a zadejte požadované informace.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` v**授权回调域**(adresa URL zpětného volání). Pokud máte `tenant_name` například contoso, nastavte adresu URL `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`na.
1. Vyberte**创建应用**(vytvořit aplikaci).
1. Na stránce potvrzení vyberte**应用管理**(Správa aplikací) a vraťte se na stránku správy aplikací.
1. U aplikace, kterou jste vytvořili, vyberte**查看**(Zobrazit).
1. Vyberte**修改**(Upravit).
1. Zkopírujte **ID aplikace** a **klíč App**. Obě tyto hodnoty budete potřebovat k přidání poskytovatele identity k vašemu tenantovi.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurace QQ jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **QQ (Preview)** .
1. Zadejte **název**. Například *QQ*.
1. Pro **ID klienta**zadejte ID aplikace QQ, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte klíč aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.
