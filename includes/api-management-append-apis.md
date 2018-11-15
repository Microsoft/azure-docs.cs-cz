---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571935"
---
## <a name="append-other-apis"></a>Připojení dalších rozhraní API

Rozhraní API se může skládat z rozhraní API vystavovaných různými službami, včetně specifikace OpenAPI, rozhraní API SOAP, funkce API Apps služby Azure App Service, aplikace Azure Function App, Azure Logic Apps a Azure Service Fabric.

![Import rozhraní API](./media/api-management-append-apis/import.png)

Pokud chcete k existujícímu rozhraní API připojit ještě jiné rozhraní API, proveďte následující kroky. Při importu dalšího rozhraní API se k aktuálnímu rozhraní API připojí příslušné operace.

1. Na webu Azure Portal přejděte ke své instanci služby Azure API Management.
2. V nabídce na levé straně vyberte **Rozhraní API**.
3. Klikněte na **…** vedle rozhraní API, ke kterému chcete připojit jiné rozhraní API.
4. V rozevírací nabídce vyberte **Importovat**.
5. Vyberte službu, ze které se má importovat rozhraní API.