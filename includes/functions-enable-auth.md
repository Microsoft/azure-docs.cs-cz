---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648951"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Povolit App Service ověřování/autorizaci

Platforma App Service umožňuje používat Azure Active Directory (AAD) a několik zprostředkovatelů identity třetích stran k ověřování klientů. Tuto strategii můžete použít k implementaci vlastních autorizačních pravidel pro vaše funkce a můžete pracovat s uživatelskými informacemi z kódu funkce. Další informace najdete v tématu [ověřování a autorizace v Azure App Service](../articles/app-service/overview-authentication-authorization.md) a [práci s identitami klientů](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Ověřování požadavků pomocí Azure API Management (APIM)

APIM poskytuje celou řadu možností zabezpečení rozhraní API pro příchozí požadavky. Další informace najdete v tématu [zásady ověřování API Management](../articles/api-management/api-management-authentication-policies.md). Pomocí APIM můžete nakonfigurovat aplikaci Function App, aby přijímala požadavky pouze z IP adresy vaší instance APIM. Další informace najdete v tématu [omezení IP adres](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
