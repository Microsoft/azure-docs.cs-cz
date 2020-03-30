---
title: Zaregistrovat žádost SaaS | Azure Marketplace
description: Vysvětluje, jak zaregistrovat aplikaci SaaS pomocí portálu Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275709"
---
# <a name="register-a-saas-application"></a>Registrace aplikace SaaS

Tento článek vysvětluje, jak zaregistrovat aplikaci SaaS pomocí [portálu](https://portal.azure.com/)Microsoft Azure .  Po úspěšné registraci obdržíte token zabezpečení Azure Active Directory (Azure AD), který můžete použít pro přístup k rozhraní API plnění SaaS.  Další informace o Azure AD najdete v tématu [Co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Tok ověřování mezi službami

Následující diagram znázorňuje tok předplatného nového zákazníka a při použití těchto api:

![SaaS nabízí tok API](./media/saas-offer-publish-api-flow-v1.png)

Azure neukládá žádná omezení na ověřování, které služba SaaS zveřejňuje svým koncovým uživatelům. Ověřování pomocí saas plnění API se však provádí pomocí tokenu zabezpečení Azure AD, obvykle získané registrací aplikace SaaS prostřednictvím portálu Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registrace aplikace zabezpečené azure ad

Všechny aplikace, které chtějí využívat možnosti Azure AD, musí být nejdřív zaregistrované v tenantovi Azure AD. Tento proces registrace zahrnuje poskytnutí podrobností o vaší aplikaci ve službě Azure AD, jako je adresa URL, kde se nachází, adresa URL pro odesílání odpovědí po ověření uživatele, identifikátor URI, který identifikuje aplikaci a tak dále.  Chcete-li zaregistrovat novou aplikaci pomocí portálu Azure, proveďte následující kroky:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com/).
2.  Pokud váš účet umožňuje přístup k více než jednomu, klikněte na svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaný klient Azure AD.
3.  V levém navigačním podokně klikněte na službu **Azure Active Directory,** klikněte na **Registrace aplikací**a klikněte na Nová **registrace aplikace**.

    ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration-v1.png)

4.  Na stránce Vytvořit zadejte\'registrační informace o aplikaci:
    -   **Název**: Zadejte smysluplný název aplikace.
    -   **Typ aplikace**: 
        - Vyberte **Nativní** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), které jsou nainstalované místně na zařízení. Toto nastavení se používá pro veřejné [nativní klienty](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth.
        - Vyberte **Webová aplikace / ROZHRANÍ API** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) a aplikace [prostředků/rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) nainstalované na zabezpečeném serveru. Toto nastavení se používá pro [důvěrné webové klienty](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth a veřejné [klienty založené na uživatelském agentovi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Stejná aplikace může také zpřístupnit klienta i prostředek / rozhraní API.
    -   **Přihlašovací adresa URL**: Pro webové aplikace/aplikace API zadejte základní adresu URL aplikace. **http://localhost:31544** Může to být například adresa URL webové aplikace spuštěné v místním počítači. Uživatelé by pak použít tuto adresu URL pro přihlášení k webové klientské aplikace.
    -   **Identifikátor URI přesměrování**: Pro nativní aplikace zadejte identifikátor URI používaný službou Azure AD k vrácení odpovědí tokenů. Zadejte hodnotu specifickou pro **http://MyFirstAADApp**vaši aplikaci, například .

        ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration-v1-2.png)

        Konkrétní příklady pro webové aplikace nebo nativní aplikace najdete v nastavení s asistencí quickstart, které jsou k dispozici v části *Začínáme* v [Průvodci vývojáři Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Jakmile budete hotovi, klikněte na **Vytvořit**. Azure AD přiřadí jedinečné *ID aplikace* do\'vaší aplikace\'a přejdete na hlavní registrační stránku aplikace. V závislosti na tom, jestli je vaše aplikace webová nebo nativní, jsou k dispozici různé volby pro přidání dalších možností do vaší aplikace.

>[!Note]
>Ve výchozím nastavení je nově registrovaná aplikace nakonfigurována tak, aby umožňovala přihlášení k vaší aplikaci pouze uživatelům ze stejného klienta.


## <a name="using-the-azure-ad-security-token"></a>Použití tokenu zabezpečení Azure AD

Jakmile zaregistrujete aplikaci, můžete programově požádat o token zabezpečení Azure AD.  Očekává se, že vydavatel použije tento token a požádá o jeho vyřešení.  Při použití různých rozhraní API plnění je parametr dotazu tokenu v adrese URL, když je uživatel přesměrován na web SaaS z Azure.  Tento token je platný pouze po dobu jedné hodiny.  Kromě toho byste měli url dekódovat hodnotu tokenu z prohlížeče před použitím.

Další informace o těchto tokenech najdete [v tématu Tokeny přístupu služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Získání tokenu založeného na aplikaci Azure AD

Metoda HTTP

`POST`

*Adresa URL požadavku*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Parametr URI*

|  **Název parametru**  | **Požadováno**  | **Popis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID klienta registrované aplikace AAD   |
|  |  |  |


*Hlavička požadavku*

|  **Název hlavičky**  | **Požadováno** |  **Popis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Typ obsahu     | True         | Typ obsahu přidružený k požadavku. Výchozí hodnota je `application/x-www-form-urlencoded`.  |
|  |  |  |


*Text požadavku*

| **Název vlastnosti**   | **Požadováno** |  **Popis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ grantu. Výchozí hodnota je `client_credentials`.                    |
|  Client_id          | True         |  Identifikátor klienta nebo aplikace přidružený k aplikaci Azure AD.                  |
|  client_secret      | True         |  Heslo přidružené k aplikaci Azure AD.                               |
|  Prostředek           | True         |  Cílový prostředek, pro který je požadován token. Výchozí hodnota je `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Reakce*

|  **Název**  | **Typ**       |  **Popis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | Odpověď tokenu  | Požadavek byl úspěšný.   |
|  |  |  |

*Odpověď tokenu*

Ukázkový token odpovědi:

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

Vaše aplikace zabezpečená službou Azure AD teď může používat [rozhraní API plnění SaaS verze 2](./pc-saas-fulfillment-api-v2.md).
