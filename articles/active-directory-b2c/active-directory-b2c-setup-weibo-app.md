---
title: Konfigurace Weibo v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty Weibo ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bfd7dde290bd040f8457e6d095fdf896e802764b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444785"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: Poskytněte uživatelům s účty Weibo registrace a přihlášení

> [!NOTE]
> Tato funkce je ve verzi Preview. Nepoužívejte v produkčním prostředí tohoto zprostředkovatele identity.
> 

## <a name="create-a-weibo-application"></a>Vytvoření aplikace Weibo

Použití Weibo jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci Weibo a zadejte správné parametry. Budete potřebovat účet Weibo provedete to tak. Pokud ho nemáte, můžete jej získat na [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Zaregistrujte se na programu pro vývojáře Weibo

1. Přejděte [portál pro vývojáře Weibo](http://open.weibo.com/) a přihlaste se pomocí přihlašovacích údajů k účtu Weibo.
2. Po přihlášení klikněte na své zobrazované jméno v pravém horním rohu.
3. V rozevíracím seznamu vyberte**编辑开发者信息**(Upravit informace pro vývojáře).
4. Zadejte požadované informace do formuláře a klikněte na tlačítko**提交**(Odeslat).
5. Abyste prošli procesem ověření e-mailu.
6. Přejděte [stránku ověření identity](http://open.weibo.com/developers/identity/edit).
7. Zadejte požadované informace do formuláře a klikněte na tlačítko**提交**(Odeslat).

### <a name="register-a-weibo-application"></a>Registrace aplikace Weibo

1. Přejděte [nové stránce registrace aplikace Weibo](http://open.weibo.com/apps/new).
2. Zadejte informace potřebné aplikace.
3. Klikněte na tlačítko**创建**(vytvořit).
4. Zkopírujte hodnoty **klíče aplikace** a **tajný kód aplikace**. Ho budete potřebovat později.
5. Nahrát povinné fotkám a zadejte potřebné informace.
6. Klikněte na tlačítko**保存以上信息**(Uložit).
7. Klikněte na tlačítko**高级信息**(rozšířené informace o).
8. Klikněte na tlačítko**编辑**(Upravit) vedle pole pro OAuth 2.0**授权设置**(přesměrování URL adresy).
9. Zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` pro OAuth 2.0**授权设置**(přesměrování URL adresy). Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL bude `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Klikněte na tlačítko**提交**(Odeslat).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Konfigurace Weibo jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Weibo".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Weibo**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Zadejte **klíče aplikace** , který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný kód aplikace** , který jste zkopírovali dříve, jako **tajný kód klienta**.
9. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Weibo.

