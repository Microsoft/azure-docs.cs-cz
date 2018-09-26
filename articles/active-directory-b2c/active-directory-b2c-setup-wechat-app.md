---
title: Nastavení registrace a přihlášení s účtem WeChat pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty WeChat ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 45afd0d4c038123a1daf5f57de18e5f2799faa31
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181256"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem WeChat pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-wechat-application"></a>Vytvoření aplikace WeChat

Použít účet WeChat jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet WeChat, získáte informace v [ http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přihlaste se k [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) pomocí svých přihlašovacích údajů WeChat.
2. Vyberte**管理中心**(System center management).
3. Postupujte podle pokynů pro registraci nové aplikace.
4. Zadejte `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v**授权回调域**(adresu URL zpětného volání). Například pokud je název klienta contoso, nastavte adresu URL bude `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopírovat **ID aplikace** a **klíče aplikace**. Budete potřebovat tyto přidání zprostředkovatele identity do vašeho tenanta.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *WeChat*.
6. Vyberte **typ zprostředkovatele identit**vyberte **WeChat (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte klíč aplikace, který jste si poznamenali jako **tajný kód klienta** z WeChat aplikaci, kterou jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci WeChat.

