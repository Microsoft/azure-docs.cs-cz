---
title: Registrace aplikace SaaS | Azure Marketplace
description: Vysvětluje, jak zaregistrovat aplikaci SaaS pomocí Azure Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275709"
---
# <a name="register-a-saas-application"></a>Registrace aplikace SaaS

Tento článek vysvětluje, jak zaregistrovat aplikaci SaaS pomocí Microsoft [Azure Portal](https://portal.azure.com/).  Po úspěšné registraci se zobrazí token zabezpečení Azure Active Directory (Azure AD), který můžete použít pro přístup k rozhraním API pro plnění SaaS.  Další informace o Azure AD najdete v tématu [co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Tok ověřování služby – služba

Následující diagram znázorňuje tok předplatného pro nového zákazníka a při použití těchto rozhraní API:

![Tok rozhraní API nabídky SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure neposkytuje žádná omezení pro ověřování, které služba SaaS zpřístupňuje koncovým uživatelům. Ověřování s rozhraními API pro plnění SaaS se ale provádí pomocí tokenu zabezpečení Azure AD, který se obvykle získá registrací aplikace SaaS prostřednictvím Azure Portal. 


## <a name="register-an-azure-ad-secured-app"></a>Registrace aplikace zabezpečené službou Azure AD

Všechny aplikace, které chtějí využívat možnosti Azure AD, musí být nejdřív zaregistrované v tenantovi Azure AD. Tento proces registrace zahrnuje poskytnutí podrobností o vaší aplikaci Azure AD, jako je adresa URL, kde se nachází, adresa URL pro odeslání odpovědí po ověření uživatele, identifikátor URI, který identifikuje aplikaci atd.  Chcete-li zaregistrovat novou aplikaci pomocí Azure Portal, proveďte následující kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Pokud vám váš účet poskytne přístup k více než jednomu, klikněte na svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3.  V levém navigačním podokně klikněte na službu **Azure Active Directory** , klikněte na **Registrace aplikací**a pak klikněte na **Nová registrace aplikace**.

    ![Registrace aplikací AD SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na stránce vytvořit zadejte informace o registraci aplikace\':
    -   **Název**: zadejte smysluplný název aplikace.
    -   **Typ aplikace**: 
        - Vyberte **Nativní** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), které jsou nainstalované místně na zařízení. Toto nastavení se používá pro veřejné [nativní klienty](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth.
        - Vyberte **Webová aplikace/rozhraní API** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) a [aplikace prostředků/rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) , které jsou nainstalované na zabezpečeném serveru. Toto nastavení se používá u důvěrných [webových klientů](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth a [klientů založených na agentech veřejného uživatele](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Stejná aplikace může také zpřístupnit klienta i prostředek / rozhraní API.
    -   **Přihlašovací adresa URL**: pro aplikace webové aplikace nebo rozhraní API zadejte základní adresu URL vaší aplikace. **http://localhost:31544** Může to být například adresa URL webové aplikace běžící na vašem místním počítači. Uživatelé pak použijí tuto adresu URL pro přihlášení k aplikaci webového klienta.
    -   **Identifikátor URI pro přesměrování**: u nativních aplikací zadejte identifikátor URI, který služba Azure AD používá k vrácení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci, například **http://MyFirstAADApp**.

        ![Registrace aplikací AD SaaS](./media/saas-offer-app-registration-v1-2.png)

        Konkrétní příklady webových aplikací nebo nativních aplikací najdete v průvodci rychlým startem, které jsou k dispozici *v části Začínáme* v [příručce pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Jakmile budete hotovi, klikněte na **Vytvořit**. Azure AD přiřadí vaší aplikaci jedinečné *ID aplikace* a\'znovu přejdete na hlavní registrační stránku aplikace\'. V závislosti na tom, jestli je vaše aplikace webová nebo nativní, jsou k dispozici různé volby pro přidání dalších možností do vaší aplikace.

>[!Note]
>Ve výchozím nastavení je nově registrovaná aplikace nakonfigurovaná tak, aby umožňovala pouze uživatelům ze stejného tenanta přihlašovat se k vaší aplikaci.


## <a name="using-the-azure-ad-security-token"></a>Použití tokenu zabezpečení Azure AD

Po zaregistrování aplikace můžete programově požádat o token zabezpečení Azure AD.  Očekává se, že vydavatel použije tento token, a vytvoří požadavek na jeho vyřešení.  Při použití různých rozhraní API pro splnění je parametr dotazu tokenu v adrese URL, když se uživatel přesměruje na web SaaS z Azure.  Tento token je platný jenom pro jednu hodinu.  Kromě toho by měla adresa URL dekódovat hodnotu tokenu z prohlížeče, aby ji bylo možné použít.

Další informace o těchto tokenech naleznete v tématu [Azure Active Directory Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Získání tokenu založeného na aplikaci Azure AD

HTTP – metoda

`POST`

*Adresa URL požadavku*

**https://login.microsoftonline.com/*{tenantId}*/OAuth2/token**

*Parametr URI*

|  **Název parametru**  | **Požadováno**  | **Popis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID tenanta registrované aplikace AAD   |
|  |  |  |


*Hlavička požadavku*

|  **Název hlavičky**  | **Požadováno** |  **Popis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Typ obsahu     | True         | Typ obsahu přidružený k žádosti Výchozí hodnota je `application/x-www-form-urlencoded`.  |
|  |  |  |


*Text požadavku*

| **Název vlastnosti**   | **Požadováno** |  **Popis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ udělení Výchozí hodnota je `client_credentials`.                    |
|  Client_id          | True         |  Identifikátor klienta nebo aplikace přidružený k aplikaci Azure AD                  |
|  client_secret      | True         |  Heslo přidružené k aplikaci Azure AD.                               |
|  Prostředek           | True         |  Cílový prostředek, pro který je požadován token. Výchozí hodnota je `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Základě*

|  **Název**  | **Typ**       |  **Popis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Požadavek byl úspěšný   |
|  |  |  |

*TokenResponse*

Vzorový token odpovědi:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Další kroky

Vaše aplikace zabezpečená pro Azure AD teď může používat [rozhraní API pro plnění SaaS verze 2](./pc-saas-fulfillment-api-v2.md).
