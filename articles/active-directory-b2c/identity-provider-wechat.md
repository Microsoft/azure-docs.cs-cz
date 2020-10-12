---
title: Nastavení registrace a přihlášení pomocí účtu WeChat
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům WeChat ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462e33c836d8ca0a904e8f7b2e833dc7103311fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387894"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu WeChat pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Vytvoření aplikace v WeChat

Pokud chcete účet WeChat použít jako poskytovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet WeChat, můžete získat informace na adrese [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přihlaste se k [https://open.weixin.qq.com/](https://open.weixin.qq.com/) přihlašovacím údajům WeChat.
1. Vyberte **管理中心** (centrum pro správu).
1. Použijte postup k registraci nové aplikace.
1. Zadejte `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **授权回调域** (adresa URL zpětného volání). Například pokud je název vašeho tenanta contoso, nastavte adresu URL na `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Zkopírujte **ID aplikace** a **klíč App**. Budete je potřebovat pro přidání poskytovatele identity k vašemu tenantovi.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **WeChat (Preview)**.
1. Zadejte **název**. Například *WeChat*.
1. Pro **ID klienta**zadejte ID aplikace WeChat, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte klíč aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.
