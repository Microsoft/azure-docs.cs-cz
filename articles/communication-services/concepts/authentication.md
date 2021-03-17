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
ms.openlocfilehash: 83976ed9d6f80b6c785cb84e74a0755472f9579f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561800"
---
# <a name="authenticate-to-azure-communication-services"></a>Ověřování ve službě Azure Communication Services

Všechny interakce klientů s komunikačními službami Azure musí být ověřené. V typické architektuře najdete informace v tématu [Architektura klienta a serveru](./client-and-server-architecture.md), *přístupové klíče* nebo *spravované identity* se používají k ověřování.

Jiný typ ověřování používá *tokeny přístupu uživatele* k ověřování vůči službám, které vyžadují účast na uživateli. Například chat nebo volající služba využívá *tokeny přístupu uživatele* , aby umožnila přidávání uživatelů do vlákna a jejich vzájemná konverzace.

## <a name="authentication-options"></a>Možnosti ověřování:

V následující tabulce jsou uvedeny klientské knihovny služby Azure Communication Services a jejich možnosti ověřování:

| Klientská knihovna    | Možnost ověřování                               |
| ----------------- | ----------------------------------------------------|
| Identita          | Přístupový klíč nebo spravovaná identita                      |
| SMS               | Přístupový klíč nebo spravovaná identita                      |
| Telefonní čísla     | Přístupový klíč nebo spravovaná identita                      |
| Videohovory           | Token přístupu uživatele                                   |
| Chat              | Token přístupu uživatele                                   |

Každá možnost autorizace je stručně popsána níže:

- Ověřování **přístupového klíče** je vhodné pro aplikace služeb běžící v prostředí důvěryhodné služby. Přístupový klíč najdete na portálu Azure Communication Services a aplikace služby ji používá jako přihlašovací údaje k inicializaci odpovídajících klientských knihoven. Podívejte se na příklad použití v [klientské knihovně identity](../quickstarts/access-tokens.md). Vzhledem k tomu, že přístupový klíč je součástí připojovacího řetězce vašeho prostředku, ověřování pomocí připojovacího řetězce je ekvivalentní k ověřování pomocí přístupového klíče.

- **Spravované ověřování identity** poskytuje prvotřídní zabezpečení a snadné použití v dalších možnostech autorizace. Například při použití Azure AD se vyhnete nutnosti ukládat přístupový klíč účtu k vašemu kódu, jak to uděláte v případě autorizace pomocí přístupového klíče. I když můžete nadále používat autorizaci přístupových klíčů k aplikacím komunikačních služeb, Microsoft doporučuje přejít na Azure AD tam, kde je to možné. Pokud chcete nastavit spravovanou identitu, [vytvořte v Azure CLI registrovanou aplikaci](../quickstarts/managed-identity-from-cli.md). Pak se koncový bod a přihlašovací údaje dají použít k ověření klientských knihoven. Podívejte se na příklady použití [spravované identity](../quickstarts/managed-identity.md) .

- **Tokeny přístupu uživatele** jsou generovány pomocí klientské knihovny identity a jsou přidruženy k uživatelům vytvořeným v klientské knihovně identity. Podívejte se na příklad [Vytvoření uživatelů a generování tokenů](../quickstarts/access-tokens.md). Tokeny přístupu uživatele pak slouží k ověřování účastníků přidaných do konverzací v chatu nebo v sadě SDK volání. Další informace najdete v tématu [Přidání chatu do aplikace](../quickstarts/chat/get-started.md). Ověřování pomocí tokenu přístupu uživatele se v porovnání s přístupovými klíči a spravovanými identitami liší v tom, že se používá k ověření uživatele místo zabezpečeného prostředku Azure.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a Správa prostředků](../quickstarts/create-communication-resource.md) 
>  komunikačních služeb [Vytvoření aplikace spravované identity Azure Active Directory z Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Vytvořit tokeny přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
