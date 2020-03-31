---
title: Nastavení registrace a přihlášení pomocí účtu Weibo
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů Weibo ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187895"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Weibo pomocí služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Vytvoření aplikace Weibo

Chcete-li použít účet Weibo jako poskytovatele identity ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve vašem tenantovi, která ji zastupuje. Pokud ještě nemáte účet Weibo, můžete se zaregistrovat na adrese [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Přihlaste se k [vývojářskému portálu Weibo](https://open.weibo.com/) pomocí přihlašovacích údajů k účtu Weibo.
1. Po přihlášení vyberte zobrazované jméno v pravém horním rohu.
1. V rozevíracím panelu vyberte možnost **"upravit** informace o vývojáři).
1. Zadejte požadované informace a zvolte **možnost "odeslat).**
1. Dokončete proces ověření e-mailu.
1. Přejděte na [stránku ověření identity](https://open.weibo.com/developers/identity/edit).
1. Zadejte požadované informace a zvolte **možnost "odeslat).**

### <a name="register-a-weibo-application"></a>Registrace aplikace Weibo

1. Přejděte na [novou stránku registrace aplikace Weibo](https://open.weibo.com/apps/new).
1. Zadejte potřebné informace o aplikaci.
1. Vyberte **možnost "vytvořit).**
1. Zkopírujte hodnoty **klíče aplikace** a **tajného klíče aplikace**. Potřebujete oba tyto přidat zprostředkovatele identity do vašeho tenanta.
1. Nahrajte požadované fotografie a zadejte potřebné informace.
1. Vyberte **možnost 中中中中中**中 (uložit).
1. Vyberte **možnost "Upřesnit informace).**
1. Vyberte **možnost "upravit)** vedle pole pro OAuth2.0**中中中**(adresa URL přesměrování).
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` OAuth2.0**中中中中**(přesměrování URL). Pokud je například název klienta contoso, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`nastavte adresu URL na .
1. Vyberte **možnost "odeslat).**

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurace účtu Weibo jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**, pak vyberte **Weibo (Náhled)**.
1. Zadejte **název**. Například *Weibo*.
1. Pro **ID klienta**zadejte klíč aplikace aplikace Weibo, kterou jste vytvořili dříve.
1. U **tajného klíče klienta**zadejte tajný klíč aplikace, který jste nahráli.
1. Vyberte **Uložit**.
