---
title: Authentication
description: Vysvětluje, jak ověřování funguje.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: e61767b9b8d904eae9a247f48d2d781fd0c95192
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202714"
---
# <a name="configure-authentication"></a>Konfigurace ověřování

Vzdálené vykreslování Azure používá stejný ověřovací mechanismus jako [prostorové kotvy Azure (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Klienti musí nastavit *jednu* z následujících možností, aby úspěšně volala rozhraní REST API:

* **AccountKey**: lze získat na kartě Keys (klíče) pro účet vzdáleného vykreslování na Azure Portal. Klíče účtu doporučujeme jenom pro vývoj a vytváření prototypů.
    ![Account ID](./media/azure-account-primary-key.png)

* **AuthenticationToken**: je token Azure AD, který se dá získat pomocí [knihovny MSAL](../../active-directory/develop/msal-overview.md). K dispozici je více různých toků pro příjem přihlašovacích údajů uživatele a používání těchto pověření k získání přístupového tokenu.

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

 Ověřování Azure AD je popsané v části [ověřování uživatelů Azure AD](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) ve službě [Azure prostorové kotvy (ASA)](../../spatial-anchors/index.yml) .

 Další informace najdete v tomto [kurzu: zabezpečení vzdáleného vykreslování Azure a modelu úložiště – Azure Active Directory ověřování](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pokud chcete zajistit řízení úrovně přístupu k vaší službě, použijte při udělování přístupu založeného na rolích následující role:

* **Správce vzdáleného vykreslování**: poskytuje uživateli s převodem, správou možností relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure.
* **Klient vzdáleného vykreslování**: poskytuje uživateli možnost spravovat relaci, vykreslování a diagnostické funkce pro vzdálené vykreslování Azure.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)