---
title: Principy ověřování digitální dvojče API Azure | Dokumentace Microsoftu
description: Digitální dvojče Azure můžete připojit a provést ověření rozhraní API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711051"
---
# <a name="connect-and-authenticate-to-apis"></a>Připojit a provést ověření rozhraní API

Azure digitální dvojče používá Azure Active Directory (Azure AD) k ověřování uživatelů a ochraně aplikací. Azure AD podporuje ověřování pro širokou škálu moderní architektury. Všechny z nich jsou založeny na standardních oborových protokolů OAuth 2.0 nebo OpenID Connect. Kromě toho mohou vývojáři služby Azure AD k vytvoření jednoho tenanta a obchodní aplikace (LOB). Vývojáři taky můžete použít Azure AD k vývoji víceklientských aplikací.

Přehled služby Azure AD, najdete [základy stránky](https://docs.microsoft.com/azure/active-directory/fundamentals/index) podrobní průvodci, koncepty a šablon rychlý start.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Podrobné pokyny a snímky obrazovky najdete v tématu [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Scénáře pěti primární aplikace](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) jsou podporovány službou Azure AD:

* Jednostránkové aplikace (SPA): uživatel potřebuje pro přihlášení k jednostránková aplikace, která je zabezpečena pomocí služby Azure AD.
* Webový prohlížeč na webovou aplikaci: uživatel potřebuje pro přihlášení k webové aplikaci, která je zabezpečena pomocí služby Azure AD.
* Nativní aplikace pro webové rozhraní API: nativní aplikaci, která běží na telefonu, tabletu nebo počítači potřebuje ověřit uživatele k získání zdroje z webového rozhraní API, která je zabezpečena pomocí služby Azure AD.
* Webové aplikace k webovému rozhraní API: webová aplikace je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.
* Proces démon nebo server aplikace webového rozhraní API: aplikace proces démon nebo serverovou aplikaci s žádné webové uživatelské rozhraní je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.

Knihovna ověřování Windows Azure nabízí mnoho způsobů, jak získat tokeny služby Active Directory. Podrobnosti o do knihovny a ukázky kódu, naleznete v tématu [v tomto článku](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Volání digitální dvojče z webového rozhraní API střední vrstvy

Když vývojáři navrhovat řešení digitální dvojče, obvykle vytvoří aplikace střední vrstvy nebo rozhraní API. Aplikace nebo API potom směru server-klient volá rozhraní API pro digitální dvojče. Pro podporu této architektury řešení standardních webových, ujistěte se, že uživatelé první:

1. Ověřování pomocí aplikace střední vrstvy

1. Token OAuth 2.0 On-Behalf-Of je požadován při ověřování

1. Získané token se pak použije k ověření pomocí nebo volání rozhraní API, která dále směru server-klient používá tok On-Behalf-Of

Pokyny ohledně toho, jak orchestrovat tok on-behalf-of, naleznete v tématu [tok OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Také můžete zobrazit ukázky kódu v [volat podřízené webové rozhraní API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Další postup

Nakonfigurovat a otestovat Azure digitální dvojče pomocí tok implicitní grant OAuth 2.0, přečtěte si [nakonfigurovat Postman](./how-to-configure-postman.md).

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).