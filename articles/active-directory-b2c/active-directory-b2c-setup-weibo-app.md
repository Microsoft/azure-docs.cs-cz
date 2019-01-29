---
title: Nastavení registrace a přihlášení s účtem Weibo pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Weibo ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: db43487dd9f0f424456fba0f57593b36de031327
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165945"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem Weibo pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-weibo-application"></a>Vytvoření aplikace Weibo

Použít účet Weibo jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Weibo, získáte ji na [ https://weibo.com/signup/signup.php?lang=en-us ](https://weibo.com/signup/signup.php?lang=en-us).

1. Přihlaste se k [portál pro vývojáře Weibo](https://open.weibo.com/) pomocí svých přihlašovacích údajů účtu Weibo.
2. Po přihlášení vyberte své zobrazované jméno v pravém horním rohu.
3. V rozevíracím seznamu vyberte**编辑开发者信息**(Upravit informace pro vývojáře).
4. Zadejte požadované informace a vyberte**提交**(Odeslat).
5. Abyste prošli procesem ověření e-mailu.
6. Přejděte [stránku ověření identity](https://open.weibo.com/developers/identity/edit).
7. Zadejte požadované informace a vyberte**提交**(Odeslat).

### <a name="register-a-weibo-application"></a>Registrace aplikace Weibo

1. Přejděte [nové stránce registrace aplikace Weibo](https://open.weibo.com/apps/new).
2. Zadejte informace potřebné aplikace.
3. Vyberte**创建**(vytvořit).
4. Zkopírujte hodnoty **klíče aplikace** a **tajný kód aplikace**. Obě tyto přidat zprostředkovatele identity do svého tenanta potřebujete.
5. Nahrát povinné fotkám a zadejte potřebné informace.
6. Vyberte**保存以上信息**(Uložit).
7. Vyberte**高级信息**(rozšířené informace o).
8. Vyberte**编辑**(Upravit) vedle pole pro OAuth 2.0**授权设置**(přesměrování URL adresy).
9. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` pro OAuth 2.0**授权设置**(přesměrování URL adresy). Například pokud je název klienta contoso, nastavte adresu URL bude `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Vyberte**提交**(Odeslat).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurace účtu Weibo jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Weibo*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Weibo (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte klíč aplikace, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód aplikace, které jste si poznamenali jako **tajný kód klienta** z Weibo aplikaci, kterou jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Weibo.
