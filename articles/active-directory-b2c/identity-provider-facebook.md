---
title: Nastavení registrace a přihlášení pomocí účtu na Facebooku
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí facebookových účtů ve vašich aplikacích pomocí Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188269"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Na Facebooku pomocí Služby Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Vytvoření aplikace pro Facebook

Pokud chcete použít facebookový účet jako [poskytovatele identity](authorization-code-flow.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ji zastupuje. Pokud ještě nemáte facebookový účet, můžete se [https://www.facebook.com/](https://www.facebook.com/)zaregistrovat na webu .

1. Přihlaste se na [Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji svého účtu na Facebooku.
1. Pokud jste tak ještě neučinili, musíte se zaregistrovat jako vývojář Facebooku. Chcete-li to provést, vyberte **Možnost Začínáme** v pravém horním rohu stránky, přijměte zásady Facebooku a proveďte kroky registrace.
1. Vyberte **Moje aplikace** a potom **Vytvořte aplikaci**.
1. Zadejte **zobrazované jméno** a platný **kontaktní e-mail**.
1. Vyberte **Vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **základní nastavení** > **.**
1. Zvolte **Category**například `Business and Pages`kategorii . Tato hodnota je vyžadována Facebook, ale nepoužívá pro Azure AD B2C.
1. V dolní části stránky vyberte **Přidat platformu**a pak vyberte **Web**.
1. Do pole Adresa `https://your-tenant-name.b2clogin.com/` URL `your-tenant-name` **webu**zadejte nahrazení názvem vašeho tenanta. Zadejte adresu URL **adresy URL zásad ochrany osobních údajů**, například `http://www.contoso.com`. Adresa URL zásad je stránka, kterou spravujete za účelem poskytnutí informací o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **Uložit změny**.
1. V horní části stránky zkopírujte hodnotu **ID aplikace**.
1. Vyberte **Zobrazit** a zkopírujte hodnotu **Tajné ho aplikace**. Oba se používají ke konfiguraci Facebooku jako poskytovatele identity ve vašem tenantovi. **Tajný klíč aplikace** je důležité pověření zabezpečení.
1. Vyberte znaménko plus vedle **položky PRODUKTY**a pak **v** části Facebook Přihlášení vyberte **Nastavit**.
1. V části **Facebook Přihlášení**vyberte **Nastavení**.
1. V platném rozhraní **URI přesměrování OAuth**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` se jménem svého tenanta. V dolní části stránky vyberte **Uložit změny.**
1. Pokud chcete, aby vaše facebooková aplikace byla dostupná pro Azure AD B2C, vyberte volič stavu v pravém horním rohu stránky a zapněte **ho,** aby byla aplikace veřejná, a pak vyberte **Přepnout režim**.  V tomto okamžiku stav by měl změnit z **rozvoje** na **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace facebookového účtu jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**, pak vyberte **Facebook**.
1. Zadejte **název**. Například *Facebook*.
1. Pro **ID klienta**zadejte ID aplikace aplikace Facebook, kterou jste vytvořili dříve.
1. U **tajného klíče klienta**zadejte tajný klíč aplikace, který jste nahráli.
1. Vyberte **Uložit**.
