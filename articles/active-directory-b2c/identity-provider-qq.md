---
title: Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům QQ ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387979"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu QQ pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Vytvoření aplikace v QQ

Pokud chcete účet QQ použít jako poskytovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet QQ, můžete se zaregistrovat [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se do programu QQ Developer program

1. Přihlaste se k [portálu pro vývojáře QQ](http://open.qq.com) pomocí přihlašovacích údajů k účtu QQ.
1. Až se přihlásíte, pokračujte v [https://open.qq.com/reg](https://open.qq.com/reg) registraci jako vývojář.
1. Vyberte **个人** (individuální vývojář).
1. Zadejte požadované informace a vyberte **下一步** (další krok).
1. Dokončete proces ověření e-mailu. Až se zaregistrujete jako vývojář, budete muset počkat několik dní.

### <a name="register-a-qq-application"></a>Registrace aplikace QQ

1. Přejít na [https://connect.qq.com/index.html](https://connect.qq.com/index.html) .
1. Vyberte **应用管理** (Správa aplikací).
1. Vyberte **创建应用** (vytvořit aplikaci) a zadejte požadované informace.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` v **授权回调域** (adresa URL zpětného volání). Pokud máte například `tenant_name` Contoso, nastavte adresu URL na `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Vyberte **创建应用** (vytvořit aplikaci).
1. Na stránce potvrzení vyberte **应用管理** (Správa aplikací) a vraťte se na stránku správy aplikací.
1. U aplikace, kterou jste vytvořili, vyberte **查看** (Zobrazit).
1. Vyberte **修改** (Upravit).
1. Zkopírujte **ID aplikace** a **klíč App**. Obě tyto hodnoty budete potřebovat k přidání poskytovatele identity k vašemu tenantovi.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurace QQ jako zprostředkovatele identity

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **QQ (Preview)**.
1. Zadejte **název**. Například *QQ*.
1. Pro **ID klienta**zadejte ID aplikace QQ, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte klíč aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.
