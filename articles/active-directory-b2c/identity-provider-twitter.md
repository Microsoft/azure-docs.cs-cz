---
title: Nastavení registrace a přihlášení pomocí účtu na Twitteru
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů Twitter ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051466"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Twitter pomocí služby Azure Active Directory B2C

## <a name="create-an-application"></a>Vytvoření aplikace

Chcete-li používat Twitter jako poskytovatele identity v Azure AD B2C, musíte vytvořit aplikaci Twitter. Pokud ještě nemáte účet na Twitteru, můžete se [https://twitter.com/signup](https://twitter.com/signup)zaregistrovat na adrese .

1. Přihlaste se na web [Twitter Developers](https://developer.twitter.com/en/apps) pomocí přihlašovacích údajů k účtu Twitter.
1. Vyberte **Vytvořit aplikaci**.
1. Zadejte **název aplikace** a popis **aplikace**.
1. Do adresy URL `https://your-tenant.b2clogin.com` **webu**zadejte . Nahraďte `your-tenant` se jménem svého tenanta. Například, `https://contosob2c.b2clogin.com`.
1. Adresu **URL zpětného** `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`volání zadejte . Nahraďte `your-tenant` název vašeho klienta a `your-user-flow-Id` identifikátorem toku uživatele. Například, `b2c_1A_signup_signin_twitter`. Musíte použít všechna malá písmena při zadávání názvu klienta a id toku uživatele i v případě, že jsou definovány s velkými písmeny v Azure AD B2C.
1. V dolní části stránky si přečtěte a přijměte podmínky a pak vyberte **Vytvořit**.
1. Na stránce **Podrobnosti o aplikaci** vyberte **Upravit > podrobnosti ,** zaškrtněte políčko **Povolit přihlášení pomocí Twitteru**a pak vyberte **Uložit**.
1. Vyberte **klíče a tokeny** a zaznamenejte **klíč rozhraní API příjemce** a hodnoty **tajných klíčů rozhraní API příjemce,** které se použijí později.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako poskytovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatele identity**, pak vyberte **Twitter**.
1. Zadejte **název**. Například *Twitter*.
1. Pro **ID klienta**zadejte klíč rozhraní API příjemce aplikace Twitter, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč rozhraní API příjemce, který jste zaznamenali.
1. Vyberte **Uložit**.
