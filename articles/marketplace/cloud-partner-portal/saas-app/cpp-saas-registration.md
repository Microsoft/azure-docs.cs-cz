---
title: Registrace aplikace SaaS – Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak zaregistrovat aplikaci SaaS pomocí webu Azure portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101587"
---
# <a name="register-a-saas-application"></a>Registrace aplikace SaaS

Tento článek vysvětluje postup při registraci aplikace SaaS využívající Microsoft [webu Azure portal](https://portal.azure.com/).  Po úspěšné registraci se zobrazí token zabezpečení Azure Active Directory (Azure AD), který vám umožní přístup k rozhraním API SaaS splnění.  Další informace o službě Azure AD najdete v tématu [co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Tok ověřování služba služba

Následující diagram znázorňuje tok předplatného nového zákazníka, a pokud se používá tato rozhraní API:

![Nabídky SaaS API toku](./media/saas-offer-publish-api-flow-v1.png)

Azure nepředstavuje jakákoliv omezení u ověřování, které služba SaaS poskytuje koncovým uživatelům. Nicméně s rozhraními API SaaS splnění ověřování pomocí tokenu zabezpečení Azure AD, obvykle získané když si zaregistrujete aplikaci SaaS na webu Azure portal. 


## <a name="register-an-azure-ad-secured-app"></a>Registrace Azure AD zabezpečené aplikace

Všechny aplikace, které chtějí využívat možnosti Azure AD, musí být nejdřív zaregistrované v tenantovi Azure AD. Tento proces registrace zahrnuje poskytuje Azure AD podrobnosti o vaší aplikaci, jako je například adresa URL, kde je umístěn, adresa URL pro odeslání odpovědi po ověření uživatele, identifikátor URI, který identifikuje aplikaci a tak dále.  Registrace nové aplikace pomocí webu Azure portal, postupujte následovně:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Pokud váš účet umožňuje přístup k více účtům, klikněte na požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3.  V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**a klikněte na tlačítko **registrace nové aplikace**.

    ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration-v1.png)

4.  Na stránce pro vytvoření, zadejte vaše aplikace\'s informace o registraci:
    -   **Název**: Zadejte název smysluplné aplikace
    -   **Typ aplikace**: 
        - Vyberte **Nativní** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), které jsou nainstalované místně na zařízení. Toto nastavení se používá pro veřejné [nativní klienty](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth.
        - Vyberte **webová aplikace / rozhraní API** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) a [prostředků nebo rozhraní API aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) , které jsou nainstalovány na zabezpečení serveru. Toto nastavení se používá pro OAuth důvěrné [webových klientů](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) a veřejné [uživatelského agenta – klienti se systémem](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Stejná aplikace může také zpřístupnit klienta i prostředek / rozhraní API.
    -   **Adresa URL přihlašování**: Pro webové aplikace nebo rozhraní API aplikace zadejte základní adresu URL vaší aplikace. Například **http://localhost:31544** může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by pak pomocí této adresy URL pro přihlášení k webové klientské aplikace.
    -   **Identifikátor URI pro přesměrování**: U nativních aplikací zadejte identifikátor URI používá Azure AD k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci, například **http://MyFirstAADApp**.

        ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration-v1-2.png)

        Podívejte se na tomto rychlém startu pro konkrétní příklady webových nebo nativních aplikací na základě nastavení, která jsou k dispozici v *Začínáme* část [příručku pro vývojáře v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Jakmile budete hotovi, klikněte na **Vytvořit**. Azure AD přiřadí jedinečné *ID aplikace* k vaší aplikaci a\'znovu provést do vaší aplikace\'s hlavním registrační stránku. V závislosti na tom, jestli je vaše aplikace webová nebo nativní, jsou k dispozici různé volby pro přidání dalších možností do vaší aplikace.

>[!Note]
>Ve výchozím nastavení je nově zaregistrovaný aplikace nakonfigurována pro povolit pouze uživatele ze stejného tenanta k přihlášení do vaší aplikace.


## <a name="using-the-azure-ad-security-token"></a>Pomocí tokenu zabezpečení Azure AD

Jakmile budete zaregistrováni u vaší aplikace, můžete prostřednictvím kódu programu požádat o token zabezpečení Azure AD.  Vydavatel má používat tento token a požádat o jeho vyřešení.  Při použití různá rozhraní API splnění, je parametr token dotazu v adrese URL, když je uživatel přesměrován na SaaS webu z Azure.  Tento token je platný pouze pro jednu hodinu.  Kromě toho byste měli adresu URL dekódování hodnota tokenu z prohlížeče před jeho použitím.

Další informace o těchto tokenů najdete v tématu [přístupové tokeny služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Získání tokenu podle aplikace Azure AD

Metoda HTTP

`GET`

*URL požadavku*

**https://login.microsoftonline.com/*{ID Tenanta}*/oauth2/token**

*Parametr URI*

|  **Název parametru**  | **Požadováno**  | **Popis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| ID Tenanta             | True          | ID klienta registrované aplikace AAD   |
|  |  |  |


*Hlavička požadavku*

|  **Název hlavičky**  | **Požadováno** |  **Popis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Typ obsahu     | True         | Typ obsahu přidruženého k požadavku. Výchozí hodnota je `application/x-www-form-urlencoded`.  |
|  |  |  |


*Text žádosti*

| **Název vlastnosti**   | **Požadováno** |  **Popis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ udělení oprávnění. Výchozí hodnota je `client_credentials`.                    |
|  Client_id          | True         |  Identifikátor klienta nebo aplikace přidružené k aplikaci Azure AD.                  |
|  client_secret      | True         |  Heslo přidružené k aplikaci Azure AD.                               |
|  Prostředek           | True         |  Cílový prostředek, pro kterou je požadována token. Výchozí hodnota je `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Odpověď*

|  **Název**  | **Typ**       |  **Popis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Požadavek byl úspěšný.   |
|  |  |  |

*TokenResponse*

Ukázkové odpovědi tokenu:

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


## <a name="next-steps"></a>Další postup

Teď můžete použít aplikaci Azure AD zabezpečená [SaaS splnění rozhraní API verze 2](./cpp-saas-fulfillment-api-v2.md).
