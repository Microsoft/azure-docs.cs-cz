---
title: Principy Azure digitální dvojče API ověřování | Dokumentace Microsoftu
description: Použití Azure digitální dvojče pro připojení a ověření pro rozhraní API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638014"
---
# <a name="connect-and-authenticate-to-apis"></a>Připojit a provést ověření rozhraní API

Azure digitální dvojče používá Azure Active Directory (Azure AD) k ověřování uživatelů a ochraně aplikací. Azure AD podporuje ověřování pro širokou škálu moderní architektury, všechny z nich založeny na standardních oborových protokolů OAuth 2.0 nebo OpenID Connect. Kromě toho Azure AD umožňuje vývojáři, kteří vytvářejí jednoho tenanta, -obchodní aplikace (LOB), jak vývojáři chtějí vyvíjet aplikace s více tenanty.

Přehled služby Azure AD najdete [základy stránky](https://docs.microsoft.com/azure/active-directory/fundamentals/index) podrobní průvodci, koncepty a rychlých startech.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Pro podrobné pokyny a snímky obrazovky zobrazení pokynů [zde](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Jedná se o [pěti primární aplikace scénáře](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) podporuje Azure AD:

* Jednostránkové aplikace (SPA): uživatel potřebuje pro přihlášení k jednostránková aplikace, která je zabezpečena pomocí služby Azure AD.
* Webový prohlížeč na webovou aplikaci: uživatel potřebuje pro přihlášení k webové aplikaci, která je zabezpečena pomocí služby Azure AD.
* Nativní aplikace pro webové rozhraní API: nativní aplikaci, která běží na telefonu, tabletu nebo počítači potřebuje ověřit uživatele k získání zdroje z webového rozhraní API, která je zabezpečena pomocí služby Azure AD.
* Webové aplikace k webovému rozhraní API: webová aplikace je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.
* Proces démon nebo server aplikace webového rozhraní API: aplikace proces démon nebo serverovou aplikaci s žádné webové uživatelské rozhraní je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.

Knihovna ověřování Windows Azure nabízí mnoho způsobů, jak získat tokeny služby Active Directory. Podrobně nyní do knihovny, stejně jako kód ukázky najdete [tady](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Volání digitální dvojče z webového rozhraní API střední vrstvy

Při navrhování běžně vývojáři digitální dvojče řešení můžete vytvořit aplikace střední vrstvy nebo rozhraní API, které pak zavolá rozhraní API pro digitální dvojče směru server-klient. Uživatelé by nejdřív projít ověřením aplikace střední vrstvy a pak on-behalf-of token tok, který se použije při volání metody podřízené. Podrobné pokyny o tom, jak orchestrovat tok on-behalf-of naleznete [na této stránce](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Můžete také zobrazit ukázky kódu [tady](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testování s klientem nástroje Postman

Pokud chcete začít pracovat s rozhraními API sady digitální dvojče slouží jako prostředí rozhraní API klienta, jako je Postman. Postman umožňuje rychle vytvářet složité požadavky HTTP. Podle následujících pokynů se zaměří na tom, jak získat token Azure AD potřebných k volání digitální dvojče v rámci uživatelského rozhraní nástroje Postman.


1. Přejděte na https://www.getpostman.com/ stažení aplikace
1. Postupujte podle kroků [tady](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) k vytvoření aplikace Azure Active Directory (nebo můžete využít existující registrací). 
1. V části Požadovaná oprávnění Přidat "Digitální dvojče Azure" a vyberte delegovaná oprávnění. Nezapomeňte kliknout na udělit oprávnění k dokončení.
1. Otevření manifestu aplikace a oauth2AllowImplicitFlow nastavena na hodnotu true
1. Postup konfigurace adresy url odpovědi na [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Vyberte **kartu autorizace**, klikněte na **OAuth 2.0**a vyberte **získat nový přístupový Token**.

    |**Pole**  |**Hodnota** |
    |---------|---------|
    | Typ udělení | Implicitní |
    | Adresa URL pro zpětné volání | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Ověřovací adresa URL | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | ID klienta | Pomocí Id aplikace pro aplikace Azure AD, který byl vytvořen nebo k jinému účelu z kroku 1 |
    | Rozsah | Ponechte prázdné |
    | Stav | Ponechte prázdné |
    | Ověřování klientů | Odeslat jako hlavičku základního ověřování |

1. Klikněte na tlačítko **požádat o Token**.

    >[!NOTE]
    >Pokud se zobrazí chybová zpráva "Nebylo možné dokončit OAuth 2", zkuste následující:
    > * Postman zavřete a znovu ho otevřete a akci opakujte.
   
1. Posuňte se dolů a klikněte na tlačítko **použijte Token**.

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
