---
title: Nastavení registrace a přihlášení pomocí účtu Facebook
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům služby pro registraci a přihlašování k aplikacím Facebook v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db6421167442e8aa3f0e7994dda35a8a5e475eb7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847573"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Facebook pomocí Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Vytvoření aplikace na Facebooku

Pokud chcete použít účet Facebook jako [poskytovatele identity](authorization-code-flow.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet Facebook, můžete se zaregistrovat na [https://www.facebook.com/](https://www.facebook.com/).

1. Přihlaste se ke [službě Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji k účtu Facebook.
1. Pokud jste to ještě neudělali, musíte se zaregistrovat jako vývojář Facebooku. Provedete to tak, **že vyberete Začínáme** v pravém horním rohu stránky, přijmete zásady Facebooku a dokončíte registrační kroky.
1. Vyberte **Moje aplikace** a pak **vytvořit aplikaci**.
1. Zadejte **Zobrazovaný název** a platný **kontaktní e-mail**.
1. Vyberte **vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **nastavení** > **Basic**.
1. Vyberte **kategorii**, například `Business and Pages`. Tuto hodnotu vyžaduje Facebook, ale nepoužívá se pro Azure AD B2C.
1. V dolní části stránky vyberte **Přidat platformu**a pak vyberte **Web**.
1. Do pole **Adresa URL webu**zadejte `https://your-tenant-name.b2clogin.com/` nahrazující `your-tenant-name` názvem vašeho tenanta. Zadejte adresu URL **zásad ochrany osobních údajů**, například `http://www.contoso.com`. Adresa URL zásad je stránka, kterou udržujete pro poskytování informací o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **Uložit změny**.
1. V horní části stránky zkopírujte hodnotu **ID aplikace**.
1. Vyberte **Zobrazit** a zkopírujte hodnotu **tajného kódu aplikace**. Pomocí obou z nich můžete nakonfigurovat Facebook jako poskytovatele identity ve vašem tenantovi. **Tajný klíč aplikace** je důležité bezpečnostní pověření.
1. Vyberte znaménko plus vedle položky **produkty**a potom vyberte možnost **nastavit** v části **přihlášení do Facebooku**.
1. V části **přihlášení na Facebooku**vyberte **Nastavení**.
1. V **platné identifikátory URI pro přesměrování OAuth**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` názvem vašeho tenanta. V dolní části stránky vyberte **Uložit změny** .
1. Chcete-li zpřístupnit aplikaci Facebook Azure AD B2C, vyberte selektor **stavu v pravém** horním rohu stránky a zapněte ji tak, aby byla aplikace veřejná, a pak vyberte **přepínač režim**.  V tomto okamžiku se stav změní z **vývoje** na **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace účtu Facebook jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Facebook**.
1. Zadejte **název**. Například *Facebook*.
1. Jako **ID klienta**zadejte ID aplikace Facebook, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný kód aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.
