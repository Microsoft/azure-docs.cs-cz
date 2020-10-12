---
title: Nastavení registrace a přihlášení pomocí účtu Twitteru
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Twitteru ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e07be01a0fb6d74b4dcef5cbc6ec129f95fd2e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387928"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Twitteru pomocí Azure Active Directory B2C

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete jako poskytovatele identity v Azure AD B2C používat Twitter, musíte vytvořit aplikaci Twitter. Pokud ještě nemáte účet na Twitteru, můžete se zaregistrovat [https://twitter.com/signup](https://twitter.com/signup) .

1. Přihlaste se k webu pro [vývojáře na Twitteru](https://developer.twitter.com/en/apps) pomocí přihlašovacích údajů k účtu Twitteru.
1. Vyberte  **vytvořit aplikaci**.
1. Zadejte **název aplikace** a **Popis aplikace**.
1. Do **adresy URL webu**zadejte `https://your-tenant.b2clogin.com` . Nahraďte `your-tenant` názvem vašeho tenanta. Například, `https://contosob2c.b2clogin.com`.
1. Jako **adresu URL zpětného volání**zadejte `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Nahraďte `your-tenant` názvem vašeho tenanta a `your-user-flow-Id` identifikátorem toku uživatele. Například, `b2c_1A_signup_signin_twitter`. Při zadávání názvu tenanta a ID toku uživatele je potřeba použít malá písmena, i když jsou v Azure AD B2C definované velkými písmeny.
1. V dolní části stránky si přečtěte a přijměte podmínky a pak vyberte **vytvořit**.
1. Na stránce **podrobností aplikace** vyberte **Upravit > upravit podrobnosti**, zaškrtněte políčko **Povolit přihlášení pomocí Twitteru**a potom vyberte **Uložit**.
1. Vyberte **klíče a tokeny** a zaznamenejte **klíč rozhraní API příjemce** a hodnoty **tajného klíče rozhraní API příjemce** , které chcete použít později.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako poskytovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Twitter**.
1. Zadejte **název**. Například *Twitter*.
1. Pro **ID klienta**zadejte klíč rozhraní API příjemce aplikace Twitter, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč rozhraní API příjemce, který jste si poznamenali.
1. Vyberte **Uložit**.
