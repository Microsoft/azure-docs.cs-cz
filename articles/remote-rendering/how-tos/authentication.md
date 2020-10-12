---
title: Authentication
description: Vysvětluje, jak ověřování funguje.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565845"
---
# <a name="configure-authentication"></a>Konfigurace ověřování

Vzdálené vykreslování Azure používá stejný ověřovací mechanismus jako [prostorové kotvy Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienti musí nastavit *jednu* z následujících možností, aby úspěšně volala rozhraní REST API:

* **AccountKey**: lze získat na kartě Keys (klíče) pro účet vzdáleného vykreslování na Azure Portal. Klíče účtu doporučujeme jenom pro vývoj a vytváření prototypů.
    ![Account ID](./media/azure-account-primary-key.png)

* **AuthenticationToken**: je token Azure AD, který se dá získat pomocí [knihovny MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-overview). K dispozici je více různých toků pro příjem přihlašovacích údajů uživatele a používání těchto pověření k získání přístupového tokenu.

* **MRAccessToken**: je token Mr, který se dá získat ze služby STS (Azure Mixed reality Security token). Načteno z `https://sts.mixedreality.azure.com` koncového bodu pomocí volání REST podobného následujícímu volání:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Záhlaví autorizace je formátováno takto: `Bearer <Azure_AD_token>` nebo `Bearer <accoundId>:<accountKey>` . Původní je vhodnější pro zabezpečení. Token vrácený z tohoto volání REST je přístupový token MR.

## <a name="authentication-for-deployed-applications"></a>Ověřování pro nasazené aplikace

Klíče účtu se doporučují pro rychlé vytváření prototypů při vývoji. Doporučujeme, abyste aplikaci nedodali do produkčního prostředí pomocí vloženého klíče účtu. Doporučený postup je použití přístupu k ověřování Azure AD založeného na uživateli nebo službě.

 Ověřování Azure AD je popsané v části [ověřování uživatelů Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) ve službě [Azure prostorové kotvy (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

 Další informace najdete v tomto [kurzu: zabezpečení vzdáleného vykreslování Azure a modelu úložiště – Azure Active Directory ověřování](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pokud chcete zajistit řízení úrovně přístupu k vaší službě, použijte při udělování přístupu založeného na rolích následující role:

* **Správce vzdáleného vykreslování**: poskytuje uživateli s převodem, správou možností relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure.
* **Klient vzdáleného vykreslování**: poskytuje uživateli možnost spravovat relaci, vykreslování a diagnostické funkce pro vzdálené vykreslování Azure.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
