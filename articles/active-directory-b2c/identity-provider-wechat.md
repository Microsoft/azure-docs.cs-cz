---
title: Nastavení registrace a přihlášení pomocí účtu WeChat
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů WeChat ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184430"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu WeChat pomocí služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Vytvoření aplikace WeChat

Chcete-li použít účet WeChat jako poskytovatele identity ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve vašem tenantovi, která ji zastupuje. Pokud ještě nemáte účet WeChat, můžete získat informace [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)na adrese .

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přihlaste [https://open.weixin.qq.com/](https://open.weixin.qq.com/) se ke svým přihlašovacím údajům wechatu.
1. Vyberte **možnost Centrum** správy.
1. Podle pokynů zaregistrujte novou aplikaci.
1. `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` Zadejte **授权回调域** adresu URL pro zpětné volání. Pokud je například název klienta contoso, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`nastavte adresu URL na .
1. Zkopírujte **ID aplikace** a **klíč aplikace**. Budete je potřebovat k přidání zprostředkovatele identity do vašeho tenanta.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako poskytovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatele identity**, pak vyberte **WeChat (Náhled)**.
1. Zadejte **název**. Například *WeChat*.
1. Pro **ID klienta**zadejte ID aplikace aplikace WeChat, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte kód APP KEY, který jste zaznamenali.
1. Vyberte **Uložit**.
