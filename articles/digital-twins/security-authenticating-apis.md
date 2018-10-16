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
ms.openlocfilehash: 1d5b1869428cec6bf80b8518485f685e38ad5997
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324056"
---
# <a name="connect-and-authenticate-to-apis"></a>Připojit a provést ověření rozhraní API

Azure digitální dvojče používá Azure Active Directory (Azure AD) k ověřování uživatelů a ochraně aplikací.

Pokud nejste obeznámeni s Azure AD, další informace najdete v [– Příručka pro vývojáře](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-developers-guide). Knihovna ověřování Windows Azure nabízí mnoho způsobů, jak získat tokeny služby Active Directory. Podrobně nyní do knihovny, najdete [tady](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

Tento článek poskytuje přehled o dva scénáře: produkční scénář pomocí rozhraní API střední vrstvy a ověřování v klientské aplikaci Postman pro rychlé spuštění a testování.

## <a name="authentication-in-production"></a>Ověřování v produkčním prostředí

Dva způsoby, jak se připojit k digitální dvojče mají vývojáři řešení.  Vývojáři řešení můžete připojit k Azure digitální dvojče následujícími způsoby:

* Může vytvořit klientskou aplikaci nebo API střední vrstvy. Klientské aplikace vyžadovat, aby uživatelé pro ověření a následné použití [OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) tok zabezpečení volat podřízené rozhraní API.
* Vytvořit nebo použít stávající aplikací Azure AD. Projděte si dokumentaci ke [tady](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad).
    1. Zadejte **přihlašování a identifikátory URI přesměrování** (v případě potřeby).
    1. V aplikaci manifest sady `oauth2AllowImplicitFlow` na hodnotu true.
    1. V **požadovaná oprávnění**, přidejte digitální dvojče tak, že "Azure digitální dvojče." Vyberte **Delegovaný přístup pro čtení/zápis oprávnění** a klikněte na tlačítko **udělit oprávnění** tlačítko.

Podrobné pokyny o tom, jak orchestrovat tok on-behalf-of naleznete [na této stránce](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Můžete také zobrazit ukázky kódu [tady](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="test-with-the-postman-client"></a>Testování s klientem nástroje Postman

1. Počáteční výše uvedených kroků k vytvoření (nebo upravit) aplikaci Azure Active Directory. Potom nastavte `oauth2AllowImplicitFlow` na hodnotu true v manifestu aplikace a udělení oprávnění pro "Digitální Azure Dvojčat."
1. Nastavit adresu url odpovědi [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
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

    >[!TIP]
    >Pokud se zobrazí chybová zpráva "Nebylo možné dokončit OAuth 2", zkuste použijte jeden z následujících akcí:
    > * Postman zavřete a znovu ho otevřete a akci opakujte.
    > * Odstranění tajného klíče ve vaší aplikaci, znovu vytvořit nový a nainstalováno hodnoty ve formuláři výše.

1. Posuňte se dolů a klikněte na tlačítko **použijte Token**.

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
