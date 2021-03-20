---
title: Ověřování ve službě Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s různými způsoby, kterými se aplikace nebo služba může ověřit pro komunikační služby.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0146ff9ce3ec4821bee7ce34700ca4198bb23ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598860"
---
# <a name="authenticate-to-azure-communication-services"></a>Ověřování ve službě Azure Communication Services

Všechny interakce klientů s komunikačními službami Azure musí být ověřené. V typické architektuře najdete informace v tématu [Architektura klienta a serveru](./client-and-server-architecture.md), *přístupové klíče* nebo *spravované identity* se používají k ověřování.

Jiný typ ověřování používá *tokeny přístupu uživatele* k ověřování vůči službám, které vyžadují účast na uživateli. Například chat nebo volající služba využívá *tokeny přístupu uživatele* , aby umožnila přidávání uživatelů do vlákna a jejich vzájemná konverzace.

## <a name="authentication-options"></a>Možnosti ověřování

V následující tabulce jsou uvedeny klientské knihovny služby Azure Communication Services a jejich možnosti ověřování:

| Klientská knihovna    | Možnost ověřování                               |
| ----------------- | ----------------------------------------------------|
| Identita          | Přístupový klíč nebo spravovaná identita                      |
| SMS               | Přístupový klíč nebo spravovaná identita                      |
| Telefonní čísla     | Přístupový klíč nebo spravovaná identita                      |
| Videohovory           | Token přístupu uživatele                                   |
| Chat              | Token přístupu uživatele                                   |

Každá možnost autorizace je stručně popsána níže:

### <a name="access-key"></a>Přístupový klíč

Ověřování přístupového klíče je vhodné pro aplikace služeb běžící v prostředí důvěryhodné služby. Váš přístupový klíč najdete na portálu Azure Communication Services. Aplikace služby ji používá jako přihlašovací údaje k inicializaci odpovídajících klientských knihoven. Podívejte se na příklad použití v [klientské knihovně identity](../quickstarts/access-tokens.md). 

Vzhledem k tomu, že přístupový klíč je součástí připojovacího řetězce vašeho prostředku, ověřování pomocí připojovacího řetězce je ekvivalentní k ověřování pomocí přístupového klíče.

Pokud chcete volat rozhraní API ACS ručně pomocí přístupového klíče, budete muset žádost podepsat. Podepsání žádosti je vysvětleno podrobněji v rámci [kurzu](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Spravovaná identita

Spravované identity poskytují zabezpečení a možnost snadného použití prostřednictvím dalších možností autorizace. Například při použití Azure AD se nemusíte ukládat klíč pro přístup k účtu v rámci kódu, jak je to u přístupu k autorizaci klíčů. I když můžete nadále používat autorizaci přístupových klíčů k aplikacím komunikačních služeb, Microsoft doporučuje přejít na Azure AD tam, kde je to možné. 

Pokud chcete nastavit spravovanou identitu, [vytvořte v Azure CLI registrovanou aplikaci](../quickstarts/managed-identity-from-cli.md). Pak se koncový bod a přihlašovací údaje dají použít k ověření klientských knihoven. Podívejte se na příklady použití [spravované identity](../quickstarts/managed-identity.md) .

### <a name="user-access-tokens"></a>Tokeny přístupu uživatele

Tokeny přístupu uživatele jsou generovány pomocí klientské knihovny identity a jsou přidruženy k uživatelům vytvořeným v klientské knihovně identity. Podívejte se na příklad [Vytvoření uživatelů a generování tokenů](../quickstarts/access-tokens.md). Tokeny přístupu uživatele pak slouží k ověřování účastníků přidaných do konverzací v chatu nebo v sadě SDK volání. Další informace najdete v tématu [Přidání chatu do aplikace](../quickstarts/chat/get-started.md). Ověřování pomocí tokenu přístupu uživatele se v porovnání s přístupovými klíči a spravovanými identitami liší v tom, že se používá k ověření uživatele místo zabezpečeného prostředku Azure.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a Správa prostředků](../quickstarts/create-communication-resource.md) 
>  komunikačních služeb [Vytvoření aplikace spravované identity Azure Active Directory z Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Vytvořit tokeny přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
