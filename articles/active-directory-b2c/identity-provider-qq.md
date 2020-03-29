---
title: Nastavení registrace a přihlášení pomocí účtu QQ pomocí služby Azure Active Directory B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů QQ ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187983"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu QQ pomocí služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Vytvoření aplikace QQ

Chcete-li použít účet QQ jako zprostředkovatele identity ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve vašem tenantovi, která ji zastupuje. Pokud ještě nemáte účet QQ, můžete se zaregistrovat na adrese [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zaregistrujte se do vývojářského programu QQ

1. Přihlaste se k [portálu pro vývojáře QQ](http://open.qq.com) pomocí přihlašovacích údajů k účtu QQ.
1. Po přihlášení se [https://open.qq.com/reg](https://open.qq.com/reg) můžete zaregistrovat jako vývojář.
1. Vyberte **jednotlivé vývojáře.**
1. Zadejte požadované informace a zvolte **možnost Další** krok.
1. Dokončete proces ověření e-mailu. Budete muset počkat několik dní, které mají být schváleny po registraci jako vývojář.

### <a name="register-a-qq-application"></a>Registrace qq aplikace

1. Přejděte [https://connect.qq.com/index.html](https://connect.qq.com/index.html)na .
1. Vyberte **možnost Správa** aplikací.
1. Vyberte **možnost Vytvoření aplikace** a zadejte požadované informace.
1. `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` Zadejte **授权回调域** adresu URL pro zpětné volání. Pokud je například vaše `tenant_name` adresa contoso, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`nastavte adresu URL na .
1. Vyberte možnost Vytvoření **aplikace.**
1. Na stránce s potvrzením vyberte **možnost** Správa aplikací a vraťte se na stránku pro správu aplikací.
1. Vedle **查看** aplikace, kterou jste vytvořili, vyberte zobrazit.
1. Vyberte **možnost "upravit).**
1. Zkopírujte **ID aplikace** a **klíč aplikace**. K přidání zprostředkovatele identity do tenanta potřebujete obě tyto hodnoty.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurace qq jako zprostředkovatele identity

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatele identity**a pak vyberte **QQ (Náhled).**
1. Zadejte **název**. Například *QQ*.
1. Pro **ID klienta**zadejte ID aplikace aplikace QQ, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte kód APP KEY, který jste zaznamenali.
1. Vyberte **Uložit**.
