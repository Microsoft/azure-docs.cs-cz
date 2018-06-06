---
title: Konfigurace Weibo v Azure Active Directory B2C | Microsoft Docs
description: Zadejte registrace a přihlášení k příjemce s Weibo účty v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c11931b0b1c568591a89277844722dceeac59039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711463"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s Weibo účty

> [!NOTE]
> Tato funkce je ve verzi Preview. Nepoužívejte tohoto zprostředkovatele identity v provozním prostředí.
> 

## <a name="create-a-weibo-application"></a>Vytvoření aplikace Weibo

Pokud chcete použít Weibo jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci Weibo a zadat se správné parametry. Potřebujete účet Weibo k tomu. Pokud nemáte, můžete jej získat v [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registrace pro Weibo programu pro vývojáře

1. Přejděte na [portál pro vývojáře Weibo](http://open.weibo.com/) a přihlaste se pomocí přihlašovacích údajů účtu Weibo.
2. Po přihlášení, klikněte na název zobrazení v pravém horním rohu.
3. V rozevírací nabídce, a vyberte**编辑开发者信息**(Upravit informace pro vývojáře).
4. Zadejte požadované informace do formuláře a klikněte na tlačítko**提交**(Odeslat).
5. Dokončete proces ověření e-mailu.
6. Přejděte na [stránky ověření identit](http://open.weibo.com/developers/identity/edit).
7. Zadejte požadované informace do formuláře a klikněte na tlačítko**提交**(Odeslat).

### <a name="register-a-weibo-application"></a>Registrace aplikace Weibo

1. Přejděte na [novou stránku registrace aplikace Weibo](http://open.weibo.com/apps/new).
2. Zadejte informace potřebné aplikace.
3. Klikněte na tlačítko**创建**(vytvořit).
4. Zkopírujte hodnoty z **klíč aplikace** a **tajný klíč aplikace**. Budete potřebovat později.
5. Nahrání požadované fotografie a zadejte nezbytné informace.
6. Klikněte na tlačítko**保存以上信息**(Uložit).
7. Klikněte na tlačítko**高级信息**(rozšířené informace).
8. Klikněte na tlačítko**编辑**(Upravit) vedle pole pro OAuth2.0**授权设置**(přesměrování URL adresy).
9. Zadejte `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` pro OAuth2.0**授权设置**(přesměrování URL adresy). Například pokud vaše `tenant_name` je contoso.onmicrosoft.com, nastavte adresu URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Klikněte na tlačítko**提交**(Odeslat).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Konfigurace Weibo jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Weibo".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Weibo**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity**
7. Zadejte **klíč aplikace** který jste zkopírovali dříve, jako **ID klienta**.
8. Zadejte **tajný klíč aplikace** který jste zkopírovali dříve, jako **tajný klíč klienta**.
9. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci Weibo.

