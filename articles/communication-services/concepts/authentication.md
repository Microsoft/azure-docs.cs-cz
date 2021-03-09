---
title: Ověřování ve službě Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s různými způsoby, kterými se aplikace nebo služba může ověřit pro komunikační služby.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485957"
---
# <a name="authenticate-to-azure-communication-services"></a>Ověřování ve službě Azure Communication Services

Všechny interakce klientů s komunikačními službami Azure musí být ověřené. V typické architektuře najdete informace v tématu [Architektura klienta a serveru](./client-and-server-architecture.md), *přístupové klíče* nebo *spravovaná identita* se používají ve službě důvěryhodného přístupu uživatele k vytváření uživatelů a vydávání tokenů. A *token přístupu uživatele* , který vystavila služba důvěryhodného přístupu uživatele, se používá pro klientské aplikace pro přístup k jiným komunikačním službám, například k chatu nebo volání služby.

Služba Azure Communications Services SMS pro ověřování také přijímá *přístupové klíče* nebo *spravovanou identitu* . K tomu obvykle dochází v aplikaci služby spuštěné v prostředí důvěryhodné služby.

Každá možnost autorizace je stručně popsána níže:

- **Přístupové ověřování klíčů** pro operace SMS a identity. Ověřování přístupového klíče je vhodné pro aplikace služeb běžící v prostředí důvěryhodné služby. Přístupový klíč najdete na portálu Azure Communication Services. K ověření pomocí přístupového klíče aplikace služby používá přístupový klíč jako přihlašovací údaje k inicializaci odpovídajících klientských knihoven služby SMS nebo identity v tématu [Vytvoření a Správa přístupových tokenů](../quickstarts/access-tokens.md). Vzhledem k tomu, že přístupový klíč je součástí připojovacího řetězce vašeho prostředku, přečtěte si téma [Vytvoření a Správa prostředků komunikačních služeb](../quickstarts/create-communication-resource.md). ověřování pomocí připojovacího řetězce je ekvivalentem ověřování pomocí přístupového klíče.
- **Spravované ověřování identity** pro operace SMS a identity. Spravovaná identita, viz [spravovaná identita](../quickstarts/managed-identity.md), je vhodná pro aplikace služeb běžící v prostředí důvěryhodné služby. Při ověřování pomocí spravované identity vytvoří aplikace služby přihlašovací údaje s ID a tajným kódem spravované identity a potom inicializuje odpovídající klientské knihovny pro SMS nebo identity. Další informace najdete v tématu [Vytvoření a Správa přístupových tokenů](../quickstarts/access-tokens.md).
- Ověřování **uživatelského přístupového tokenu** pro chat a volání. Tokeny přístupu uživatele umožňují vašim klientským aplikacím ověřování pomocí chatu komunikace Azure a volání služeb. Tyto tokeny jsou vygenerovány ve službě důvěryhodného přístupu uživatele, kterou vytvoříte. Pak jsou k dispozici pro klientská zařízení, která používají token k inicializaci chatu a volání klientských knihoven. Další informace najdete v tématu [Přidání chatu do aplikace](../quickstarts/chat/get-started.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a Správa prostředků](../quickstarts/create-communication-resource.md) 
>  komunikačních služeb [Vytvoření aplikace spravované identity Azure Active Directory z Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
