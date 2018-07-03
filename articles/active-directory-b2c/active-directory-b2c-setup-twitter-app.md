---
title: Konfigurace Twitter pro Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Twitteru ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 6/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dad35f26496306558a6e0105db86321c497a8306
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342903"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Poskytují zaregistrujte se a přihlaste se k zákazníkům Twitteru účtů pomocí Azure AD B2C

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pokud chcete použít Twitteru jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvoření aplikace Twitter a zadejte správné parametry. Budete potřebovat k tomu účtu sítě Twitter. Pokud ho nemáte, můžete získat na [ https://twitter.com/signup ](https://twitter.com/signup).

1. Přejděte [Twitteru aplikace](https://apps.twitter.com/) a přihlaste se pomocí svých přihlašovacích údajů.
2. Klikněte na tlačítko **vytvořte novou aplikaci**.
3. Ve formuláři, zadejte hodnotu **název**, **popis**, a **webu**.
4. Pro **adresu URL zpětného volání**, zadejte `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Nezapomeňte nahradit **{klient}** s názvem (například contosob2c.onmicrosoft.com) vašeho tenanta a {policyId} se svým id zásad (například b2c_1_policy).  Toto zpětné volání adresy URL musí být v všechna písmena malá. Měli byste přidat adresu URL zpětného volání pro všechny zásady, které používají přihlášení Twitter. Ujistěte se, že používáte `b2clogin.com` místo ` login.microsoftonline.com` Pokud používáte ho ve vaší aplikaci.
5. Zaškrtnutím políčka souhlas **smlouva k vývojářským** a klikněte na tlačítko **vytvoření aplikace Twitter**.
6. Po vytvoření aplikace, vyberte v seznamu, vyberte **nastavení** kartu a potom klikněte na tlačítko **aktualizovat nastavení**.
7. Vyberte **klíče a přístupové tokeny** kartu.
8. Zkopírujte hodnotu **uživatelský klíč** a **uživatelský tajný klíč**. Budete potřebovat oba poskytovatelé konfigurace Twitteru jako zprostředkovatele identity ve vašem tenantovi.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C. 
2. Pokud chcete přepnout na svého tenanta Azure AD B2C, vyberte adresář Azure AD B2C v pravém horním rohu portálu.
3. Klikněte na tlačítko **všechny služby**a pak vyberte **Azure AD B2C** ze seznamu služeb v rámci **zabezpečení + Identita**.
4. Klikněte na tlačítko **zprostředkovatelé Identity**.
5. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Twitter".
6. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Twitteru (Preview)** a klikněte na tlačítko **OK**.
7. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte Twitteru **uživatelský klíč** pro **id klienta** a Twitter **uživatelský tajný klíč** pro **tajný kód klienta**.
8. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Twitter.

## <a name="next-steps"></a>Další postup

Vytvoření nebo úprava [integrované zásady](active-directory-b2c-reference-policies.md) a přidejte Twitteru jako zprostředkovatele identity.