---
title: 'Azure Active Directory B2C: Konfigurace QQ | Microsoft Docs'
description: Zadejte registrace a přihlášení k příjemce s QQ účty v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.openlocfilehash: 12daed5f90e2b073a7520c061f18761baaed5c03
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s QQ účty

> [!NOTE]
> Tato funkce je ve verzi preview.
> 

## <a name="create-a-qq-application"></a>Vytvoření aplikace QQ

Pokud chcete použít QQ jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci QQ a zadat se správné parametry. Potřebujete účet QQ k tomu. Pokud nemáte, můžete jej získat v [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrace pro QQ programu pro vývojáře

1. Přejděte na [portál pro vývojáře QQ](http://open.qq.com) a přihlaste se pomocí přihlašovacích údajů účtu QQ.
2. Po přihlášení, přejděte na [ http://open.qq.com/reg ](http://open.qq.com/reg) sami zaregistrovat jako vývojář.
3. V nabídce vyberte**个人**(jednotlivé vývojáře).
4. Zadejte požadované informace do formuláře a klikněte na tlačítko**下一步**(Další krok).
5. Dokončete proces ověření e-mailu.

> [!NOTE]
> Musíte se za několik dní po registraci jako vývojář schválení. 

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejděte do [ (Nastavení)https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Integrace a služby).
2. Klikněte na**应用管理**(Správa aplikací).
3. Klikněte na**创建应用**(vytvořit aplikaci).
4. Zadejte informace potřebné aplikace.
5. Klikněte na**创建应用**(vytvořit aplikaci).
6. Zadejte požadované informace.
7. Pro**授权回调域**(zpětného volání URL) zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Klikněte na**创建应用**(vytvořit aplikaci).
9. Na stránce pro potvrzení klikněte na**应用管理**(Správa aplikací) se vrátíte na stránku správy aplikace.
10. Klikněte na**查看**(Zobrazit) vedle aplikace, kterou jste právě vytvořili.
11. Klikněte na**修改**(Upravit).
12. Z horní části stránky, zkopírujte **ID aplikace** a **klíč aplikace**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Konfigurace QQ jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "QQ".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **QQ**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity**
7. Zadejte **klíč aplikace** který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný klíč aplikace** který jste zkopírovali dříve, jako **tajný klíč klienta**.
9. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci QQ.

