---
title: Registrace aplikace v SaaS – Azure Marketplace
description: Naučte se používat Azure Portal k registraci aplikace SaaS a získání tokenu zabezpečení Azure Active Directory.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: dsindona
ms.openlocfilehash: 0201ea7b207b7d4c0eaa56de1ee062ea405f0bbb
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119235"
---
# <a name="register-a-saas-application"></a>Registrace aplikace SaaS

Tento článek vysvětluje, jak zaregistrovat aplikaci SaaS pomocí Microsoft [Azure Portal](https://portal.azure.com/) a jak získat přístupový token vydavatele (Azure Active Directory přístupového tokenu). Vydavatel použije tento token k ověření aplikace SaaS voláním rozhraní API pro plnění SaaS.  Rozhraní API pro splnění používají pověření klienta OAuth 2,0 k udělení toku v koncových bodech Azure Active Directory (v 1.0) k vytvoření žádosti o přístupový token služby na službu.

Azure Marketplace neobsahují žádná omezení pro metodu ověřování, kterou služba SaaS používá pro koncové uživatele. Následující tok se vyžaduje jenom k ověření služby SaaS v Azure Marketplace.

Další informace o službě Azure AD (Active Directory) najdete v tématu [co je ověřování](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)?.

## <a name="register-an-azure-ad-secured-app"></a>Registrace aplikace zabezpečené službou Azure AD

Všechny aplikace, které chtějí využívat možnosti Azure AD, musí být nejdřív zaregistrované v tenantovi Azure AD. Tento proces registrace zahrnuje poskytnutí některých podrobností o vaší aplikaci Azure AD. Chcete-li zaregistrovat novou aplikaci pomocí Azure Portal, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Pokud vám váš účet poskytne přístup k více než jednomu, klikněte na svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně klikněte na službu **Azure Active Directory** , klikněte na **Registrace aplikací**a pak klikněte na **Nová registrace aplikace**.

    ![Registrace aplikací AD SaaS](./media/saas-offer-app-registration-v1.png)

4. Na stránce vytvořit zadejte \' informace o registraci aplikace:
    -   **Název**: zadejte smysluplný název aplikace.
    -   **Typ aplikace**:  
        
        Vyberte **Webová aplikace/rozhraní API** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) a [aplikace prostředků/rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) , které jsou nainstalované na zabezpečeném serveru. Toto nastavení se používá u důvěrných [webových klientů](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth a [klientů založených na agentech veřejného uživatele](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Stejná aplikace může také zpřístupnit klienta i prostředek / rozhraní API.

        Konkrétní příklady webových aplikací najdete v průvodci rychlým startem, které jsou k dispozici [v části Začínáme](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) v [příručce pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/).

5. Po dokončení klikněte na **zaregistrovat**.  Azure AD přiřadí nové aplikaci jedinečné *ID aplikace* . Doporučujeme zaregistrovat jednu aplikaci, která přistupuje pouze k rozhraní API a jako jeden tenant.

6. Pokud chcete vytvořit tajný klíč klienta, přejděte na **stránku certifikáty & tajných** kódů a klikněte na **+ nový tajný klíč klienta**.  Nezapomeňte zkopírovat tajnou hodnotu a použít ji ve svém kódu.

**ID aplikace Azure AD** je přidruženo k vašemu ID vydavatele, takže se ujistěte, že se stejné *ID aplikace* používá ve všech nabídkách.

>[!Note]
>Pokud má Vydavatel v partnerském centru dva různé účty, měli byste použít dvě odlišná ID aplikací Azure AD.  Každý partnerský účet v partnerském centru by měl používat jedinečné ID aplikace Azure AD pro všechny nabídky SaaS, které jsou publikovány prostřednictvím tohoto účtu.

## <a name="how-to-get-the-publishers-authorization-token"></a>Jak získat autorizační token vydavatele

Po zaregistrování aplikace můžete programově požádat o autorizační token vydavatele (přístupový token Azure AD v1 pomocí koncového bodu Azure AD V1). Vydavatel musí tento token použít při volání různých rozhraní API pro plnění SaaS. Tento token je platný jenom pro jednu hodinu. 

Další informace o těchto tokenech naleznete v tématu [Azure Active Directory Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).  Všimněte si, že v toku níže je použit token koncového bodu v1.

### <a name="get-the-token-with-an-http-post"></a>Získání tokenu pomocí HTTP POST

#### <a name="http-method"></a>HTTP – metoda

Příspěvek<br>

##### <a name="request-url"></a>*Adresa URL požadavku* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*Parametr URI*

|  Název parametru    |  Povinné         |  Popis |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  ID tenanta registrované aplikace AAD |

##### <a name="request-header"></a>*Hlavička žádosti*

|  Název hlavičky       |  Povinné         |  Popis |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  Typ obsahu přidružený k žádosti Výchozí hodnota je `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Text žádosti*

|  Název vlastnosti     |  Povinné         |  Popis |
|  ---------------   |  ---------------  | ------------ |
|  `grant-type`      |  True      |  Typ udělení Použijte `"client_credentials"`. |
|  `client_id`       |  True      |  Identifikátor klienta nebo aplikace přidružený k aplikaci Azure AD |
|  `client_secret`   |  True      |  Tajný kód přidružený k aplikaci Azure AD. |
|  `resource`        |  True      |  Cílový prostředek, pro který je požadován token. Použijte, `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` protože rozhraní SaaS API pro Marketplace je v tomto případě vždy cílovým prostředkem. |

##### <a name="response"></a>*Základě*

|  Name     |  Typ         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  Požadavek byl úspěšný. |

##### <a name="tokenresponse"></a>*TokenResponse*

Ukázková odpověď:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`Hodnota pole v odpovědi je ta, kterou `<access_token>` předáte jako parametr Authorization při volání všech rozhraní API pro SaaS a vyřizování měření na webu Marketplace.

## <a name="next-steps"></a>Další kroky

Vaše aplikace zabezpečená pro Azure AD teď může používat [rozhraní API pro plnění SaaS verze 2](./pc-saas-fulfillment-api-v2.md).
