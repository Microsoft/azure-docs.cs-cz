---
title: Nastavení registrace a přihlášení pomocí účtu Weibo
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Weibo ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 08aa7e4af6dc5d5e5bff470bc4c5d023e25b3014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387877"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Weibo pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Vytvoření aplikace v Weibo

Pokud chcete účet Weibo použít jako poskytovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet Weibo, můžete se zaregistrovat [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) .

1. Přihlaste se k [portálu pro vývojáře Weibo](https://open.weibo.com/) pomocí přihlašovacích údajů k účtu Weibo.
1. Po přihlášení vyberte své zobrazované jméno v pravém horním rohu.
1. V rozevíracím seznamu vyberte **编辑开发者信息** (upravit informace pro vývojáře).
1. Zadejte požadované informace a vyberte **提交** (Odeslat).
1. Dokončete proces ověření e-mailu.
1. Přejít na [stránku ověření identity](https://open.weibo.com/developers/identity/edit).
1. Zadejte požadované informace a vyberte **提交** (Odeslat).

### <a name="register-a-weibo-application"></a>Registrace aplikace Weibo

1. Přejít na [novou registrační stránku aplikace Weibo](https://open.weibo.com/apps/new)
1. Zadejte potřebné informace o aplikaci.
1. Vyberte **创建** (vytvořit).
1. Zkopírujte hodnoty **klíče aplikace** a **tajného klíče aplikace**. Obě tyto služby potřebujete k přidání poskytovatele identity k vašemu tenantovi.
1. Nahrajte požadované fotky a zadejte potřebné informace.
1. Vyberte **保存以上信息** (Uložit).
1. Vyberte **高级信息** (rozšířené informace).
1. Vyberte **编辑** (Upravit) vedle pole pro OAuth 2.0 **授权设置** (adresa URL pro přesměrování).
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` pro OAuth 2.0 **授权设置** (adresa URL pro přesměrování). Například pokud je název vašeho tenanta contoso, nastavte adresu URL na `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Vyberte **提交** (Odeslat).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurace účtu Weibo jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Weibo (Preview)**.
1. Zadejte **název**. Například *Weibo*.
1. Pro **ID klienta**zadejte klíč aplikace aplikace Weibo, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný kód aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.
