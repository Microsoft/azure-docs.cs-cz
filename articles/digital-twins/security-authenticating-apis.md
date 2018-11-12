---
title: Principy ověřování digitální dvojče API Azure | Dokumentace Microsoftu
description: Digitální dvojče Azure můžete připojit a provést ověření rozhraní API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016212"
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

Když vývojáři navrhovat řešení digitální dvojče, obvykle vytvoří aplikace střední vrstvy nebo rozhraní API. Aplikace nebo API potom směru server-klient volá rozhraní API pro digitální dvojče. Nejdřív ověřit uživatele pro aplikace střední vrstvy, a potom on-behalf-of token tok, který slouží k volání podřízené. Pokyny ohledně toho, jak orchestrovat tok on-behalf-of, naleznete v tématu [na této stránce](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Můžete také zobrazit ukázky kódu na [na této stránce](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testování s klientem nástroje Postman

Chcete-li začít pracovat s rozhraními API sady digitální dvojče, můžete použít klienta, jako je Postman jako prostředí rozhraní API. Postman umožňuje rychle vytvářet složité požadavky HTTP. Následující kroky ukazují, jak získat token Azure AD, který je nezbytný pro volání digitální dvojče v rámci uživatelského rozhraní nástroje Postman.


1. Přejděte na https://www.getpostman.com/ stažení aplikace.
1. Postupujte podle kroků v [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) k vytvoření aplikace Azure AD. Nebo můžete znovu použít existující registrací. 
1. V části **požadovaná oprávnění**, zadejte "Digitální dvojče Azure" a vyberte **delegovaná oprávnění**. Potom vyberte **udělit oprávnění**.
1. Otevřete manifest aplikace a nastavte **oauth2AllowImplicitFlow** na hodnotu true.
1. Postup konfigurace adresy URL odpovědi na [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Vyberte **autorizace** kartu, vyberte možnost **OAuth 2.0**a pak vyberte **získat nový přístupový Token**.

    |**Pole**  |**Hodnota** |
    |---------|---------|
    | Typ udělení | Implicitní |
    | Adresa URL pro zpětné volání | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Ověřovací adresa URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?Resource=0b07f429-9F4B-4714-9392-cc5e8e80c8b0 |
    | ID klienta | Pomocí ID aplikace pro aplikace Azure AD, který byl vytvořen nebo k jinému účelu z kroku 2. |
    | Rozsah | Ponechte prázdné. |
    | Stav | Ponechte prázdné. |
    | Ověření klienta | Odešlete jako hlavičku základního ověřování. |

1. Vyberte **požádat o Token**.

    >[!NOTE]
    >Pokud se zobrazí chybová zpráva "Nebylo možné dokončit OAuth 2", zkuste následující:
    > * Nástroj Postman, zavřete a znovu ho otevřete a akci opakujte.
   
1. Přejděte dolů a vyberte možnost **použijte Token**.

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
