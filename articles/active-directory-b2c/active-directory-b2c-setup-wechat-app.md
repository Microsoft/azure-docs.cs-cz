---
title: Konfigurace WeChat v Azure Active Directory B2C | Microsoft Docs
description: Zadejte registrace a přihlášení k příjemce s WeChat účty v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bbdeccbdd0d6786fdf32fc2f547344b379bd0d7c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712483"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s WeChat účty

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-wechat-application"></a>Vytvoření aplikace WeChat

Pokud chcete použít WeChat jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci WeChat a zadat se správné parametry. Potřebujete účet WeChat k tomu. Pokud nemáte, můžete jej získat registrací prostřednictvím jednoho z jejich mobilních aplikací nebo pomocí své QQ číslo. Potom získáte váš účet zaregistrována WeChat programu pro vývojáře. Další informace můžete najít [zde](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přejděte na adresu [https://open.weixin.qq.com/](https://open.weixin.qq.com/) a přihlaste se.
2. Klikněte na**管理中心**(management center).
3. Postupujte podle potřeby kroky pro registraci novou aplikaci.
4. Pro**授权回调域**(zpětného volání URL), zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Najít a zkopírovat **ID aplikace** a **klíč aplikace**. Budete potřebovat později.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "WeChat".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **WeChat**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity**
7. Zadejte **klíč aplikace** který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný klíč aplikace** který jste zkopírovali dříve, jako **tajný klíč klienta**.
9. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci WeChat.

