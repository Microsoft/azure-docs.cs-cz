---
title: WeChat konfigurace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty WeChat ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a18d41a4f45b147790a17664156659d282e710d4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445931"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: Poskytněte uživatelům s účty WeChat registrace a přihlášení

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-wechat-application"></a>Vytvoření aplikace WeChat

WeChat používat jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci WeChat a zadejte správné parametry. WeChat účtu k tomu potřebujete. Pokud ho nemáte, můžete jej získat registrací prostřednictvím jednoho z jejich mobilních aplikací nebo pomocí svého QQ čísla. Poté založte si účet zaregistrovaný s WeChat programu pro vývojáře. Další informace najdete [tady](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přejděte na adresu [https://open.weixin.qq.com/](https://open.weixin.qq.com/) a přihlaste se.
2. Klikněte na**管理中心**(System center management).
3. Postupujte podle kroků nezbytných k registraci nové aplikace.
4. Pro**授权回调域**(adresu URL zpětného volání), zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL bude `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Najít a zkopírovat **ID aplikace** a **klíče aplikace**. Budete potřebovat později.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "WeChat".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **WeChat**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Zadejte **klíče aplikace** , který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný kód aplikace** , který jste zkopírovali dříve, jako **tajný kód klienta**.
9. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci WeChat.

